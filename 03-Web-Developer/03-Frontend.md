# 🖥️ Frontend — the rules that stop the expensive bugs

> ⭐⭐ **Two of the three worst failures in a vibe-coded app start in frontend code**: a loop that
> bills you, and a UI that shows the wrong data because state exists in two places. This file is
> those rules.

---

# 1 · ⭐⭐ The render loop — the bill

```
⭐⭐ THE SINGLE MOST EXPENSIVE MISTAKE IN AI-GENERATED REACT.

  💀 useEffect(() => {
       fetch('/api/items').then(r => r.json()).then(setItems);
     });                    ⭐ NO DEPENDENCY ARRAY.
  ⇒ fetch → setItems → re-render → fetch → ... ⭐⭐ FOREVER.
  ⇒ ⭐ THE PAGE LOOKS COMPLETELY NORMAL. Nothing errors. You find out
    from the invoice.

  💀 useEffect(() => { load(filters); }, [{ page, size }]);
  ⇒ ⭐⭐ AN OBJECT LITERAL IS A NEW REFERENCE EVERY RENDER, so the
    dependency ALWAYS looks changed. Same infinite loop.
```

```jsx
// ✅ primitive deps, cancelled on unmount
useEffect(() => {
  const c = new AbortController();
  fetch(`/api/items?page=${page}&size=${size}`, { signal: c.signal })
    .then(r => r.json()).then(setItems)
    .catch(e => { if (e.name !== 'AbortError') setError(e); });
  return () => c.abort();
}, [page, size]);          // ⭐ PRIMITIVES

// ✅✅ BEST — no effect at all. Dedupes, caches, bounded retries.
const { data } = useQuery({
  queryKey: ['items', page, size],
  queryFn: () => api(`/items?page=${page}&size=${size}`),
  staleTime: 60_000,
  retry: 2,
});
```

**The nine-question audit — run it on every AI-written diff:**

| # | Question |
|---|---|
| 1 | Does every fetching `useEffect` have a correct **primitive** dependency array? |
| 2 | Does any `setState` inside a fetch re-trigger that fetch? |
| 3 | Is every `setInterval` / poll cleared on unmount? |
| 4 | Is every in-flight request cancelled on unmount? |
| 5 | Do retries have a max count and backoff **with jitter**? |
| 6 | Are search inputs debounced (400ms, min 2 chars)? |
| 7 | Is there a server-side rate limit **per user and per IP**? |
| 8 | Are payment operations idempotent? |
| 9 | Is there a hard spend cap with an alert **below** it? |

```
⭐⭐ THE OTHER THREE LEAKS
  · ⭐ POLLING that never stops    ⇒ every setInterval needs a matching
    clearInterval in the cleanup. Navigate away 10 times, get 10 timers.
  · ⭐ LISTENERS on window/document never removed ⇒ each one retains the
    whole closure. Use { signal } to remove a batch.
  · ⭐⭐ WEBSOCKET/SSE not closed on unmount ⇒ N navigations = N held
    connections, and the server runs out.
```

---

# 2 · ⭐⭐ State — the wrong-data bug

```
⭐⭐ THE RULE: ANYTHING YOU CAN COMPUTE MUST NOT BE STORED.
   Two copies of one fact WILL drift, and nothing tells you when.

  💀 const [items, setItems]   = useState([]);
     const [count, setCount]   = useState(0);      ⭐ DERIVED
     const [total, setTotal]   = useState(0);      ⭐ DERIVED
     ⇒ every code path must update all three, forever. remove() forgets
       one. ⭐⭐ THE BADGE SAYS 5, THE LIST SHOWS 4. NO ERROR.

  ✅ const [items, setItems] = useState([]);
     const count = items.length;
     const total = items.reduce((s,i) => s + i.price, 0);
     ⇒ ⭐ CANNOT DRIFT. There is nothing to keep in sync.
```

**Before every `useState`, four questions:**

```
① Is it a prop?                          ⇒ not state
② ⭐⭐ CAN I COMPUTE IT?                   ⇒ NOT STATE. COMPUTE IT.
③ Does anything re-render when it changes? ⇒ if no, a ref
④ ⭐ DID IT COME FROM THE SERVER?          ⇒ that is a CACHE, not state.
                                             Use TanStack Query.
```

| ⭐ Where state belongs | |
|---|---|
| One component uses it | Inside that component |
| Two siblings | The common parent |
| ⭐⭐ **Filters, sort, page, search, active tab** | **THE URL** — survives refresh, shareable, Back works |
| From the server | ⭐ A query cache, not `useState` |
| Half the app (auth, theme) | Context — ⭐ things that are *wide and slow* |

> ⭐⭐ **The URL is state you keep forgetting you have.** A filtered list whose filter lives in
> `useState` loses it on refresh and cannot be shared — that is a deleted feature, not a design choice.
> ⭐ And reset the page number when a filter changes, or the user lands on page 7 of a 2-page result
> and thinks the site is broken.

---

# 3 · Lists and keys

```
⭐⭐ NEVER USE THE ARRAY INDEX AS A KEY ON A LIST THAT CAN CHANGE.

   Rows [A, B, C], keys 0,1,2. User ticks a checkbox on B.
   Delete A ⇒ rows [B, C], keys 0,1.
   ⇒ React keeps instance 1 (which holds B's ticked box) and gives it
     C's data.
   ⇒ ⭐⭐ THE TICK IS NOW ON C. THE SCREEN LOOKS NORMAL.
     ⭐ If that checkbox means "refund this order", you refunded the
       wrong customer.

  ✅ key={item.id}   — a stable identity that belongs to the DATA
  ❌ key={index} · key={Math.random()} · key={JSON.stringify(item)}
```

