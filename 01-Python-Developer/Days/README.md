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
| [023](Day-023.md) | 🐍 **Everything is an object** — `PyObject`, classes as objects, EAFP | 1 |
| [024](Day-024.md) | ⭐⭐ **There are no variables** — names, bindings, and call by sharing | 1 |
| [025](Day-025.md) | ⭐⭐ **Mutability** — the mutable default, aliasing, `copy` vs `deepcopy` | 1 |
| [026](Day-026.md) | ⭐⭐ **Reference counting** — why `__del__` is not cleanup, and half of why the GIL exists | 1 |
| [027](Day-027.md) | The cyclic GC — generations, thresholds, and `gc.freeze()` before fork | 1 |
| [028](Day-028.md) | Memory — object overhead, `__slots__`, interning, the small-int cache | 1 |
| [029](Day-029.md) | ⭐ `is` vs `==`, truthiness, and the ⭐⭐ `__eq__`/`__hash__` contract | 1 |
| [030](Day-030.md) | Scope, LEGB, closures, and ⭐⭐ **the loop-variable trap** | 1 |
| [031](Day-031.md) | ⭐⭐ **The data model** — syntax as dispatch to dunders | 1 |
| [032](Day-032.md) | `__getattr__` vs `__getattribute__`, ⭐ **descriptors**, and what `property` is | 1 |
| [033](Day-033.md) | ⭐ `list` internals — over-allocation, and why `pop(0)` is O(n) | 1 |
| [034](Day-034.md) | `tuple` and `NamedTuple` — a record, not an immutable list | 1 |
| [035](Day-035.md) | ⭐⭐ **`dict` internals** — open addressing, compact layout, insertion order | 1 |
| [036](Day-036.md) | `set` and `frozenset` — ⭐ set algebra as an algorithm | 1 |
| [037](Day-037.md) | ⭐ **Strings** — `str` vs `bytes`, encoding, and the silent corruption | 1 |
| [038](Day-038.md) | `collections` — `deque`, `defaultdict`, `Counter`, ⭐ the `OrderedDict` LRU | 1 |
| [039](Day-039.md) | `heapq`, `bisect`, `array` — ⭐ top-k, lower bound, and contiguous numbers | 1 |
| [040](Day-040.md) | Slicing, unpacking, star-args, and the walrus operator | 1 |
| [041](Day-041.md) | Numbers — float traps, ⭐⭐ **why money is never a float** | 1 |
| [042](Day-042.md) | Functions as objects — introspection, `lambda`, higher-order functions | 1 |
| [043](Day-043.md) | ⭐ **Closures and cells** — late binding, and what a closure keeps alive | 1 |
| [044](Day-044.md) | ⭐⭐ **Decorators** — `functools.wraps`, three levels, stacking order | 1 |
| [045](Day-045.md) | `classmethod` factories, `staticmethod`, `partial`, `operator` | 1 |
| [046](Day-046.md) | ⭐ **The iteration protocol** — iterable vs iterator, and exhaustion | 1 |
| [047](Day-047.md) | ⭐⭐ **Generators** — constant memory, pipelines, `yield from` | 1 |
| [048](Day-048.md) | Generators as coroutines — ⭐⭐ **`await` is `yield from`** | 1 |
| [049](Day-049.md) | `itertools` — batching, windowing, and the `groupby` trap | 1 |
| [050](Day-050.md) | Comprehensions — and when a plain loop is clearer | 1 |
| [051](Day-051.md) | `functools` — ⭐⭐ **why `@lru_cache` on a method is a leak** | 1 |
| [052](Day-052.md) | ⭐ **Context managers** — `contextlib`, `ExitStack`, and the only reliable cleanup | 1 |

**✅ Stage 0 complete (22/22).** **🔵 Stage 1 in progress (30/55)** — object model, built-in types
and the functions/iteration block are done. Next: Days 053–063 — classes, ⭐ **the MRO**, protocols,
dataclasses, enums, ⭐⭐ **exceptions**, type hints and mypy.

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
