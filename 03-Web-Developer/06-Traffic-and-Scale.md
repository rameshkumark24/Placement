# 📈 Traffic & Scale — will it hold up?

> ⭐⭐ **"Will it scale?" is not one question. It is four**, and they have different answers, different
> costs, and different urgency. Answer them *before* launch — after launch you are answering them
> during an outage.

```
⭐⭐ THE FOUR QUESTIONS
  ① WHAT BREAKS FIRST at 10× traffic?      ⇒ almost always the database
  ② WHAT DOES IT COST at 10× traffic?      ⇒ the bill scales too
  ③ WHAT HAPPENS WHEN A DEPENDENCY IS DOWN? ⇒ degrade, don't hang
  ④ WHAT HAPPENS UNDER ABUSE?               ⇒ not the same as traffic
```

---

# 1 · ⭐⭐ Do the arithmetic first

```
⭐⭐ MOST "WE NEED TO SCALE" IS ONE MISSING INDEX. Do the numbers before
   you architect anything.

  10,000 visitors/day
    ÷ 86,400 seconds        =  0.12 requests/sec average
    × 10 (peak is not flat) =  1.2 req/sec peak
    × 8 requests per page   ≈  10 req/sec

  ⇒ ⭐ TEN REQUESTS PER SECOND. A single small Postgres and one server
    handle this without noticing.
  ⇒ ⭐⭐ SO IF IT IS SLOW AT THIS VOLUME, IT IS NOT A SCALE PROBLEM.
    IT IS A QUERY PROBLEM. Adding servers will not fix it.
```

| ⭐ Traffic | What you actually need |
|---|---|
| < 10k visits/day | ⭐ One instance, one Postgres, a CDN. **Nothing clever.** |
| 10k–100k/day | ⭐ Indexes right, hot reads cached, slow work queued |
| 100k–1M/day | ⭐⭐ Read replica, real caching strategy, background workers |
| > 1M/day | Sharding, multi-region — ⭐ and you will have hired for it |

> ⭐⭐ **Never architect for traffic you do not have.** The complexity is paid immediately and the
> benefit arrives never. **Design so the *next* step is possible, not so every step is pre-built.**

---

# 2 · ⭐⭐ The database breaks first — always

```
⭐⭐ THE ORDER OF FAILURE UNDER LOAD, IN PRACTICE:
   ① a query with no index          ⇒ ⭐ THE #1 CAUSE, BY FAR
   ② connection exhaustion          ⇒ ⭐⭐ the serverless killer
   ③ N+1 queries                    ⇒ 200 queries to render one page
   ④ a count(*) on a big table      ⇒ a full scan on every page load
   ⑤ a lock held too long           ⇒ everything queues behind one write
```

```
□ ⭐⭐ EVERY FOREIGN KEY IS INDEXED. Postgres does NOT do this for you.
□ ⭐ EVERY COLUMN YOU FILTER OR SORT BY IS INDEXED.
□ Composite index order: equality columns first, then range, then sort
□ ⭐ RUN EXPLAIN ANALYZE ON YOUR THREE SLOWEST QUERIES.
   "Seq Scan" on a big table ⇒ ⭐⭐ THAT IS YOUR SCALE PROBLEM.
□ ⭐⭐ TEST WITH 100,000 SEEDED ROWS, NOT 12.
   ⭐ Everything is fast with 12 rows. Every scale bug is invisible
     until the table is real.
□ Fix N+1 — one query with a join, not a query per row
□ ⭐ PAGINATE EVERYTHING. No endpoint returns an unbounded list.
□ ⭐⭐ ORDER BY needs a UNIQUE TIEBREAK: `ORDER BY created_at DESC, id DESC`.
   ⭐ Without it, rows with equal timestamps can come back in a
     different order per query ⇒ rows DUPLICATE ACROSS PAGES and others
     vanish, with no error. It looks like data corruption.
□ `count(*)` for "page 3 of 47" is a second full scan — cache it,
   estimate it, or drop the total
```