```
□ Paginate. ⭐ No screen renders an unbounded list.
□ Under 100 rows ⇒ do nothing. 1000+ ⇒ ⭐ ASK WHY THE SERVER SENT
   1000 ROWS before you virtualise. Pagination fixes payload, memory
   AND render; virtualisation fixes only render.
```

---

# 4 · Forms

```
□ ⭐ VALIDATE ON THE CLIENT FOR UX, ON THE SERVER FOR SAFETY.
   ⭐⭐ The client one protects nobody — curl ignores it. The real risk
     is the two DRIFTING, so derive both from one schema (Zod).
□ Validate on BLUR, then re-validate on change once a field has errored
   ⭐ Validating every keystroke shows "invalid email" while typing "a"
□ ⭐⭐ NEVER CLEAR THE FORM ON ERROR. The most user-hostile thing a page
   can do is discard what someone typed because the server said no.
□ ⭐ SERVER ERRORS LAND ON THE RIGHT FIELD, not in a toast.
   "Email already taken" belongs under the email box.
□ Focus the first error on failed submit
□ ⭐⭐ DOUBLE-SUBMIT: disable while pending is UX, NOT the fix.
   The fix is an idempotency key generated once per form + a unique
   constraint. A slow network and the Enter key still race.
□ Every input has a <label>. A placeholder is not a label.
□ Warn before navigating away from a dirty form
```

---

# 5 · Data fetching

```
⭐⭐ THE SEVEN BUGS IN THE TUTORIAL VERSION:
  ① no error handling — a 500 renders as an empty list
  ② no loading state       ③ ⭐ no empty state (and it needs TWO)
  ④ ⭐ a RACE — a fast filter change lets the OLD response win
  ⑤ sets state after unmount
  ⑥ no cache — Back re-fetches everything
  ⑦ no retry

⭐ USE A QUERY LIBRARY. You are buying: dedupe · cache across mounts ·
  stale-while-revalidate (no flicker) · bounded retry · cancellation.
```

```
□ ⭐⭐ fetch() DOES NOT THROW ON 4xx/5xx. `.ok` is false and the promise
   RESOLVES. ⭐ Every hand-written fetch that forgets this treats a 500
   as success and renders the error JSON as data.
□ One API layer. ⭐ No component calls fetch() directly — grep for it.
   Anything that does has opted out of auth, retry and error handling.
□ Timeout on every request
□ ⭐ RETRY 5xx AND 429 ONLY. Never 4xx. ⭐⭐ Never blind-retry a POST —
   the response was lost, not the request, so you may create two.
□ ⭐⭐ AVOID THE WATERFALL: page fetches user → child fetches profile →
   its child fetches avatar = three sequential round trips.
   Each is individually fast, so it is invisible in server metrics.
   ⇒ hoist fetches up, or fetch in the route/server component.
```

---

# 6 · Performance — measure before you optimise

```
⭐⭐ NINE TIMES OUT OF TEN "REACT IS SLOW" IS A NETWORK PROBLEM —
   bundle size, images, or a waterfall. NOT re-renders.
   ⇒ ⭐ Reaching for useMemo first is optimising the layer you can see
     instead of the one costing you.

□ Measure in a PRODUCTION build, on a throttled CPU. ⭐ Dev mode is
   several times slower and tells you nothing.
□ ⭐⭐ useMemo/React.memo ARE NOT FREE — each adds a comparison and a
   cache entry. Memoising something cheap is a straight loss, and
   React.memo with an inline object prop NEVER hits, so you pay the
   compare forever. ⭐ Strictly worse than not having it.
□ ⭐ STRUCTURAL FIXES BEAT MEMOISATION: move state down so the
   expensive component never re-renders · pass content as children ·
   delete derived state · fix the key · split the context.
□ Debounce expensive inputs
```

---

# 7 · Errors on screen

```
□ ⭐⭐ AN ERROR BOUNDARY, OR ONE THROWN ERROR BLANKS THE ENTIRE APP.
   ⭐ React unmounts everything deliberately — a half-rendered UI could
     show wrong data. But the blast radius is total.
   ⇒ THREE LEVELS: root (never blank) · per route · per independent
     widget.
□ ⭐ BOUNDARIES DO NOT CATCH: event handlers · anything after an await ·
   errors in the fallback itself.
   ⇒ ⭐⭐ ASYNC IS MOST OF YOUR REAL ERROR SURFACE, so you also need an
     `unhandledrejection` listener and try/catch in handlers.
□ ⭐ NEVER SWALLOW AN ERROR. `catch {}` turns a bug into a blank screen
   with no trace.
□ Report to Sentry with a request id (→ [09-Testing.md](09-Testing.md))
```

---

# 8 · The mobile rules that belong here

```
□ ⭐⭐ NO HORIZONTAL SCROLL. Test 320px. Full causes and the one-line
   console check: [10-Ship-Checklist.md §2](10-Ship-Checklist.md)
□ ⭐ USE 100dvh, NOT 100vh — 100vh is wrong on mobile Safari
□ Tap targets ≥ 44px · inputs ≥ 16px font (or iOS zooms)
□ ⭐⭐ THE MOBILE MENU MUST: close on navigate · close on Escape · trap
   focus · lock the background scroll · return focus on close
□ ⭐ TEST ON A REAL PHONE. Devtools does not show you iOS Safari's
   quirks, real touch, or real latency.
```

---

**Back:** [folder index](README.md) · **Backend:** [04-Backend.md](04-Backend.md) ·
**Audit:** [10-Ship-Checklist.md](10-Ship-Checklist.md)
