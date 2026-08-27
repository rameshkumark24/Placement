# Phase 05 — API Safety, Loops & Cost Control

Mobile amplifies loop bugs. Flaky networks trigger retries, users background and foreground the app
constantly, and — critically — **you cannot patch a released client quickly.** A loop bug that ships
keeps billing you until users update, and some never will.

**Gate to pass this phase:** the audit returns yes on all ten, and every endpoint has a
server-side rate limit.

---

## The ten-question audit

| # | Question | If no |
|---|---|---|
| 1 | Is every API call outside the render body / `build()`? | [§1]() |
| 2 | Does every fetching effect have a correct dependency array? | [§1]() |
| 3 | Are focus/resume refetches time-gated? | [§2](#2-the-focus-refetch-loop) |
| 4 | Is every listener removed on unmount? | [§3](#3-the-listener-leak) |
| 5 | Is every `setInterval` cleared, and does polling stop when backgrounded? | [§4](#4-polling--background-behaviour) |
| 6 | Are in-flight requests cancelled on unmount? | [§5](#5-request-cancellation--timeouts) |
| 7 | Do retries have a cap, backoff **and jitter**? | [§6](#6-retry-storms--the-tunnel-problem) |
| 8 | Is there a server-side rate limit per user and per IP? | [§7](#7-rate-limiting-with-upstash) |
| 9 | Are creates and payments idempotent? | [§8](#8-idempotency) |
| 10 | Is there a hard spend cap with an alert below it? | [§9](#9-spend-caps) |

---

## 1. The render loop

### Flutter — the worst version

```dart
// 💀 CATASTROPHIC — build() runs on every frame. This is an infinite API loop.
@override
Widget build(BuildContext context) {
  fetchData();                       // NEVER call an API from build()
  return Scaffold(...);
}

// ✅ Fetch once; store the Future
class _MyScreenState extends State<MyScreen> {
  late final Future<Data> _future;

  @override
  void initState() {
    super.initState();
    _future = fetchData();           // created once, not on every rebuild
  }

  @override
  Widget build(BuildContext context) =>
      FutureBuilder(future: _future, builder: ...);   // not fetchData() inline
}
```

### React Native

```jsx
// 💀 No dependency array — fetches on every render, forever
useEffect(() => { fetch('/api/items').then(r => r.json()).then(setItems); });

// ✅ Primitive deps, cancelled on unmount
useEffect(() => {
  const controller = new AbortController();
  fetch(`/api/items?page=${page}`, { signal: controller.signal })
    .then(r => r.json()).then(setItems)
    .catch(e => { if (e.name !== 'AbortError') setError(e); });
  return () => controller.abort();
}, [page]);

// ✅ BEST — dedupes, caches, bounded retries
const { data } = useQuery({
  queryKey: ['items', page],
  queryFn: () => api.getItems(page),
  staleTime: 60_000,
  retry: 2,
});
```

- [ ] `react-hooks/exhaustive-deps` set to **error**
- [ ] **TanStack Query for all server state** — removes this entire bug class
- [ ] Deps are primitives; objects memoised

---

## 2. The focus-refetch loop

Mobile-specific, and very common. Every back-navigation re-triggers the fetch.

```jsx
// 💀 Refetches on every single focus, including every back-navigation
useFocusEffect(() => { fetchData(); });

// ✅ Time-gated
useFocusEffect(
  useCallback(() => {
    if (Date.now() - lastFetch > 60_000) fetchData();
  }, [lastFetch])
);

// ✅ Or let the query layer decide
useFocusEffect(
  useCallback(() => { queryClient.invalidateQueries({ queryKey: ['items'] }); }, [])
);
// with staleTime set, this is a no-op when the data is fresh
```

Also: **set `refetchOnWindowFocus: false`** in TanStack Query on mobile. On web it fires when you
switch tabs; on mobile it fires constantly.

---

## 3. The listener leak

```jsx
// 💀 A new listener on every render, never removed
useEffect(() => {
  AppState.addEventListener('change', refetchEverything);
});

// ✅ One listener, removed on unmount
useEffect(() => {
  const sub = AppState.addEventListener('change', handleChange);
  return () => sub.remove();
}, []);
```

Applies to **every** listener: `AppState`, NetInfo, keyboard, location, notifications, Supabase
realtime subscriptions, deep links. Each leaked listener multiplies every subsequent event.

- [ ] Every `addEventListener` has a matching removal in the cleanup
- [ ] Supabase realtime channels unsubscribed on unmount
- [ ] Location watchers stopped when the screen closes — this is also a battery killer

---

## 4. Polling & background behaviour

```jsx
// ✅ Polls only while foregrounded
useEffect(() => {
  if (appState !== 'active') return;
  const id = setInterval(refetch, 30_000);
  return () => clearInterval(id);
}, [appState]);
```

- [ ] Every `setInterval` has a matching `clearInterval`
- [ ] **Polling stops when the app is backgrounded** — draining battery in the background is how
      you get uninstalled
- [ ] Poll no faster than the data changes
- [ ] Stop after N consecutive failures
- [ ] Background sync uses the OS scheduler (WorkManager / BGTaskScheduler), never a timer
- [ ] Real-time needs use Supabase Realtime, not a 1-second poll

---

## 5. Request cancellation & timeouts

```ts
useEffect(() => {
  const controller = new AbortController();
  api.getItems({ signal: controller.signal });
  return () => controller.abort();
}, []);
```

- [ ] Requests cancelled on unmount (`AbortController` / Dio `CancelToken`)
- [ ] **Timeout on every call** — mobile networks hang rather than fail cleanly; without a timeout
      a request can wait indefinitely with a spinner on screen
- [ ] Superseded searches cancelled

---

## 6. Retry storms — the tunnel problem

When a train exits a tunnel, **thousands of your users reconnect simultaneously**. Without jitter,
they all retry in lockstep and spike your backend at the same millisecond.

```ts
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      retry: 2,
      retryDelay: attempt =>
        Math.min(1000 * 2 ** attempt, 10_000) + Math.random() * 300,   // ← jitter
      networkMode: 'offlineFirst',
    },
    mutations: { retry: 3, networkMode: 'offlineFirst' },
  },
});
```

- [ ] Max 3 attempts
- [ ] Exponential backoff **with jitter** — non-negotiable on mobile
- [ ] **Never retry 4xx** except 429
- [ ] Honour `Retry-After`
- [ ] Circuit breaker: after N consecutive failures, stop and serve cached data
- [ ] Offline queue replays are **staggered**, not fired all at once on reconnect

---

## 7. Rate limiting with Upstash

More important on mobile than web: **a buggy released client cannot be patched quickly.** The server
limit is your only defence until users update.

```ts
const { success, reset } = await limiter.limit(userId ?? `ip:${ip}`);
if (!success) {
  return new Response('Too many requests', {
    status: 429,
    headers: { 'Retry-After': String(Math.ceil((reset - Date.now()) / 1000)) },
  });
}
```

- [ ] Per authenticated user **and** per IP
- [ ] Tighter on auth, search, upload, AI endpoints
- [ ] `429` with `Retry-After`, and the client honours it
- [ ] Cloudflare rate limiting as a second layer
- [ ] **Server-side limits assume a buggy client** — design them as if a loop bug is already live

---

## 8. Idempotency

Mobile networks fail mid-request constantly, so retries are routine, so double-creates are routine.

- [ ] **Client generates a UUID per logical operation** — this also gives offline writes an ID
      before they reach the server
- [ ] Server stores the key with its result; a repeat returns the stored result
- [ ] Payment charges use idempotency keys
- [ ] Webhook handlers idempotent — store processed event IDs
- [ ] DB unique constraint as the last line of defence
- [ ] **Submit buttons disabled while in flight** — kills most double-charges
- [ ] Offline queue replay is idempotent by construction

---

## 9. Spend caps

- [ ] Hard limit on every paid service: Supabase, Upstash, Sentry, Clerk, EAS, any LLM API
- [ ] Alerts at 50% and 80%
- [ ] Per-user quotas in your code
- [ ] **Separate dev and prod keys**, dev on the smallest tier
- [ ] Expensive responses cached
- [ ] **Bill watched daily for the first week** after release

> Upstash bills per request and Sentry per event. A client-side loop hits your API bill, your Redis
> bill and your error quota simultaneously — and it's shipped, so you can't stop it without an OTA
> update.

---

## 10. LLM & agent loops

- [ ] **`MAX_STEPS` constant**, enforced in code, never decided by the model
- [ ] `max_tokens` on every call
- [ ] Per-user daily token quota
- [ ] Timeout on the whole run
- [ ] Streaming cancelled when the screen unmounts — otherwise you pay for tokens nobody sees
- [ ] All model calls proxied through **your backend** — never put a provider key in the app
- [ ] Model output never executed; treated as untrusted input
- [ ] Prompt injection handled — user content delimited
- [ ] **Pinecone/RAG retrieval filtered by the requesting user's ID in the query**

---

## 11. Data & battery — the mobile-only cost

Your loop bug costs the **user** money too, on a metered connection.

- [ ] Images correctly sized server-side; never download a 4000px image for a 100px thumbnail
- [ ] `expo-image` caching, not a raw fetch per render
- [ ] Delta sync, not full re-downloads ([Phase 03](03-Architecture-and-Data.md#delta-sync))
- [ ] Large downloads deferred to Wi-Fi where sensible
- [ ] Location updates use the coarsest accuracy that works, and stop when the screen closes
- [ ] Battery drain measured over 30 minutes of normal use

---

## Phase gate

- [ ] All ten audit questions answered yes
- [ ] `exhaustive-deps` set to error
- [ ] TanStack Query configured with jitter and `refetchOnWindowFocus: false`
- [ ] Every listener cleanup verified
- [ ] Upstash rate limits live on every endpoint
- [ ] Idempotency in place for creates and payments
- [ ] Spend caps and alerts set
