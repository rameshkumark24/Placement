# Stages 13 & 14 — the crossing

**Java Day 413 → Python Day 461 · 103 days · the only stage of this track written in another language**

> **⭐⭐ Stages 13 and 14 are not missing from the Java track. They are deliberately not written in
> Java, and this document is the stage.** It tells you exactly which days to do, in which order, what
> you already own and can skip, and the twelve places where a Java developer's instinct is wrong in
> Python. Everything here is a route into [`01-Python-Developer/Days/`](../../01-Python-Developer/Days/).

---

# Part 1 — ⭐⭐ Why this stage is not written in Java

You have spent 413 days becoming a Java engineer. The honest question is why the AI stage makes you
leave.

```
⭐ THE ARGUMENT FOR STAYING IN JAVA        ⭐⭐ WHY IT LOSES ANYWAY
Spring AI and LangChain4j are real     →  they are wrappers over the same HTTP APIs
your employer may be a Java shop       →  ⭐ true, and Part 7 covers exactly that case
you are already fast in Java           →  ⭐⭐ you are not fast in the ECOSYSTEM, and that
                                          is what this stage actually teaches
```

| ⭐ What actually forces the crossing | |
|---|---|
| **The first-party SDKs** | Every model provider ships Python first. The Java client, where it exists, lags on streaming, tool calling and structured output — the three things Stage 14 is about. |
| **⭐⭐ The measurement tooling** | Stage 14's whole claim is *measured*, not *built*. Eval harnesses, embedding benchmarks, retrieval scoring, judge calibration — this tooling is Python and has no Java equivalent worth using. |
| **The papers ship code** | Retrieval and agent techniques arrive as a repo. You want to *read the repo*, not wait for a port. |
| **⭐⭐ The interview** | An AI engineering round will ask you to write a chunker, a retry wrapper, or an eval loop. It will be in Python. This is not a preference; it is what happens in the room. |

> **⭐ The price, stated plainly.** You will be slower for about three weeks, and you will write some
> bad Python before you write good Python. That is the whole cost. It is smaller than the cost of
> being the person who can only discuss AI systems through a wrapper.

**⭐⭐ What transfers and what does not:** the *concepts* of Days 429–461 — chunking, retrieval,
permission filters, agent budgets, injection, caching, unit economics — are language-independent and
you will carry them into any stack. The *code* is not. Learn the concepts once, here, in the language
they were written in.

---

# Part 2 — What you already own

The Python track is 488 days. **You do not do 488 days.** Days 001–022 and 078–413 of that track are
the same curriculum you have already finished in Java — networking, Linux, backend, databases,
projects, LLD, system design, DevOps, cloud, distributed systems — with Python examples.

```
⭐⭐ THE ARITHMETIC
Python track days 001–413 ................ 413 days
   already done as Java 001–413 (concepts) −358 days   ⇒ ⭐ RECALL mode, or skip entirely
   genuinely new: Python days 023–077 ..... 55 days    ⇐ ⭐⭐ THIS IS STAGE 13
Python track days 414–461 ................ 48 days     ⇐ ⭐⭐ THIS IS STAGE 14
                                          ─────────
⭐⭐ YOUR STAGE 13+14 .......................103 days
```

| ⭐ Rule for the 358 | |
|---|---|
| **Do not re-read them** | You know what a fencing token is. Reading it again in Python is not study. |
| **⭐ Do dip in when the syntax blocks you** | If a Stage 14 day uses `async with` and you stall, go read Python Day 070–072 and come back. Targeted, not sequential. |
| **⭐⭐ The exception: Days 070–072 and 075** | asyncio and packaging are not concept-recall — they are genuinely different from Java and Stage 14 depends on both. They are inside the 55. |

---

# Part 3 — ⭐⭐ The route

### Stage 13 — Python Mastery · Python Days 023–077 · 55 days

You have a language to learn, not a curriculum. These are the days, and they are the *whole* of
Stage 13.