```
⭐⭐ CONNECTION EXHAUSTION — THE ONE THAT SURPRISES PEOPLE
   Serverless functions each open a DB connection. 100 concurrent
   invocations ⇒ 100 connections ⇒ ⭐ POSTGRES REFUSES AT ~100 AND THE
   WHOLE SITE ERRORS — at traffic that is otherwise trivial.
   ⇒ ⭐⭐ USE A POOLER (Supabase pooler / pgBouncer) IN TRANSACTION MODE.
     This is not optional on serverless. It is the default failure.
```

---

# 3 · Caching — in this order

```
⭐⭐ CACHE FROM THE OUTSIDE IN. THE CHEAPEST LAYER IS THE FURTHEST OUT.

 ① ⭐ CDN (Cloudflare/Vercel)  — static pages and assets.
    ⇒ hashed filenames + immutable, one year. The request never
      reaches your server at all.
 ② ⭐⭐ HTTP CACHE HEADERS      — get these right before anything else
 ③ ⭐ REDIS (Upstash)          — hot reads, computed results, sessions
 ④ IN-MEMORY                   — per-instance, careful with staleness
 ⑤ DATABASE materialised views — for expensive aggregates

⭐⭐ EVERY CACHE NEEDS THREE THINGS DECIDED, NOT ASSUMED:
   · a KEY that includes everything that changes the answer
     ⇒ ⭐⭐ INCLUDING THE USER, IF THE RESULT IS PER-USER.
       ⭐ A cache key that omits the user is a cross-user data leak that
         BYPASSES your authorization instead of defeating it — every
         check passed, the wrong data was already in the cache.
   · a TTL you chose deliberately
   · an INVALIDATION rule for when the underlying thing changes
```

> ⭐ **Never cache a personalised page at the CDN.** One user's name, served to everyone. It is a
> security incident wearing a performance costume.

---

# 4 · Move slow work off the request

```
⭐⭐ IF IT TAKES MORE THAN ~300ms AND THE USER DOES NOT NEED THE RESULT
   IMMEDIATELY, IT DOES NOT BELONG IN THE REQUEST.

  ⇒ email · PDF generation · image processing · AI/LLM calls ·
    third-party syncs · webhooks you send · exports · reports

  ⭐ IN THE REQUEST: the user waits, the connection is held, a timeout
    loses the work entirely, and one slow dependency stalls everything.
  ✅ ⭐⭐ IN A QUEUE: return 202 immediately, do the work in a worker,
    tell the user when it is done.
```

```
□ A queue (Upstash QStash, Supabase queues, or a worker) exists before
   launch if you send email or generate anything
□ ⭐ JOBS ARE IDEMPOTENT — they WILL run twice
□ ⭐⭐ JOBS HAVE A RETRY LIMIT AND A DEAD-LETTER QUEUE.
   A job retrying forever is an outage that bills you.
□ You can see failed jobs. ⭐ A queue you cannot inspect is a place
   work goes to disappear silently.
```

---

# 5 · ⭐⭐ Abuse is not traffic

```
⭐⭐ TRAFFIC IS USERS. ABUSE IS ONE SCRIPT. THEY NEED DIFFERENT DEFENCES,
   AND ABUSE ARRIVES FIRST.

□ ⭐ RATE LIMIT EVERY PUBLIC ENDPOINT — PER USER **AND** PER IP.
   ⭐ Per user alone does nothing against someone creating accounts.
□ ⭐⭐ HARD LIMITS ON: login · password reset · signup · anything that
   sends email or SMS · anything that calls a paid API
   ⭐ An unlimited "forgot password" is a free email-bombing service
     billed to you.
□ CAPTCHA / Turnstile on signup and public forms
□ ⭐ CLOUDFLARE WAF + BOT PROTECTION ON before launch
□ ⭐⭐ A SPEND CAP ON EVERY PAID SERVICE, WITH AN ALERT BELOW IT.
   ⭐ The alert is the point. The cap is what stops you being ruined;
     the alert is what lets you react.
□ File uploads: size cap, type check, per-user quota
□ ⭐ AI/LLM ENDPOINTS: auth required, per-user quota, MAX_STEPS,
   and a monthly spend ceiling. ⭐⭐ AN OPEN LLM ENDPOINT IS SOMEONE
   ELSE'S FREE API, BILLED TO YOU.
```

