# Day 099A

**L-099A** · ➕ **Logging done properly** — structured logs, request IDs, and ⭐⭐ what must never be logged

**Time:** 2–3 hrs · **Mode:** NEW · ➕ **an addition to the roadmap**

> ⭐⭐ **The sentence to own: a log line is written once and read at 3 a.m. by someone who was not
> there — so the only question that matters is whether it can be *found* and whether it says what was
> happening, and the single feature that decides both is a request id on every line.**
>
> ⭐ Day 076 gave you the four mechanical rules. **Today is what makes logs usable in a real service**,
> and one part of it is a genuine legal and security obligation rather than a preference.

---

# Part 1 — ⭐⭐ From lines to records

```python
logger.info("user %s placed order %s for %s", user_id, order_id, total)
# ⇒ 2026-08-18T14:03:11Z INFO myapp.orders user 42 placed order 9981 for 30.00
```

```
⚠️ ⭐⭐ THAT LINE IS FINE TO READ AND USELESS TO QUERY. To answer "how many
⭐    orders over £100 failed for pro customers this hour" you must parse English
⭐    with a regex — ⭐ Day 080's tools, applied to a format that was never
⭐    designed to be parsed and that changes whenever someone rewords the message.
```

```python
# ⭐⭐ STRUCTURED — the message and the DATA are separate:
logger.info("order_placed", extra={"user_id": 42, "order_id": 9981,
                                   "total": "30.00", "tier": "pro"})
# ⇒ {"ts":"…","level":"info","event":"order_placed","user_id":42,
#    "order_id":9981,"total":"30.00","tier":"pro","request_id":"01J…"}
```

```
⭐⭐ WHY JSON WINS THE MOMENT MORE THAN ONE PERSON DEPENDS ON THE LOGS:
⭐   · ⭐⭐ QUERYABLE: `level=error AND tier=pro AND total>100` — an actual query,
⭐        not a regex against prose.
⭐   · ⭐⭐ THE EVENT NAME IS STABLE. `order_placed` survives every rewording ⇒
⭐        you can COUNT it, alert on it, and graph it. ⭐ This is the same reason
⭐        Day 076 said to use `%s` args rather than an f-string, taken to its
⭐        conclusion.
⭐   · ⭐ fields keep their TYPES — numbers stay numbers.
⭐   ⇒ ⭐ `structlog`, or a JSON formatter on stdlib logging. ⭐⭐ Both are fine;
⭐        what matters is that you choose one and every service uses the same
⭐        field names. ⭐ `user_id` in one service and `userId` in another is how
⭐        a cross-service query becomes impossible.
⭐   ⇒ ⚠️ ⭐ AND KEEP IT HUMAN-READABLE LOCALLY — a dev-mode console renderer.
⭐        JSON in a terminal is miserable, and miserable logs get ignored.
```

---

# Part 2 — ⭐⭐ The request id — the highest-value line in this file

```python
import contextvars, uuid
request_id: contextvars.ContextVar[str] = contextvars.ContextVar("request_id", default="-")

class RequestIdMiddleware:                 # ⭐ ASGI middleware (Stage 4)
    async def __call__(self, scope, receive, send):
        rid = headers.get("x-request-id") or uuid.uuid4().hex   # ⭐⭐ ACCEPT an inbound one
        request_id.set(rid)                                     # ⭐ set for this context
        ...

class RequestIdFilter(logging.Filter):     # ⭐ every record picks it up automatically
    def filter(self, record):
        record.request_id = request_id.get()
        return True
```

```
⭐⭐ WHY A ContextVar AND NOT A GLOBAL, AND NOT A PARAMETER (Day 070):
⭐   · a plain global is shared by every concurrent request ⇒ ⭐⭐ WRONG ID ON
⭐     EVERY LINE under any concurrency.
⭐   · ⭐ threading.local works for threads and NOT for asyncio, where many
⭐     coroutines share one thread.
⭐   · ⭐⭐ A ContextVar IS PER-CONTEXT: each task gets its own copy, so it is
⭐     correct for threads AND for tasks. ⭐ That is exactly what it was added for.
⭐   · ⭐ and passing the id through forty function signatures pollutes every
⭐     layer with something that is not domain data.
```

```
⭐⭐ WHAT IT BUYS YOU, AND IT IS THE DIFFERENCE BETWEEN A TWO-MINUTE AND A
⭐   TWO-HOUR INVESTIGATION:
⭐   · ⭐⭐ ONE QUERY — request_id=01J7… — RETURNS EVERY LINE FOR THAT REQUEST,
⭐        in order, across concurrent traffic. ⭐ Without it, a busy service's
⭐        logs are forty requests interleaved and effectively unreadable.
⭐   · ⭐⭐ FORWARD IT AS A HEADER to every downstream call, and accept an inbound
⭐        one ⇒ ⭐ THE ID SPANS SERVICES. One query, the whole journey.
⭐   · ⭐⭐ PUT IT IN THE ERROR RESPONSE. Then a user says "I got an error, id
⭐        01J7…" and you go straight to the exact lines. ⭐ This is the cheapest
⭐        support feature you will ever ship.
```