| Block | Python days | What it is |
|---|---|---|
| **The object model** | [023](../../01-Python-Developer/Days/Day-023.md)–[032](../../01-Python-Developer/Days/Day-032.md) | ⭐⭐ Everything is an object · **there are no variables** · mutability · refcounting · the data model and dunders |
| **Data structures** | [033](../../01-Python-Developer/Days/Day-033.md)–[041](../../01-Python-Developer/Days/Day-041.md) | `list`/`dict`/`set` internals · strings and bytes · ⭐⭐ why money is never a float |
| **Functions** | [042](../../01-Python-Developer/Days/Day-042.md)–[052](../../01-Python-Developer/Days/Day-052.md) | Closures · ⭐⭐ decorators · the iteration protocol · **generators** · context managers |
| **Types and classes** | [053](../../01-Python-Developer/Days/Day-053.md)–[063](../../01-Python-Developer/Days/Day-063.md) | MRO · ⭐⭐ **`Protocol`** · dataclasses · exceptions · type hints and mypy strict |
| **⭐⭐ Concurrency** | [064](../../01-Python-Developer/Days/Day-064.md)–[073](../../01-Python-Developer/Days/Day-073.md) | **The GIL** · threading · races · multiprocessing · **asyncio** · the blocking call |
| **Tooling** | [074](../../01-Python-Developer/Days/Day-074.md)–[076](../../01-Python-Developer/Days/Day-076.md) | Imports · ⭐⭐ **`uv` and `pyproject.toml`** · the stdlib you will actually use |
| **🚪 Gate** | [077](../../01-Python-Developer/Days/Day-077.md) | Six answers, four artefacts, a 22-question audit |

> **⭐ Do not start Stage 14 until Python Day 077's gate is clean.** Stage 14 is dense enough that
> fighting the language while learning the domain means you learn neither. The gate is the check.

### Stage 14 — AI Engineering · Python Days 414–461 · 48 days

| Block | Days | ⭐⭐ The shape |
|---|---|---|
| **Foundations** | [414](../../01-Python-Developer/Days/Day-414.md)–[421](../../01-Python-Developer/Days/Day-421.md) | What an LLM actually is, and every consequence of next-token prediction — tokens, attention, training, sampling, embeddings, ⭐ **the unit economics of a feature** |
| **Prompting & evals** | [422](../../01-Python-Developer/Days/Day-422.md)–[428](../../01-Python-Developer/Days/Day-428.md) | ⭐⭐ **Evals are what replace unit tests** · abstention as the most valuable line in a prompt · a prompt change is a deploy |
| **Retrieval** | [429](../../01-Python-Developer/Days/Day-429.md)–[437](../../01-Python-Developer/Days/Day-437.md) | RAG as an economic argument · ⭐⭐ **chunking is the ceiling** · hybrid + rerank · ⭐⭐ **the permission filter — where these systems leak** |
| **Agents** | [438](../../01-Python-Developer/Days/Day-438.md)–[445](../../01-Python-Developer/Days/Day-445.md) | ⭐⭐ **The model never calls anything; your for-loop does** · MAX_STEPS as a correctness boundary · idempotency when retry no longer replays |
| **Safety** | [446](../../01-Python-Developer/Days/Day-446.md)–[452](../../01-Python-Developer/Days/Day-452.md) | Prompt injection with no complete fix · ⭐⭐ **output as untrusted input** · a check is not a boundary · hallucination as the model working correctly |
| **Production** | [453](../../01-Python-Developer/Days/Day-453.md)–[459](../../01-Python-Developer/Days/Day-459.md) | Serving, caching, ⭐⭐ **observability where everything returns 200 while broken**, fallback, cost, rate limits, eval-gated rollout |
| **🚪 Gate** | [460](../../01-Python-Developer/Days/Day-460.md)–[461](../../01-Python-Developer/Days/Day-461.md) | The AI Engineer interview · ⭐⭐ **ten shapes, eight proofs** — ✅ AI Engineer |

**Then return here** for [Stage 15 — Interview Conversion](../Days/Day-462.md), Days 462–488, which
is written in the Java track and expects both.

---

# Part 4 — ⭐⭐ The twelve places your Java instinct is wrong

This is the part that does not exist in either track, because it only matters to someone arriving
from Java. Read it once now, and again after Python Day 077.

