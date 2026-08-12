# 🔁 API Safety, Loop Prevention & Cost Control

The single most expensive vibe-coding failure is not a data leak — it's a loop. An AI agent writes
a `useEffect` without a dependency array, you deploy on Friday, and by Monday you have 40 million
requests and a bill you can't pay.

**Rule: every API call needs three ceilings — a loop guard, a retry cap, and a spend cap.**

---

## Quick audit — run this on any AI-generated code

| # | Question | If the answer is no |
|---|---|---|
| 1 | Does every `useEffect`/`watch` that fetches have a correct dependency array? | [§1](#1-the-render-loop) |
| 2 | Does every state update inside a fetch avoid re-triggering that same fetch? | [§1](#1-the-render-loop) |
| 3 | Is every `setInterval` / polling loop cleared on unmount? | [§2](#2-the-polling-leak) |
| 4 | Is every in-flight request cancelled when the component unmounts? | [§3](#3-the-unmount-leak) |
| 5 | Do retries have a **maximum count** and exponential backoff with jitter? | [§4](#4-retry-storms) |
| 6 | Are search / autocomplete inputs debounced? | [§5](#5-the-keystroke-storm) |
| 7 | Is there a server-side rate limit per user **and** per IP? | [§6](#6-server-side-rate-limiting) |
| 8 | Is there a hard spend cap with an alert *below* it? | [§8](#8-spend-caps--billing-alerts) |
| 9 | Can any agent/LLM call recurse into itself without a depth limit? | [§9](#9-llm--agent-specific-loops) |

---

## 1. The render loop

**The #1 cause of runaway API bills in AI-generated React/Next code.**

```jsx
// 💀 CATASTROPHIC — no dependency array. Fetches on EVERY render.
// The setState triggers a re-render, which triggers the fetch, forever.
useEffect(() => {
  fetch('/api/items').then(r => r.json()).then(setItems);
});

// 💀 ALSO CATASTROPHIC — object/array literal in deps is a new
// reference on every render, so the array never "matches".
useEffect(() => {
  fetchData(filters);
}, [{ page, size }]);        // new object every render
```

```jsx
// ✅ CORRECT — primitive deps, runs only when they actually change
useEffect(() => {
  let cancelled = false;
  fetch(`/api/items?page=${page}&size=${size}`)
    .then(r => r.json())
    .then(data => { if (!cancelled) setItems(data); });
  return () => { cancelled = true; };
}, [page, size]);
```

**Guardrails**

- Enable `react-hooks/exhaustive-deps` as an **error**, not a warning.
- Deps must be primitives (string, number, boolean). Memoise objects with `useMemo`.
- Never call `setState` with a *new object* derived from the same data inside a fetch effect —
  it re-triggers anything depending on that state.
- Prefer a data library that dedupes for you: **TanStack Query**, **SWR**, or RTK Query. They
  cache by key, collapse duplicate in-flight requests, and give you retry limits for free.

```jsx
// ✅ BEST — dedupes, caches, bounded retries, no manual effect at all
const { data } = useQuery({
  queryKey: ['items', page, size],
  queryFn: () => fetch(`/api/items?page=${page}&size=${size}`).then(r => r.json()),
  staleTime: 60_000,
  retry: 2,                       // NOT the default infinite-ish behaviour
});
```

**Dev-time canary — put this in your app during development:**

```js
// Screams in the console if any endpoint is hit absurdly often.
if (import.meta.env.DEV) {
  const counts = new Map();
  const realFetch = window.fetch;
  window.fetch = (...args) => {
    const url = String(args[0]).split('?')[0];
    const n = (counts.get(url) ?? 0) + 1;
    counts.set(url, n);
    if (n % 50 === 0) console.error(`🚨 ${url} called ${n} times — probable loop`);
    return realFetch(...args);
  };
}
```

---

## 2. The polling leak

```jsx
// 💀 A new interval every render; none are ever cleared.
useEffect(() => {
  setInterval(() => fetch('/api/status'), 1000);
});

// ✅ One interval, cleared on unmount, sane cadence
useEffect(() => {
  const id = setInterval(() => fetch('/api/status'), 30_000);
  return () => clearInterval(id);
}, []);
```

- Poll no faster than the data actually changes. 1s polling is almost never justified.
- Stop polling when the tab is hidden: `document.visibilityState !== 'visible'`.
- Stop polling after N consecutive failures — don't hammer a dead endpoint.
- If you need real-time, use **SSE or WebSockets**, not a 1-second poll.

---

## 3. The unmount leak

Every request that outlives its component wastes money and can set state on a dead component.

```jsx
useEffect(() => {
  const controller = new AbortController();
  fetch('/api/items', { signal: controller.signal })
    .then(r => r.json())
    .then(setItems)
    .catch(e => { if (e.name !== 'AbortError') setError(e); });
  return () => controller.abort();
}, []);
```

Also set a **timeout on every outbound call** — server-side too. A hung request holds a connection
open, and in serverless you pay for that wall-clock time.

```js
const res = await fetch(url, { signal: AbortSignal.timeout(10_000) });
```

---

## 4. Retry storms

A retry without a cap turns one failing dependency into a self-inflicted DDoS. When your service is
already struggling, unbounded retries guarantee it never recovers.

```js
// ✅ Bounded, exponential, jittered
async function fetchWithRetry(url, opts = {}, maxAttempts = 3) {
  let lastErr;
  for (let attempt = 0; attempt < maxAttempts; attempt++) {
    try {
      const res = await fetch(url, { ...opts, signal: AbortSignal.timeout(10_000) });
      // Never retry client errors — they will fail identically forever.
      if (res.status >= 400 && res.status < 500 && res.status !== 429) return res;
      if (res.ok) return res;
      lastErr = new Error(`HTTP ${res.status}`);
    } catch (e) {
      lastErr = e;
    }
    const backoff = Math.min(2 ** attempt * 1000, 10_000);
    const jitter = Math.random() * 300;          // prevents thundering herd
    await new Promise(r => setTimeout(r, backoff + jitter));
  }
  throw lastErr;
}
```

**Rules**

- Max 3 attempts for user-facing calls; max 5 for background jobs.
- **Never retry 4xx** (except `429`) — the request is malformed, it will fail identically.
- Honour the `Retry-After` header when present.
- Always add jitter, or every client retries in lockstep and spikes you again.
- Add a **circuit breaker**: after N consecutive failures, stop calling for a cooldown period and
  fail fast with a cached/degraded response.

---

## 5. The keystroke storm

```jsx
// 💀 One request per keystroke — "laptop" = 6 requests
<input onChange={e => search(e.target.value)} />

// ✅ Debounced: one request 400ms after typing stops
const [term, setTerm] = useState('');
const debounced = useDeferredValue(term);           // or a useDebounce hook

useEffect(() => {
  if (debounced.length < 2) return;                 // don't search 1 character
  const controller = new AbortController();
  search(debounced, controller.signal);
  return () => controller.abort();                  // cancel superseded searches
}, [debounced]);
```

- **Debounce** (wait for a pause) for search boxes. 300–500ms.
- **Throttle** (at most once per N ms) for scroll and resize handlers.
- Enforce a minimum query length before hitting the server.
- Disable submit buttons while a request is in flight — this alone kills most double-charges.

---

## 6. Server-side rate limiting

Client-side limits are a courtesy. The server limit is the one that protects you, because it also
stops a hostile client.

- Rate limit **per authenticated user** and **per IP** — an attacker rotates IPs, a buggy client doesn't.
- Tighter limits on expensive endpoints: auth, search, file upload, anything calling an LLM.
- Return `429` with a `Retry-After` header.
- Suggested starting points: auth `5/min`, writes `30/min`, reads `100/min`, LLM calls `10/min`.

```ts
// Upstash example — works on serverless/edge
import { Ratelimit } from '@upstash/ratelimit';
const limiter = new Ratelimit({
  redis,
  limiter: Ratelimit.slidingWindow(10, '60 s'),
  prefix: 'llm',
});
const { success } = await limiter.limit(`user:${userId}`);
if (!success) return new Response('Too many requests', { status: 429 });
```

---

## 7. Idempotency — the double-charge killer

Any create/payment operation that can be retried **must** be idempotent, or a network blip charges
your customer twice.

- Client generates a UUID per logical operation and sends it as `Idempotency-Key`.
- Server stores the key with the result; a repeat key returns the **stored** result, does not re-execute.
- Stripe and Razorpay both support this natively — use it.
- Add a DB unique constraint as the last line of defence (e.g. unique on `(user_id, order_ref)`).

---

## 8. Spend caps & billing alerts

Do this on **day one**, not after the first bill.

- [ ] Hard spend limit set on every paid API (OpenAI/Anthropic usage limits, AWS Budgets, Vercel spend cap)
- [ ] Alert at **50% and 80%** of the ceiling, routed to a channel you actually read
- [ ] Per-user quota enforced in *your* code — the vendor cap protects your wallet, not your fairness
- [ ] Separate API keys for dev and prod, so a dev-loop bug can't drain the prod budget
- [ ] Dev keys on the smallest possible tier/limit
- [ ] Cache expensive responses (LLM outputs, geocoding, third-party lookups) keyed on input hash
- [ ] Log cost-per-request for the top 3 expensive endpoints so you can see drift
- [ ] Watch the bill **daily for the first week** after launch

---

## 9. LLM & agent-specific loops

If your app calls an LLM, the loop risk multiplies — an agent can call a tool that calls the agent.

- [ ] **Hard iteration cap** on any agent loop (e.g. max 10 tool calls per request), enforced in code
- [ ] Max token limit per request *and* per user per day
- [ ] Recursion depth limit if a tool can trigger another model call
- [ ] Timeout on the whole agent run, not just individual calls
- [ ] Streaming responses cancelled when the client disconnects — otherwise you pay for tokens
      nobody receives
- [ ] Never let the model decide its own retry count
- [ ] Treat model output as **untrusted input**: never `eval()` it, never run it as a shell command,
      never interpolate it into SQL
- [ ] Prompt-injection handling: user content goes in a clearly delimited block, and instructions
      in the system prompt state that content within it is data, not commands
- [ ] Cache identical prompts — same input, same output, zero cost

```python
# ✅ Bounded agent loop
MAX_STEPS = 10
for step in range(MAX_STEPS):
    result = model.call(messages, tools=tools)
    if result.stop_reason != "tool_use":
        break
    messages.append(run_tool(result))
else:
    raise RuntimeError(f"Agent did not converge in {MAX_STEPS} steps")
```

---

## 10. Backend & database loops

- [ ] **N+1 queries** — the classic AI-generated bug. One query for the list, then one per row.
      Fix with a join or an eager-load (`select_related` / `JOIN FETCH` / `include`).
- [ ] Every list endpoint paginated with a **maximum** page size the server enforces
      (client asks for 10,000, server caps at 100)
- [ ] No unbounded `SELECT *` on a table that grows
- [ ] Connection pooling configured — serverless functions exhaust Postgres connections fast
- [ ] Recursive CTEs have a depth limit
- [ ] Webhook handlers are idempotent and never call back into the endpoint that triggered them
- [ ] Database triggers don't cascade into each other (trigger A updates table B, whose trigger
      updates table A…)
- [ ] Background job retries capped, with a dead-letter queue for permanent failures

---

## Drop this into your `CLAUDE.md`

```md
## API call rules — non-negotiable
- Every useEffect that fetches MUST have a correct primitive dependency array.
- Use TanStack Query for all server state. Do not hand-roll fetch-in-useEffect.
- Every fetch gets an AbortController and a 10s timeout.
- Retries: max 3, exponential backoff with jitter, never retry 4xx except 429.
- Search inputs debounced at 400ms, minimum 2 characters.
- Every new endpoint gets a server-side rate limit before it is merged.
- Any agent/LLM loop has an explicit MAX_STEPS constant.
- Never introduce setInterval without a matching clearInterval in the cleanup.
```
