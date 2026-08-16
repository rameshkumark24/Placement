# Python Developer — Written Days

One file per day. Full plan: [`../Roadmap/`](../Roadmap/README.md).

> **Same skeleton as the [Java track](../../02-Java-Developer/Days/), Python flesh.** Day numbers
> line up stage for stage, so the common SDE material is the same curriculum taught with Python
> examples. Only Stages 1, 3, 4 and 7 are genuinely different.

| Day | Lesson | Stage |
|---|---|---|
| [001](Day-001.md) | What a computer is · **C-01** why layering exists | 0 |
| [002](Day-002.md) | Source to execution — ⭐⭐ **where Python actually sits** · **C-02** link layer | 0 |
| [003](Day-003.md) | Processes and ports — how two programs stay separate | 0 |
| [004](Day-004.md) | ⭐⭐ **What a server actually is** — a loop calling `accept()` · **C-03** IP | 0 |
| [005](Day-005.md) | Clients, request/response, and why the web is client-server · **C-04** TCP I | 0 |
| [006](Day-006.md) | The Internet — packets, routers, ISPs | 0 |
| [007](Day-007.md) | DNS — full resolution, and ⭐ **the resolver in your own process** · **C-05** TCP II | 0 |
| [008](Day-008.md) | ⭐⭐ **What a TCP connection is** — TIME_WAIT, CLOSE_WAIT · **C-06** congestion control | 0 |
| [009](Day-009.md) | ⭐⭐ **TLS / HTTPS** — certificates, the handshake, and what `verify=False` really does | 0 |
| [010](Day-010.md) | ⭐⭐ **HTTP anatomy** — methods, idempotence, status families · **C-07** UDP | 0 |
| [011](Day-011.md) | HTTP evolution — 1.0 → 1.1 → 2 → 3, and why HTTP/2 can be *slower* | 0 |
| [012](Day-012.md) | ⭐⭐ **What HTML is** — a tree, the DOM, and why your scraper sees an empty div · **C-08** DNS internals | 0 |
| [013](Day-013.md) | What CSS is — box model, cascade, specificity · ⭐ selectors as a query language · **C-09** HTTP on the wire | 0 |
| [014](Day-014.md) | ⭐⭐ **The event loop** — the browser's, and therefore `asyncio`'s | 0 |
| [015](Day-015.md) | The rendering pipeline · **C-10** ⭐⭐ hashing vs encryption vs signing, and how to store a password | 0 |
| [016](Day-016.md) | REST vs RPC vs GraphQL · **C-11** ⭐⭐ sockets, `epoll`, and what `asyncio` really is | 0 |
| [017](Day-017.md) | ⭐⭐ **WebSockets** — the upgrade handshake and frames, parsed by hand | 0 |
| [018](Day-018.md) | WebSockets vs ⭐ **SSE** vs long-polling · **C-12** proxies, L4/L7, CDN | 0 |
| [019](Day-019.md) | ⭐⭐ **Where state lives** — cookies, sessions, JWT, CSRF · **C-13** the debugging toolkit | 0 |
| [020](Day-020.md) | ⭐⭐ **CORS** — and why it is not API security | 0 |
| [021](Day-021.md) | Deployment — artefacts, WSGI vs ASGI, workers, config · **C-14** 40-question drill | 0 |
| [022](Day-022.md) | 🚪 **Stage 0 capstone** — one request, end to end, in ten minutes | 0 |

**✅ Stage 0 complete (22/22).** Next: Days 023–077 — 🐍 **Stage 1, Python Mastery**: the object
model, built-in type internals, functions and generators, OOP and typing, ⭐⭐ **the GIL and
asyncio**, and the runtime.

---

## How to use a day

1. **Read the whole day first** without typing anything. 15 min.
2. **Type every code block by hand.** No copy-paste — typing is where it lands.
3. **Run the experiments.** Where a day says "break it deliberately", break it. The failure is the lesson.
4. **Do the mini task** before looking at the exit questions.
5. **Answer the exit questions aloud, no notes.** Can't answer one? Re-read that section only.
6. **Record the articulation drill.** Two minutes. Play it back.

Budget ~3 hours for a NEW day. If you finish in 90 minutes and the exit questions are clean, you
were in RECALL territory — declare it next time and move faster.

## The one rule

> **No AI-generated code during lessons or practice.** AI for explanation — always.
> AI writing your code — never.

## Every day, alongside the lesson

- **DSA in Python** — 45–75 min + a pattern journal entry ([track](../Roadmap/DSA-Parallel-Track.md))
- **🎙️ Articulation drill** — 15 min, recorded
- **Spaced repetition** — 10 min on notes from days 1, 3, 7, 14, 30 ago