| # | Your Java instinct | ⭐⭐ What Python actually does | Day |
|---|---|---|---|
| 1 | A variable is a box holding a value | **There are no variables.** A name is a binding to an object. Assignment rebinds the name; it never copies the object. "Call by sharing", not by value or reference. | [024](../../01-Python-Developer/Days/Day-024.md) |
| 2 | Default parameters are evaluated per call | ⭐⭐ **The default is evaluated once, at definition.** `def f(x=[])` shares one list across every call — the single most common Python bug written by experienced engineers. | [025](../../01-Python-Developer/Days/Day-025.md) |
| 3 | GC is a tracing collector; `finalize()` is dead | **Refcounting is primary**, cycles are collected separately. `__del__` runs at an unpredictable time and is *not* your cleanup hook — `with` is. | [026](../../01-Python-Developer/Days/Day-026.md), [052](../../01-Python-Developer/Days/Day-052.md) |
| 4 | `==` compares references, `.equals()` compares values | **Inverted.** `==` is value comparison (dispatches to `__eq__`), `is` is identity. And the `__eq__`/`__hash__` contract is the same contract you know — broken the same way. | [029](../../01-Python-Developer/Days/Day-029.md) |
| 5 | `private` is enforced by the compiler | **Nothing is private.** A leading underscore is a convention, and `__name` only mangles. Encapsulation is a social contract, so your API design has to be clearer, not weaker. | [031](../../01-Python-Developer/Days/Day-031.md), [053](../../01-Python-Developer/Days/Day-053.md) |
| 6 | An interface is `implements`, checked at compile time | ⭐⭐ **`Protocol` is structural.** A class satisfies it by having the methods — no declaration, no import, no coupling. This is the single biggest design difference, and it makes Stage 14's swappable providers trivial. | [056](../../01-Python-Developer/Days/Day-056.md) |
| 7 | Checked exceptions force handling | **There are none.** Nothing tells you what a call can raise. Python is EAFP — try it and handle the failure — where Java taught you LBYL. Your error-handling has to become deliberate because the compiler stopped helping. | [059](../../01-Python-Developer/Days/Day-059.md), [060](../../01-Python-Developer/Days/Day-060.md) |
| 8 | Generics are erased but still checked | ⭐ **Type hints are not enforced at all, at any point, by anything.** `mypy --strict` is a separate build step you must run in CI or the annotations are decoration. | [061](../../01-Python-Developer/Days/Day-061.md), [062](../../01-Python-Developer/Days/Day-062.md) |
| 9 | The JMM: `volatile`, happens-before, `synchronized` | ⭐⭐ **The GIL protects bytecodes, not your sequences.** `x += 1` is still a race. You get no reordering problems and no visibility problems — and exactly the same check-then-act bugs. Do not let "Python has a GIL" become "Python is thread-safe". | [065](../../01-Python-Developer/Days/Day-065.md), [067](../../01-Python-Developer/Days/Day-067.md) |
| 10 | Threads scale CPU work across cores | **They do not** (on the standard build). CPU-bound work needs `multiprocessing`, and every argument and result is pickled — a real cost that changes your design. | [064](../../01-Python-Developer/Days/Day-064.md), [068](../../01-Python-Developer/Days/Day-068.md) |
| 11 | Virtual threads: blocking code just works | ⭐⭐ **asyncio colours your functions.** One blocking call inside `async def` stalls the entire loop and every other request on it. Stage 14 is I/O-bound by nature, so this is the failure you will actually hit. | [070](../../01-Python-Developer/Days/Day-070.md)–[072](../../01-Python-Developer/Days/Day-072.md) |
| 12 | Maven resolves and the compiler catches the rest | **No compile step.** A wrong import, a renamed symbol, a version conflict — all arrive at runtime, in production, on the path nobody exercised. `uv` + a lockfile + mypy + tests are how you get back what `javac` gave you free. | [074](../../01-Python-Developer/Days/Day-074.md), [075](../../01-Python-Developer/Days/Day-075.md) |

> **⭐⭐ The one-line summary.** Java gives you guarantees at compile time. Python gives you none, and
> gives you speed instead. Every item above is you re-buying a guarantee you used to get for free —
> with a linter, a test, a lockfile, or a design choice. **Know which guarantee you are re-buying and
> what you are paying for it.** That sentence is also a very good interview answer.

---

# Part 5 — ⭐ What Java gave you that most people here lack

The crossing is not one-directional, and this matters because it is where you are *stronger* than a
Python-native AI engineer. Say these out loud in Stage 15 rounds.