⭐ **Add the same way:** `user_id`, `tenant_id`, route, and duration. ⭐⭐ **`duration_ms` on the
completion line turns your logs into a latency dataset** you can query when you have no metrics yet.

---

# Part 3 — ⭐ Levels, and the double-reporting bug

| Level | ⭐⭐ Means | ⭐ Test |
|---|---|---|
| `DEBUG` | ⭐ detail for a developer | ⭐ off in production (or sampled) |
| `INFO` | ⭐⭐ a **business event** happened | ⭐ "order placed", "user registered" |
| `WARNING` | ⭐⭐ something bad that **recovered** | ⭐ a retry succeeded, a fallback fired |
| `ERROR` | ⭐⭐ **a human must look** | ⭐⭐ if nobody would act, it is not an ERROR |
| `CRITICAL` | ⭐ the service cannot function | ⭐ rare, and it should page someone |

```
⭐⭐ THE ONE TEST THAT FIXES LEVEL DISCIPLINE: "WOULD SOMEONE BE WOKEN UP FOR
⭐   THIS?" ⇒ if not, it is not an ERROR.
⭐   ⇒ ⭐⭐ A 400 FROM A USER SENDING BAD JSON IS NOT AN ERROR — it is INFO, or
⭐        at most WARNING. ⭐ Logging every client mistake at ERROR is how a team
⭐        learns to ignore ERROR, and then misses the real one.
⭐   ⇒ ⭐ this is Day 060's distinction exactly: EXPECTED failures are domain
⭐        facts; UNEXPECTED ones are bugs. ⭐⭐ Level should follow that line.
```

```python
# ⚠️⚠️ ⭐⭐ DOUBLE REPORTING — the most common logging bug in Python:
def charge(order):
    try:
        gateway.charge(order)
    except GatewayError:
        logger.exception("charge failed")     # ⚠️ logged here…
        raise                                  # ⚠️ …and again at the boundary,
                                               #    and possibly again above that
# ⇒ ⭐⭐ ONE FAILURE, THREE STACK TRACES, THREE ALERTS — and the on-call engineer
#    now believes there were three incidents.

# ⭐⭐ THE RULE (Day 060): LOG ONCE, AT THE BOUNDARY. Add context on the way up.
    except GatewayError as e:
        raise ChargeFailed(order.id) from e    # ⭐ translate, do not log
# ⇒ ⭐ and ONE handler at the entry point calls logger.exception once.
```

---

# Part 4 — ⭐⭐ What must never be logged

```
⚠️⚠️ ⭐⭐ NEVER, UNDER ANY CIRCUMSTANCES:
⭐   · passwords — ⭐ including inside a request body you logged wholesale
⭐   · ⭐⭐ tokens, API keys, session ids, Authorization headers, cookies
⭐        ⇒ ⭐⭐ A LOGGED SESSION TOKEN IS A WORKING CREDENTIAL sitting in a
⭐          system that far more people can read than can read production.
⭐   · card numbers, CVV (⭐ PCI), bank details
⭐   · ⭐ personal data beyond what you can justify — names, addresses, emails,
⭐        health data, IPs in some jurisdictions
⭐   · ⭐⭐ WHOLE REQUEST BODIES "for debugging" ⇒ ⭐ this is how ALL of the above
⭐        gets logged at once, and it is the single most common way it happens.
```

```
⭐⭐ AND THE PROPERTY THAT MAKES THIS SERIOUS RATHER THAN TIDY — IT IS THE SAME
⭐   SHAPE AS DAY 085'S COMMITTED SECRET: LOGS ARE COPIED, SHIPPED, INDEXED AND
⭐   RETAINED. By the time you notice, the value is in your aggregator, its
⭐   backups, its replicas and possibly a third-party SaaS in another
⭐   jurisdiction. ⭐⭐ YOU CANNOT DELETE IT FROM ALL OF THEM ⇒ THE RESPONSE IS
⭐   ROTATION, EXACTLY AS ON DAY 085.
⭐   ⇒ ⭐ and under GDPR, logs containing personal data are personal data: they
⭐        fall under retention limits and deletion requests. ⭐⭐ "It is only logs"
⭐        is not a legal position.
```

```python
REDACT = {"password", "token", "authorization", "api_key", "card_number", "secret"}

class RedactFilter(logging.Filter):        # ⭐ defence in depth — not the primary control
    def filter(self, record):
        for key in list(getattr(record, "__dict__", {})):
            if any(s in key.lower() for s in REDACT):
                setattr(record, key, "***")
        return True
```