---

# 6 · When a dependency is down

```
⭐⭐ EVERY EXTERNAL CALL IS A THING THAT WILL BE DOWN ONE DAY.
   ⭐ THE DEFAULT BEHAVIOUR — WAIT FOREVER — IS THE WORST ONE.

□ ⭐ EVERY EXTERNAL CALL HAS A TIMEOUT. No exceptions.
   ⭐⭐ Without one, a slow dependency holds your connections until you
     have none left, and your whole site is down because THEIRS is slow.
□ Retries: max 3, exponential backoff + jitter, never retry 4xx but 429
   ⭐ Jitter matters — without it every client retries in lockstep and
     you DDoS your own recovery.
□ ⭐⭐ DECIDE THE DEGRADED BEHAVIOUR PER DEPENDENCY, IN ADVANCE:
     · search down     ⇒ show the unfiltered list + a notice
     · analytics down  ⇒ ignore it, never block the page
     · AI feature down ⇒ hide the feature, keep the app
     · payments down   ⇒ ⭐⭐ SAY SO CLEARLY. Never "maybe charged".
□ ⭐ NEVER AN INFINITE SPINNER. A timeout with a message beats a page
   that hangs.
□ Health check that tests a real dependency, not just "the process runs"
```

---

# 7 · Prove it before launch

```
□ ⭐⭐ SEED 100,000 ROWS AND USE THE SITE. ⭐ The single most valuable
   scale test, and it takes twenty minutes.
□ ⭐ LOAD TEST THE THREE ENDPOINTS THAT MATTER (k6, Artillery):
   the landing page, the main list, the main write.
   ⭐⭐ TARGET 10× YOUR EXPECTED PEAK. Watch p95, not average.
□ Watch the DATABASE during the test — CPU, connections, slow queries.
   ⭐ That is where it will break, not the app.
□ ⭐ TEST A COLD START if you are serverless
□ Kill a dependency mid-test and confirm the site degrades rather
   than dies
```

---

# 8 · ⭐⭐ Scaling triggers — write the numbers down now

Decide these *before* you need them, so you act on a number rather than a feeling.

| When | Do |
|---|---|
| p95 response > 500ms | Profile. ⭐ Find the query. |
| DB CPU sustained > 70% | Index, cache, or size up |
| Connections > 70% of limit | ⭐⭐ Add/fix the pooler |
| Error rate > 1% | Stop shipping features. Fix. |
| Any table > 1M rows | Review its indexes and its queries |
| Bill up > 30% month on month | ⭐ Find out what, before it compounds |
| A single endpoint > 40% of load | Cache it or rethink it |

---

# ⭐⭐ The honest answer to "is it scalable?"

```
❌ "Yes, it uses serverless so it scales automatically."
   ⇒ ⭐ SERVERLESS SCALES THE **APP**. Your database does not, and
     your database is what breaks.

✅ ⭐⭐ "It handles <N>× current traffic — I load-tested it with 100k
   seeded rows. The first thing to break is <the database / this
   endpoint>, at roughly <X>. The fix at that point is <a read replica
   / caching this query / a pooler>, and I know because I measured it.
   Beyond that I would need to <...>, which I have not built and do
   not need yet."

⇒ ⭐ NAMING WHAT BREAKS FIRST, AND WHEN, IS THE WHOLE ANSWER.
```

---

**Back:** [folder index](README.md) · **Speed:** [07-Performance.md](07-Performance.md) ·
**Security:** [05-Security.md](05-Security.md)