| ⭐ You already have | Where it pays in Stage 14 |
|---|---|
| **Concurrency correctness intuition** | Days [438](../../01-Python-Developer/Days/Day-438.md)–[445](../../01-Python-Developer/Days/Day-445.md). Agent loops are concurrent, retried, and partially failed. Most people write them as scripts; you write them as systems. |
| **⭐⭐ Memory-model discipline** | You already ask "what happens under contention?" — which is exactly the question Day [454](../../01-Python-Developer/Days/Day-454.md)'s cache and Day [444](../../01-Python-Developer/Days/Day-444.md)'s idempotency need. |
| **"What does this cost" as a reflex** | JVM tuning made you think in budgets. Day [421](../../01-Python-Developer/Days/Day-421.md) and Day [457](../../01-Python-Developer/Days/Day-457.md) are token budgets. Same reflex, different unit. |
| **Interface-first design** | Day [442](../../01-Python-Developer/Days/Day-442.md) asks whether you can print the exact bytes you send. People who design to interfaces keep that ability; people who adopt frameworks whole lose it. |
| **⭐ Operational seriousness** | Stages 10–12 gave you observability, rollout and failure-injection habits. Days [455](../../01-Python-Developer/Days/Day-455.md)–[459](../../01-Python-Developer/Days/Day-459.md) are those habits applied to a system with no correct answer. **This is the rarest skill in AI engineering.** |

---

# Part 6 — The schedule

| Weeks | What | Mode |
|---|---|---|
| 1–8 | Python Days 023–077 | NEW — 3 hrs/day. Do not rush the object model or the GIL days. |
| — | 🚪 Python Day 077 gate | Must be clean before continuing. |
| 9–15 | Python Days 414–461 | NEW — 3 hrs/day. |
| — | 🚪 Python Day 461 gate | ✅ AI Engineer. |
| 16+ | **Return to Java Day [462](../Days/Day-462.md)** | Stage 15. |

**⭐ If you already write Python professionally:** run Days 023–077 in RECALL mode — five questions per
day, pass 4+ and move on — but do **not** RECALL Days 024, 025, 065, 067, 070–072 or 075. Those are
the ones working Python developers most often have wrong, and Stage 14 leans on all of them.

---

# Part 7 — ⭐⭐ If your employer is a Java shop

A real situation, and a real interview question. The answer is not "rewrite everything in Python".

```
⭐⭐ THE SHAPE THAT ACTUALLY SHIPS
  [ Java service ]  ──HTTP──▶  [ Python AI service ]  ──▶  [ model provider ]
   your domain,                 retrieval, prompts,
   your transactions,           evals, agent loop
   your auth                    ⭐ owns nothing durable
```

| ⭐ Question | Answer |
|---|---|
| **Why not Spring AI / LangChain4j in-process?** | You can, and for a single prompted feature it is the right call — fewer moving parts. It stops being right the moment you need **evals**, because the eval and experimentation tooling is Python-only and you will end up running it out-of-band anyway. |
| **⭐⭐ What goes in which process?** | Everything durable and authoritative stays in Java — the database, the transactions, the permission checks. The Python service is **stateless and non-authoritative**: it retrieves, prompts and returns. It never becomes a second source of truth. |
| **The permission filter?** | ⭐⭐ Read Python Day [434](../../01-Python-Developer/Days/Day-434.md) and then put the tenant on the *request*, never in a tool argument. A Java caller that passes `tenantId` as data the model can influence has the exact leak that day is about. |
| **What do I say in an interview?** | "The concepts are language-independent, the tooling isn't. I keep the domain in Java and put the AI path in a stateless Python service so I can use the eval tooling. Here's the trade I paid: one more network hop and one more deploy." |

---

# 🚪 The exit gate for the crossing

You have crossed when all of these are true. Not before.

- [ ] **Python Day [077](../../01-Python-Developer/Days/Day-077.md) gate is clean** — six answers, four artefacts, 22-question audit.
- [ ] You can explain **the GIL to a Java developer** without saying "it makes Python thread-safe", and give a `+=` race as the counter-example. → [065](../../01-Python-Developer/Days/Day-065.md), [067](../../01-Python-Developer/Days/Day-067.md)
- [ ] You can state **all twelve rows of Part 4** from memory, cold. That is the crossing.
- [ ] You have written one `Protocol`-based abstraction and swapped two implementations behind it. → [056](../../01-Python-Developer/Days/Day-056.md)
- [ ] **Python Day [461](../../01-Python-Developer/Days/Day-461.md) gate is clean** — ten shapes, eight proofs.
- [ ] ⭐⭐ You can answer *"you're a Java developer — why should I believe you can do AI engineering?"* with Part 5, not with a list of libraries.

---

**Back to:** [the Java roadmap](README.md) · [the Java day index](../Days/README.md)
**Previous:** [Java Day 413](../Days/Day-413.md) — Stage 12 exit gate
**Next:** [Python Day 023](../../01-Python-Developer/Days/Day-023.md) — Stage 13 begins · then
[Python Day 414](../../01-Python-Developer/Days/Day-414.md) — Stage 14 · then
[Java Day 462](../Days/Day-462.md) — Stage 15
