# Phase 05 — API Safety, Loops & Cost Control

The most expensive vibe-coding failure is not a data leak — it's a loop. An agent writes a
`useEffect` without a dependency array, you deploy on Friday, and by Monday you have 40 million
requests and a bill you can't pay.

**Every API call needs three ceilings: a loop guard, a retry cap, and a spend cap.**

**Gate to pass this phase:** the audit below returns yes on all nine, and every public endpoint has
an Upstash rate limit.

---

## The nine-question audit

Run this against any AI-generated diff.

| # | Question | If no |
|---|---|---|
| 1 | Does every `useEffect` that fetches have a correct dependency array? | [§1](#1-the-render-loop) |
| 2 | Does every state update inside a fetch avoid re-triggering that fetch? | [§1](#1-the-render-loop) |
| 3 | Is every `setInterval` / polling loop cleared on unmount? | [§2](#2-the-polling-leak) |
| 4 | Is every in-flight request cancelled on unmount? | [§3](#3-the-unmount-leak) |
| 5 | Do retries have a maximum count and backoff with jitter? | [§4](#4-retry-storms) |
| 6 | Are search / autocomplete inputs debounced? | [§5](#5-the-keystroke-storm) |
| 7 | Is there a server-side rate limit per user **and** per IP? | [§6](#6-rate-limiting-with-upstash) |
| 8 | Are payment operations idempotent? | [§7](#7-idempotency--the-double-charge-killer) |
| 9 | Is there a hard spend cap with an alert below it? | [§8](#8-spend-caps--billing-alerts) |

---

## 1. The render loop

**The single most common cause of runaway bills in AI-generated React.**

```jsx
// 💀 No dependency array — fetches on EVERY render.
// setItems triggers a re-render, which triggers the fetch. Forever.
useEffect(() => {
  fetch('/api/items').then(r => r.json()).then(setItems);
});

// 💀 Object literal in deps — a new reference every render, so it never "matches"
useEffect(() => { fetchData(filters); }, [{ page, size }]);
```

```jsx
// ✅ Primitive deps, cancelled on unmount
useEffect(() => {
  const controller = new AbortController();
  fetch(`/api/items?page=${page}&size=${size}`, { signal: controller.signal })
    .then(r => r.json())
    .then(setItems)
    .catch(e => { if (e.name !== 'AbortError') setError(e); });
  return () => controller.abort();
}, [page, size]);
```

```jsx
// ✅ BEST — dedupes, caches, bounded retries, no manual effect at all
const { data } = useQuery({
  queryKey: ['items', page, size],
  queryFn: () => fetch(`/api/items?page=${page}&size=${size}`).then(r => r.json()),
  staleTime: 60_000,
  retry: 2,
});
```

**Guardrails**

- `react-hooks/exhaustive-deps` set to **error**, not warning
- Deps must be primitives; memoise objects with `useMemo`
- **Use TanStack Query for all server state** — it removes this entire class of bug
- Never call `setState` with a newly-constructed object inside a fetch effect

**Dev canary — add this during development:**

```js
if (process.env.NODE_ENV === 'development') {
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
// 💀 A new interval every render, none ever cleared
useEffect(() => { setInterval(() => fetch('/api/status'), 1000); });

// ✅ One interval, cleared on unmount, sane cadence
useEffect(() => {
  const id = setInterval(() => fetch('/api/status'), 30_000);
  return () => clearInterval(id);
}, []);
```

- Poll no faster than the data actually changes — 1s polling is almost never justified
- Stop when the tab is hidden (`document.visibilityState !== 'visible'`)
- Stop after N consecutive failures — don't hammer a dead endpoint
- For real-time, use Supabase Realtime or SSE, not a 1-second poll

---

## 3. The unmount leak

```jsx
useEffect(() => {
  const controller = new AbortController();
  fetch('/api/items', { signal: controller.signal }).then(/* … */);
  return () => controller.abort();
}, []);
```

Also set a timeout on **every** outbound call, server-side included — a hung request holds a
serverless function open and you pay for the wall-clock time.

```ts
const res = await fetch(url, { signal: AbortSignal.timeout(10_000) });
```

---

## 4. Retry storms

Unbounded retries turn one failing dependency into a self-inflicted DDoS, guaranteeing it never
recovers.

```ts
async function fetchWithRetry(url: string, opts: RequestInit = {}, maxAttempts = 3) {
  let lastErr: unknown;
  for (let attempt = 0; attempt < maxAttempts; attempt++) {
    try {
      const res = await fetch(url, { ...opts, signal: AbortSignal.timeout(10_000) });
      // Never retry client errors — they fail identically forever
      if (res.status >= 400 && res.status < 500 && res.status !== 429) return res;
      if (res.ok) return res;
      lastErr = new Error(`HTTP ${res.status}`);
    } catch (e) { lastErr = e; }
    const backoff = Math.min(2 ** attempt * 1000, 10_000);
    const jitter = Math.random() * 300;          // prevents thundering herd
    await new Promise(r => setTimeout(r, backoff + jitter));
  }
  throw lastErr;
}
```

- Max 3 attempts user-facing, 5 for background jobs
- **Never retry 4xx** except `429`
- Honour `Retry-After` when present
- Always jitter, or every client retries in lockstep and spikes you again
- Circuit breaker: after N consecutive failures, stop calling for a cooldown and serve a
  cached/degraded response

---

## 5. The keystroke storm

```jsx
// 💀 One request per keystroke — "laptop" = 6 requests
<input onChange={e => search(e.target.value)} />

// ✅ Debounced, minimum length, superseded searches cancelled
const [term, setTerm] = useState('');
const debounced = useDebounce(term, 400);

useEffect(() => {
  if (debounced.length < 2) return;
  const controller = new AbortController();
  search(debounced, controller.signal);
  return () => controller.abort();
}, [debounced]);
```

- Debounce search 300–500ms; throttle scroll/resize
- Minimum query length before hitting the server
- **Disable submit buttons while a request is in flight** — this alone kills most double-charges

---

## 6. Rate limiting with Upstash

Client-side limits are a courtesy. The server limit protects you, because it also stops a hostile
client.

```ts
import { Ratelimit } from '@upstash/ratelimit';
import { Redis } from '@upstash/redis';

const limiter = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(10, '60 s'),
  prefix: 'rl:search',
  analytics: true,
});

export async function POST(req: Request) {
  const { userId } = await auth();
  const ip = req.headers.get('x-forwarded-for') ?? 'anon';
  const key = userId ?? `ip:${ip}`;             // per user, falling back to per IP

  const { success, reset } = await limiter.limit(key);
  if (!success) {
    return new Response('Too many requests', {
      status: 429,
      headers: { 'Retry-After': String(Math.ceil((reset - Date.now()) / 1000)) },
    });
  }
  // …
}
```

- [ ] Limit per **authenticated user** and per **IP** — attackers rotate IPs, buggy clients don't
- [ ] Tighter limits on auth, search, upload, email and any AI endpoint
- [ ] Return `429` with `Retry-After`
- [ ] Cloudflare rate limiting rules as a second layer at the edge
- [ ] Starting points: auth `5/min` · writes `30/min` · reads `100/min` · AI `10/min`

---

## 7. Idempotency — the double-charge killer

```ts
// ✅ Stripe idempotency key — a network retry cannot double-charge
await stripe.paymentIntents.create(
  { amount, currency: 'inr', customer },
  { idempotencyKey: `order_${orderId}` },       // stable, derived from your own data
);
```

- Client generates a UUID per logical operation, sends it as `Idempotency-Key`
- Server stores the key with its result (Upstash Redis, short TTL); a repeat key returns the
  **stored** result and does not re-execute
- **Webhook handlers must be idempotent** — Stripe retries, and will deliver the same event twice.
  Store processed event IDs and ignore repeats.
- DB unique constraint as the last line of defence (`unique (user_id, order_ref)`)

---

## 8. Spend caps & billing alerts

Day one, not after the first bill.

- [ ] Hard spend limit on **every** paid service — Vercel, Supabase, Upstash, Sentry, OpenAI/Anthropic
- [ ] Alerts at **50% and 80%** of the ceiling, routed somewhere you read
- [ ] Per-user quotas enforced in your code — the vendor cap protects your wallet, not fairness
- [ ] **Separate keys for dev and prod**, dev on the smallest tier, so a dev-loop bug can't drain
      the production budget
- [ ] Expensive responses cached (LLM output, geocoding, third-party lookups) keyed on input hash
- [ ] Cost-per-request logged for the top 3 expensive endpoints
- [ ] **Watch the bill daily for the first week** after launch

> Upstash bills per request and Sentry per event. A render loop hits all three of your request
> bill, your Redis bill and your error quota simultaneously.

---

## 9. LLM & agent loops

If the app calls a model, loop risk multiplies — an agent can call a tool that calls the agent.

- [ ] **Hard iteration cap** (`MAX_STEPS = 10`), enforced in code, never decided by the model
- [ ] `max_tokens` set on every call
- [ ] Per-user daily token quota
- [ ] Timeout on the whole agent run, not just individual calls
- [ ] Streaming cancelled when the client disconnects — otherwise you pay for unread tokens
- [ ] Prompt caching on long stable system prompts (often 5–10× cheaper)
- [ ] Identical prompts cached by input hash
- [ ] Model output treated as **untrusted input**: never `eval`, never shell, never SQL interpolation
- [ ] Prompt injection handled — user content delimited, system prompt states it is data not commands
- [ ] Pinecone/RAG retrieval filtered by the requesting user's ID **in the query**

```ts
const MAX_STEPS = 10;
for (let step = 0; step < MAX_STEPS; step++) {
  const result = await model.call(messages, { tools, max_tokens: 4000 });
  if (result.stopReason !== 'tool_use') return result;
  messages.push(await runTool(result));
}
throw new Error(`Agent did not converge in ${MAX_STEPS} steps`);
```

---

## 10. Database-side loops

- [ ] **N+1 queries** eliminated — one query for the list, then one per row, is the classic
      AI-generated bug
- [ ] Every list endpoint has a **server-enforced** maximum page size (client asks 10,000, server caps 100)
- [ ] No unbounded `select('*')` on a growing table
- [ ] Connection pooling on (Supabase port 6543)
- [ ] Postgres triggers don't cascade into each other
- [ ] Background job retries capped, with a dead-letter queue
- [ ] Supabase Realtime subscriptions unsubscribed on unmount

---

## Phase gate

- [ ] All nine audit questions answered yes
- [ ] `exhaustive-deps` set to error
- [ ] TanStack Query used for server state
- [ ] Upstash rate limit on every public endpoint
- [ ] Stripe idempotency keys in place
- [ ] Spend caps and alerts set on every service