⭐⭐ **A redaction filter is a safety net, not a strategy.** ⭐ The primary control is **logging
specific fields you chose** rather than dumping objects — `extra={"user_id": u.id}` can never leak a
password; `extra={"user": u.__dict__}` eventually will.

```
⭐ COST AND VOLUME — logging is I/O (Day 064):
⭐   · ⭐ a DEBUG line in a hot loop is real CPU and real money at ingest
⭐        ⇒ ⭐⭐ Day 076's `%s` args mean a suppressed line costs almost nothing
⭐   · ⭐ SAMPLE high-volume events (1 in 100) and always log errors
⭐   · ⭐⭐ LOG TO STDOUT and let the platform collect it (Day 021) — ⭐ do not
⭐        write files or rotate logs inside a container
⭐   · ⭐⭐ PYTHONUNBUFFERED=1, or you lose the last buffer on SIGKILL (Days 079, 082)
```

---

# Part 5 — ⭐ Logs, metrics, traces — and using the right one

| ⭐ Pillar | ⭐⭐ Answers | ⭐ Do not use it for |
|---|---|---|
| ⭐⭐ **Logs** | ⭐⭐ *what happened to **this** request* | ⚠️ computing aggregates |
| ⭐⭐ **Metrics** | ⭐⭐ *is the system healthy* — rates, p95, error ratio | ⚠️ per-request detail |
| ⭐ **Traces** | ⭐⭐ *where did the time go across services* | ⚠️ long-term storage |

```
⭐⭐ THE MISTAKE WORTH NAMING: COUNTING LOG LINES TO GET A METRIC. It is
⭐   expensive at ingest, it breaks the moment someone rewords a message, and it
⭐   silently under-counts when logs are sampled or dropped.
⭐   ⇒ ⭐⭐ EMIT A COUNTER for "how many", a HISTOGRAM for "how slow", and keep
⭐        logs for "what happened to this one request". ⭐ Structured logging
⭐        makes the mistake tempting precisely because it almost works.
⭐   ⇒ ⭐ and the three connect through the request/trace id — ⭐⭐ which is why
⭐        Part 2 is the foundation for all of this rather than a nicety.
```

---

## Common mistakes

| Mistake | Correction |
|---|---|
| ⭐ Prose logs in a service | ⭐⭐ Structured — the event name must be stable and queryable. |
| Different field names per service | ⭐ Cross-service queries become impossible. Agree on names. |
| ⭐⭐ **No request id** | ⭐⭐ Concurrent logs are interleaved and unreadable. `ContextVar`. |
| A global or `threading.local` for the id | ⭐ Wrong under concurrency; broken under asyncio. |
| ⭐ Request id not returned to the user | ⭐ The cheapest support feature there is. |
| ⭐⭐ **Logging and re-raising** | ⭐⭐ Three traces, three alerts, one incident. Log once at the boundary. |
| Client errors at `ERROR` | ⭐⭐ Teaches the team to ignore ERROR. Would someone be woken? |
| ⭐⭐ **Logging whole request bodies** | ⭐⭐ How passwords and tokens get logged, every time. |
| Relying on a redaction filter | ⭐ A safety net. Log fields you chose. |
| "It is only logs" about personal data | ⭐⭐ Logs containing personal data *are* personal data. |
| ⭐ Writing log files in a container | ⭐ stdout; the platform collects. |
| Counting log lines as a metric | ⭐⭐ Use a counter. Breaks on rewording and sampling. |

---

## Interview questions

