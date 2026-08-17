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
| [053](Day-053.md) | Classes — `__init__` vs `__new__`, shared class attributes, metaclasses | 1 |
| [054](Day-054.md) | ⭐ **Inheritance and the MRO** — C3, and what `super()` actually does | 1 |
| [055](Day-055.md) | ⭐ Composition over inheritance — mixins, and `n×m` → `n+m` | 1 |
| [056](Day-056.md) | ⭐⭐ **ABCs and `Protocol`** — nominal vs structural typing | 1 |
| [057](Day-057.md) | `dataclasses` — `frozen`, `slots`, `field`, and ⭐ where Pydantic takes over | 1 |
| [058](Day-058.md) | `enum`, `TypedDict` — closed sets, and ⭐ enums in a database | 1 |
| [059](Day-059.md) | ⭐⭐ **Exceptions** — the hierarchy, `raise ... from`, exception groups | 1 |
| [060](Day-060.md) | ⭐ **Error-handling design** — what to catch, where, and what never to | 1 |
| [061](Day-061.md) | ⭐⭐ **Type hints** — generics, `TypeVar`, and why variance bites | 1 |
| [062](Day-062.md) | mypy strict — what it catches, what it cannot, ⭐ **`Any` as a defect** | 1 |
| [063](Day-063.md) | Designing a class people can debug — `__repr__`, errors, the checklist | 1 |
| [064](Day-064.md) | 🧵 Processes, threads, the scheduler — ⭐ **measuring CPU- vs I/O-bound** | 1 |
| [065](Day-065.md) | ⭐⭐ **THE GIL** — what it locks, why it exists, what it does *not* prevent | 1 |
| [066](Day-066.md) | ⭐⭐ **Threading** — `Lock`, `Event`, and ⭐ why `Queue` beats locking | 1 |
| [067](Day-067.md) | ⭐ **Race conditions** — `+=` in bytecode, and why "atomic" is a trap | 1 |
| [068](Day-068.md) | ⭐⭐ **`multiprocessing`** — pickling costs, fork vs spawn, shared memory | 1 |
| [069](Day-069.md) | `concurrent.futures` — one API, two backends, and the vanishing exception | 1 |
| [070](Day-070.md) | ⭐⭐ **asyncio I** — the loop, and why sequential `await`s are not concurrent | 1 |
| [071](Day-071.md) | ⭐⭐ **asyncio II** — `TaskGroup`, cancellation, and bounding fan-out | 1 |
| [072](Day-072.md) | ⭐⭐ **The blocking call in an `async def`** — and why `def` is safer in FastAPI | 1 |
| [073](Day-073.md) | ⚙️ CPython internals — `dis`, frames, and ⭐ where the time actually goes | 1 |
| [074](Day-074.md) | Modules and imports — `sys.path`, and ⭐ why circular imports half-work | 1 |
| [075](Day-075.md) | ⭐⭐ **`uv` and `pyproject.toml`** — lockfiles and reproducible builds | 1 |
| [076](Day-076.md) | The stdlib — `pathlib`, `datetime`, `logging`, `subprocess`, `re` | 1 |
| [077](Day-077.md) | 🚪 **Stage 1 capstone** — six answers, four artefacts, a 22-question audit | 1 |
| [078](Day-078.md) | 🛠️ Filesystem, permissions, users, links · **D-01** why databases exist | 2 |
| [079](Day-079.md) | ⭐ **The shell** — pipes, redirection, exit codes, and ⭐⭐ why quoting is everything | 2 |
| [080](Day-080.md) | ⭐⭐ **`grep` `sort` `uniq` `awk` `sed` `find` `xargs`** — a log file into an answer | 2 |
| [081](Day-081.md) | ⭐⭐ **Processes and signals** — why every deploy drops requests · **D-02** the relational model | 2 |
| [082](Day-082.md) | ⭐⭐ **Observing a live process** — `/proc`, `lsof`, `strace`, `py-spy`, and the ladder | 2 |
| [083](Day-083.md) | Bash — ⭐⭐ `set -euo pipefail`, `trap`, and when to stop writing bash | 2 |

**✅ Stage 0 complete (22/22).** **✅ Stage 1 complete (55/55)** — the whole language.
**✅✅ Days 001–083 written.** 🛠️ **Stage 2 — Professional Engineering (078–101)** is open: the Linux
block is done. Next: **Git properly (084–088)** — the object model, rebase, reflog and bisect.

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