**Q: What makes logs actually useful in production?**
> Structure and a request id. Structure, because prose can't be queried — I want `level=error AND
> tier=pro`, not a regex against a sentence that changes whenever someone rewords it, and a stable
> event name I can count and alert on. And a request id on every line, because a busy service's logs
> are dozens of requests interleaved; with an id, one query returns everything for that request in
> order. I'd propagate it as a header to downstream services so it spans the whole journey, and return
> it in error responses so a user can quote it in a support ticket.

**Q: Why a `ContextVar` for the request id?**
> Because a global is shared by every concurrent request, so under any concurrency you'd stamp the
> wrong id on lines. `threading.local` fixes that for threads but breaks under asyncio, where many
> coroutines share one thread. A `ContextVar` is per-context — each task gets its own copy — so it's
> correct for both, which is exactly what it was added for. The alternative, threading the id through
> forty function signatures, pollutes every layer with something that isn't domain data.

**Q: What's wrong with logging an exception and re-raising it?**
> You get the same failure logged again at the next boundary, and again above that — one incident,
> three stack traces, three alerts, and an on-call engineer who believes three things went wrong. The
> rule is log once, at the boundary. On the way up you translate and add context — `raise
> ChargeFailed(order.id) from e` — and one handler at the entry point calls `logger.exception` once.

**Q: What should never appear in logs?**
> Passwords, tokens, API keys, session ids, `Authorization` headers, cookies, card details, and
> personal data beyond what you can justify. The one that causes most of it is logging whole request
> bodies "for debugging" — that dumps all of the above at once. And it matters more than tidiness
> because logs get copied, shipped, indexed and retained: by the time you notice, the value is in the
> aggregator, its backups, its replicas and possibly a third-party service in another jurisdiction. You
> can't delete it from all of them, so the response is rotation — exactly like a committed secret.
> Under GDPR, logs containing personal data are personal data, so "it's only logs" isn't a legal
> position.

**Q: Logs, metrics or traces?**
> Different questions. Logs answer what happened to *this* request. Metrics answer whether the system
> is healthy — rates, p95, error ratio. Traces answer where the time went across services. The mistake
> worth naming is counting log lines to get a metric: it's expensive at ingest, it breaks the moment
> someone rewords a message, and it silently under-counts when logs are sampled. Emit a counter for
> "how many" and a histogram for "how slow". The three connect through the request or trace id, which
> is why that id is the foundation rather than a nicety.

---

## Mini task

1. ⭐⭐ Convert one service's logging to JSON output. **Then write a query you could not have written
   before.**
2. ⭐ Add a dev-mode console renderer so local logs stay readable.
3. ⭐⭐ Implement the `ContextVar` request id with a middleware and a logging filter. **Fire twenty
   concurrent requests and confirm the ids do not cross.**
4. ⭐⭐ Do it wrong on purpose with a module-level global and **watch the ids interleave.**
5. ⭐ Forward the id as a header to a downstream service and confirm one query spans both.
6. ⭐⭐ Return the request id in your error response body.
7. ⭐ Add `duration_ms` to the completion line, then **compute p95 from your logs** (Day 080).
8. ⭐⭐ Find a `logger.exception` followed by a `raise` in your code. **Count how many times one
   failure is reported.** Fix it.
9. ⭐ Audit your ERROR logs: for each, ask "would someone be woken for this?" **Downgrade the ones
   that fail.**
10. ⭐⭐ Log an object with `__dict__` and see what leaks. Replace it with named fields.
11. ⭐ Write the redaction filter, then **deliberately log a password** and confirm it is masked — and
    note why this is still not the primary control.
12. ⭐⭐ Run a container without `PYTHONUNBUFFERED`, `kill -9` it, and **count the log lines you lost.**

---

# 🚪 Exit questions

Answer aloud, no notes.

1. Why is a prose log line useless at scale?
2. What makes an event name valuable, and which earlier rule is that the same as?
3. Why a `ContextVar` rather than a global or `threading.local`?
4. Three things a request id buys you.
5. Why put the request id in the error response?
6. Give the level test in one sentence.
7. Why is a client 400 not an ERROR?
8. What is double reporting, and what is the rule?
9. List six things that must never be logged.
10. Why is a logged secret like a committed secret?
11. Why is a redaction filter not a strategy?
12. What does each of the three pillars answer?
13. What is wrong with counting log lines as a metric?

## 🎙️ Articulation drill

Record two minutes: **"A user reports an error. Walk me through finding out what happened."**

⭐ **Start from what you would have built beforehand, because that is the real answer:** **"the whole
thing depends on a decision made long before the incident — every log line carries a request id, and
the id is returned in the error response. So the user quotes '01J7…' in the ticket, I run one query,
and I get every line for that request in order."**

⭐⭐ **Then explain why that is not trivial:** *"without it, a busy service's logs are dozens of
requests interleaved and effectively unreadable — you're guessing which lines belong together from
timestamps. And I'd hold the id in a `ContextVar` rather than a global, because a global is shared
across concurrent requests and `threading.local` breaks under asyncio where many coroutines share one
thread. The id also goes out as a header to downstream services, so one query covers the whole journey
rather than one hop."*

⭐⭐ **Then what the lines have to contain:** *"structured logs, so I can query `level=error AND
tier=pro` instead of regexing English, with a stable event name I can count and alert on. And logged
once — the most common logging bug I see is `logger.exception` followed by a re-raise, so one failure
produces three stack traces and three alerts, and whoever's on call thinks three things broke. Log
once at the boundary, translate and add context on the way up."*

⭐ **Close on the constraint people forget:** *"and none of those lines may contain the token, the
password or the request body. Logs get copied, shipped, indexed and retained, so a logged credential is
in the aggregator, its backups and possibly a third-party service — you can't delete it from all of
them, so the response is rotation, exactly like a secret committed to Git. The defence is logging
fields I chose rather than dumping objects."*

---

**Previous:** [Day 099](Day-099.md) · **Tomorrow:** [Day 100](Day-100.md) — ⭐⭐ **debugging as a
methodology**: `pdb`, post-mortem, and hypothesis-driven bisection · **D-08** SQL II
