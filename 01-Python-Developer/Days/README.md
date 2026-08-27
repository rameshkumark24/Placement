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
| [084](Day-084.md) | ⭐⭐ **The Git object model** — build a commit by hand · **D-03** ER modelling | 2 |
| [085](Day-085.md) | ⭐ **The three trees** — `add -p`, commit messages, and ⭐⭐ the committed secret | 2 |
| [086](Day-086.md) | ⭐⭐ **Branching, the three-way merge, and merge vs rebase** — argued, not asserted | 2 |
| [087](Day-087.md) | ⭐⭐ **`reflog`, `reset`/`revert`, `bisect`** — nothing is lost · **D-04** functional dependencies | 2 |
| [088](Day-088.md) | Remotes, trunk-based vs git-flow, ⭐⭐ **PR discipline** | 2 |
| [089](Day-089.md) | Clean code — naming, functions, side effects, and ⭐ when a comment is a failure | 2 |
| [090](Day-090.md) | ⭐⭐ **Structure and seams** — `src` layout, layering, and the circular import as a design bug | 2 |
| [091](Day-091.md) | ⭐ **Refactoring** — smells, safe steps, the strangler · **D-05** normalization to BCNF | 2 |
| [092](Day-092.md) | 🧪 **Testing philosophy** — the pyramid, FIRST, and ⭐⭐ what a test is actually for | 2 |
| [093](Day-093.md) | 🧪 **pytest I** — ⭐⭐ fixtures as dependency injection, `conftest.py`, scope | 2 |
| [094](Day-094.md) | 🧪 **pytest II** — ⭐⭐ `parametrize`, markers, `monkeypatch`, and the config that earns trust | 2 |
| [095](Day-095.md) | 🧪 **Test doubles** — ⭐⭐ when mocking is a design smell · **D-06** relational algebra | 2 |
| [096](Day-096.md) | 🧪 **TDD** — a rate limiter, red/green/refactor, and ⭐ where TDD does *not* help | 2 |
| [097](Day-097.md) | 🧪 **The hard parts** — async, databases, time, HTTP · **D-07** SQL I and the order of operations | 2 |
| [098](Day-098.md) | 🧪 ⭐⭐ **Coverage vs mutation testing** — why 100% coverage proves almost nothing | 2 |
| [098A](Day-098A.md) | ➕ 🧪 **Hypothesis** — property-based testing, and ⭐⭐ the shrinking that makes it usable | 2 |
| [099](Day-099.md) | ⭐⭐ **Ruff, mypy, pre-commit** — the automated reviewer, so humans review design | 2 |
| [099A](Day-099A.md) | ➕ **Logging done properly** — structured logs, ⭐⭐ request IDs, what never to log | 2 |
| [100](Day-100.md) | ⭐⭐ **Debugging as a methodology** — `pdb`, post-mortem, bisection · **D-08** SQL II and the fan-out | 2 |
| [100A](Day-100A.md) | ➕ **Profiling** — `cProfile`, `line_profiler`, `memray`, and ⭐⭐ the optimisation ladder | 2 |
| [100B](Day-100B.md) | ➕ **Code review, both sides** — the checklist, and ⭐⭐ how to say it | 2 |
| [101](Day-101.md) | 🚪 **Stage 2 capstone** — ADRs, six answers, four artefacts, a 28-question audit | 2 |
| [102](Day-102.md) | 🌐 **HTTP for backends** — idempotency, ⭐⭐ `ETag`/`If-Match`, and cache control | 3 |
| [103](Day-103.md) | ⭐⭐ **Sockets by hand** — `bind`/`listen`/`accept`, and why TCP has no messages | 3 |
| [104](Day-104.md) | **Parsing HTTP from bytes** — framing, headers, chunked, ⭐⭐ and request smuggling | 3 |
| [105](Day-105.md) | **Building the response** — `Content-Length`, keep-alive, streaming, ⭐⭐ the hang | 3 |
| [106](Day-106.md) | ⭐⭐ **Concurrency for your server** — threads, a pool, and `selectors`: asyncio by hand | 3 |
| [107](Day-107.md) | **Routing and path params** — ⭐⭐ match path, *then* method, and the ordering trap | 3 |
| [108](Day-108.md) | Request/Response objects and ⭐⭐ **middleware** — the onion, and why order is correctness | 3 |
| [109](Day-109.md) | **WSGI** — the whole spec in one page, and ⭐⭐ why it cannot do WebSockets | 3 |
| [110](Day-110.md) | **ASGI** — `scope`/`receive`/`send`, ⭐⭐ one protocol for HTTP, WebSockets and lifespan | 3 |
| [111](Day-111.md) | **Serving in production** — Gunicorn, Uvicorn workers, ⭐⭐ why nginx protects you | 3 |
| [112](Day-112.md) | 🚪 **The micro-framework capstone** — and ⭐⭐ `Depends`, demystified in 15 lines | 3 |
| [113](Day-113.md) | **Testing the framework** — a test client you write, ⭐⭐ one suite, both adapters | 3 |
| [114](Day-114.md) | 🔐 **Passwords and login** — argon2id, timing attacks, ⭐⭐ account enumeration | 3 |
| [115](Day-115.md) | 🔐 **Sessions from scratch** — cookie flags, ⭐⭐ session fixation, and CSRF | 3 |
| [116](Day-116.md) | 🔐 **JWT from scratch** — ⭐⭐ the `alg=none` attack, and why you cannot log out | 3 |
| [117](Day-117.md) | 🔐 **Refresh rotation** — ⭐⭐ detecting a stolen token by its reuse | 3 |
| [118](Day-118.md) | 🔐 **Authorisation and RBAC** — ⭐⭐ **IDOR**, and why it is a query-shape problem | 3 |
| [119](Day-119.md) | **Input validation** — ⭐⭐ parse don't validate, mass assignment, output schemas | 3 |
| [120](Day-120.md) | **Error contracts** — problem details, ⭐⭐ branch on a code, and what never to leak | 3 |
| [121](Day-121.md) | **Pagination** — offset vs ⭐⭐ **keyset**, and the drift that silently skips rows | 3 |
| [122](Day-122.md) | ⭐⭐ **Idempotency keys** — making a retried `POST` safe, end to end | 3 |
| [123](Day-123.md) | **Rate limiting** — five algorithms, ⭐⭐ and the fixed window's 2× burst | 3 |
| [124](Day-124.md) | **Caching, negotiation, compression** — ⭐⭐ four caches, and the one that leaks | 3 |
| [125](Day-125.md) | **API versioning** — ⭐⭐ what actually counts as a breaking change | 3 |
| [126](Day-126.md) | **OpenAPI by hand** — spec-first vs code-first, ⭐⭐ docs that cannot drift | 3 |
| [127](Day-127.md) | 🔒 **Security I** — injection, XSS, SSRF, and ⭐⭐ the principle under all three | 3 |
| [128](Day-128.md) | 🔒 **Security II** — ⭐⭐ `pickle` is not a data format · traversal · secrets · deps | 3 |
| [129](Day-129.md) | 🚪 **Stage 3 capstone** — six answers, four artefacts, a 32-question audit | 3 |
| [130](Day-130.md) | ⚡ **FastAPI** — Starlette + Pydantic, and ⭐⭐ recognising your own framework | 4 |
| [131](Day-131.md) | **Pydantic v2** — validators, coercion, serialisation, ⭐⭐ and the Rust core | 4 |
| [132](Day-132.md) | **Dependency injection** — sub-dependencies, `yield` teardown, ⭐⭐ overrides | 4 |
| [133](Day-133.md) | **Application structure** — routers, settings, lifespan, ⭐⭐ the one rule | 4 |
| [134](Day-134.md) | ⭐⭐ **`def` vs `async def`** — the threadpool, and the 50× regression | 4 |
| [135](Day-135.md) | **SQLAlchemy 2.0** — the session, ⭐⭐ the unit of work, `expire_on_commit` | 4 |
| [136](Day-136.md) | ⭐⭐ **The N+1 problem** — loading strategies, and `lazy="raise"` | 4 |
| [137](Day-137.md) | **Async SQLAlchemy** — the greenlet bridge, ⭐⭐ and no more lazy loading | 4 |
| [138](Day-138.md) | **Alembic** — autogenerate, ⭐⭐ locks, and expand/contract | 4 |
| [139](Day-139.md) | **The repository pattern** — ⭐⭐ where it earns its keep, and where it is ceremony | 4 |
| [140](Day-140.md) | 🔐 **Auth in FastAPI** — the dependency chain, ⭐⭐ and why auth is not middleware | 4 |
| [141](Day-141.md) | **Testing a FastAPI app** — `TestClient`, `httpx`, ⭐⭐ the transactional fixture | 4 |
| [142](Day-142.md) | **Background work** — `BackgroundTasks`, Celery, ⭐⭐ and the dual-write problem | 4 |
| [143](Day-143.md) | **Redis** — cache-aside, ⭐⭐ the stampede, and why Redis is not your database | 4 |
| [144](Day-144.md) | **WebSockets** — the upgrade, ⭐⭐ the fan-out problem, and when to use SSE | 4 |
| [145](Day-145.md) | **File uploads** — streaming, limits, ⭐⭐ and presigned URLs | 4 |
| [146](Day-146.md) | **Structured logging** — request IDs, ⭐⭐ log once at the boundary, redaction | 4 |
| [147](Day-147.md) | **Metrics and SLOs** — RED, USE, ⭐⭐ the label that kills your monitoring | 4 |
| [148](Day-148.md) | **Distributed tracing** — spans, propagation, ⭐⭐ head vs tail sampling | 4 |
| [149](Day-149.md) | **Health checks & shutdown** — ⭐⭐ the probe that causes the outage | 4 |
| [150](Day-150.md) | **Config and secrets** — fail fast at boot, ⭐⭐ and where secrets actually leak | 4 |
| [151](Day-151.md) | **Containerising Python** — layer caching, multi-stage, ⭐⭐ PID 1 | 4 |
| [152](Day-152.md) | **Resilience** — timeouts, ⭐⭐ the retry storm, circuit breakers, bulkheads | 4 |
| [153](Day-153.md) | **Rate limiting in production** — distributed counters, ⭐⭐ fairness, fail open/closed | 4 |
| [154](Day-154.md) | **CI/CD** — ⭐⭐ build once and promote, flaky tests, what must never be manual | 4 |
| [155](Day-155.md) | **Deployment strategies** — rolling, blue-green, canary, ⭐⭐ deploy ≠ release | 4 |
| [156](Day-156.md) | **Load testing** — open vs closed, ⭐⭐ coordinated omission, the knee, Little's Law | 4 |
| [157](Day-157.md) | **Profiling live** — `py-spy`, flame graphs, ⭐⭐ the memory-leak playbook | 4 |
| [158](Day-158.md) | **Webhooks** — signing and replay, ⭐⭐ and SSRF by design | 4 |
| [159](Day-159.md) | **Scheduled jobs** — ⭐⭐ the cron that ran twice, and the watermark | 4 |
| [160](Day-160.md) | **Multi-tenancy** — shared schema, ⭐⭐ row-level security, noisy neighbours | 4 |
| [161](Day-161.md) | **Event-driven architecture** — events vs commands, schemas, ⭐⭐ ordering, sagas | 4 |
| [162](Day-162.md) | 🏗 **Capstone I** — the contract, the threat model, ⭐⭐ and the decisions you write first | 4 |
| [163](Day-163.md) | 🏗 **Capstone II** — ⭐⭐ the walking skeleton, deployed on day one | 4 |
| [164](Day-164.md) | 🏗 **Capstone III** — the domain, ⭐⭐ and the race that must not happen | 4 |
| [165](Day-165.md) | 🏗 **Capstone IV** — auth, RBAC, ⭐⭐ and the threat model as a test file | 4 |
| [166](Day-166.md) | 🏗 **Capstone V** — the outbox, the worker, ⭐⭐ and `payment_unknown` | 4 |
| [167](Day-167.md) | 🏗 **Capstone VI** — observability, hardening, ⭐⭐ and a deliberate N+1 | 4 |
| [168](Day-168.md) | 🏗 **Capstone VII** — load test, ⭐⭐ find the knee, fix one thing, prove it | 4 |
| [169](Day-169.md) | 🚪 **Stage 4A exit gate** — the defence, ⭐⭐ a 40-question audit, an honest self-assessment | 4 |
| [170](Day-170.md) | 🎸 **Django** — the philosophy, the request cycle, ⭐⭐ and where it beats FastAPI | 4 |
| [171](Day-171.md) | 🎸 **The ORM I** — lazy QuerySets, ⭐⭐ `select_related` vs `prefetch_related` | 4 |
| [172](Day-172.md) | 🎸 **The ORM II** — ⭐⭐ autocommit, `atomic`, `F()`, and the bulk ops that skip everything | 4 |
| [173](Day-173.md) | 🎸 **Migrations** — the graph, ⭐⭐ `apps.get_model`, zero-downtime patterns | 4 |
| [174](Day-174.md) | 🎸 **Views, URLs, middleware** — CBVs and the MRO, sessions, ⭐⭐ CSRF | 4 |
| [175](Day-175.md) | 🎸 **DRF I** — serializers, ⭐⭐ mass assignment, and nested writes | 4 |
| [176](Day-176.md) | 🎸 **DRF II** — ⭐⭐ the object permission that never runs, throttling, pagination | 4 |
| [177](Day-177.md) | 🎸 **The admin** — ⭐⭐ a real product feature, and a real liability | 4 |
| [178](Day-178.md) | 🎸 **Django async** — ⭐⭐ what works, what silently doesn't, and the ORM façade | 4 |
| [179](Day-179.md) | 🎸 **Deploying Django** — settings, ⭐⭐ static vs media, gunicorn, Celery | 4 |
| [180](Day-180.md) | 🎸 **Testing Django** — ⭐⭐ the three things `TestCase` silently breaks | 4 |
| [181](Day-181.md) | 🎸 **Performance & caching** — four levels, ⭐⭐ and the one that leaks | 4 |
| [182](Day-182.md) | ⚖️ **The same endpoint, both frameworks** — ⭐⭐ and what was identical | 4 |
| [183](Day-183.md) | ⚖️ **Choosing and migrating** — ⭐⭐ the strangler that actually works | 4 |
| [184](Day-184.md) | ⚖️ **What actually transfers** — ⭐⭐ the twelve invariants | 4 |
| [185](Day-185.md) | 🚪 **Stage 4 exit gate** — two walkthroughs, ⭐⭐ a 50-question audit | 4 |
| [186](Day-186.md) | 🐘 **Postgres as a running program** — processes, pages, ⭐⭐ WAL, checkpoints | 5 |
| [187](Day-187.md) | 🐘 **Types & constraints** — ⭐⭐ the database as the last line of defence | 5 |
| [188](Day-188.md) | 🐘 **Schema design in practice** — ⭐⭐ the four places you stop on purpose | 5 |
| [189](Day-189.md) | ⭐ **Indexes I** — the B-tree, and ⭐⭐ the eight reasons yours is ignored | 5 |
| [190](Day-190.md) | 🐘 **Indexes II** — ⭐⭐ E-S-R, partial, covering, and the five that aren't B-trees | 5 |
| [191](Day-191.md) | ⭐ **`EXPLAIN ANALYZE`** — ⭐⭐ estimate vs actual, and the number everyone misreads | 5 |
| [192](Day-192.md) | 🐘 **Join algorithms** — three strategies, ⭐⭐ and the `LEFT JOIN` that isn't one | 5 |
| [193](Day-193.md) | 🔧 **Query optimisation workshop** — ⭐⭐ ten slow queries, against the clock | 5 |
| [194](Day-194.md) | ⭐ **MVCC, vacuum & bloat** — ⭐⭐ why `UPDATE` writes a new row, and wraparound | 5 |
| [195](Day-195.md) | ⭐ **Isolation levels** — every anomaly by hand, ⭐⭐ and write skew | 5 |
| [196](Day-196.md) | ⭐ **Locks & deadlocks** — ⭐⭐ the FIFO queue that causes outages | 5 |
| [197](Day-197.md) | 🐘 **Pooling & PgBouncer** — the arithmetic, ⭐⭐ and what a pooler breaks | 5 |
| [198](Day-198.md) | 🐘 **JSONB** — indexing, ⭐⭐ write amplification, and the ORM's silent data loss | 5 |
| [199](Day-199.md) | 🐘 **Full-text search** — four problems called search, ⭐⭐ and the honest line | 5 |
| [200](Day-200.md) | 🐘 **Partitioning** — ⭐⭐ a maintenance feature, and the key that must be everywhere | 5 |
| [201](Day-201.md) | ⭐ **Replication, failover, PITR** — ⭐⭐ the three lag anomalies, and RPO vs RTO | 5 |
| [202](Day-202.md) | ⭐ **`pg_stat_statements` & tuning** — wait events, twelve settings · 🚪 **Postgres gate** | 5 |
| [203](Day-203.md) | 🧠 **Redis architecture** — ⭐⭐ why single-threaded is the feature, and the fork | 5 |
| [204](Day-204.md) | ⭐ **Redis data structures** — ⭐⭐ five meanings of a zset score, bitmaps, HLL | 5 |
| [205](Day-205.md) | 🧠 **Persistence** — RDB, AOF, ⭐⭐ and why it doesn't protect a failover | 5 |
| [206](Day-206.md) | ⭐ **Caching patterns & eviction** — ⭐⭐ delete-don't-update, and the vanity metric | 5 |
| [207](Day-207.md) | ⭐ **Distributed locks** — four bugs, ⭐⭐ fencing tokens, and the Redlock argument | 5 |
| [208](Day-208.md) | ⭐ **Rate limiting** — four algorithms in Lua, ⭐⭐ and the boundary burst | 5 |
| [209](Day-209.md) | 🧠 **Pub/Sub & Streams** — ⭐⭐ the PEL · 🚪 **Redis block gate** | 5 |
| [210](Day-210.md) | 🍃 **MongoDB & the document model** — ⭐⭐ the inversion, and the bar it must clear | 5 |
| [211](Day-211.md) | ⭐ **Embedding vs referencing** — ⭐⭐ three questions, and six patterns | 5 |
| [212](Day-212.md) | 🍃 **Indexes & aggregation** — ⭐⭐ ESR again, `$lookup`, `$unwind` | 5 |
| [213](Day-213.md) | ⭐ **Transactions, replica sets, sharding** · 🚪🚪 **STAGE 5 EXIT GATE** | 5 |
| [214](Day-214.md) | 🌐 **Stage 6 opens** — ⭐⭐ why a backend engineer learns the frontend, and how much | 6 |
| [215](Day-215.md) | 📜 **JavaScript, the parts worth writing** — ⭐ and the traps your Python instincts walk into | 6 |
| [216](Day-216.md) | ⏳ **Async JavaScript** — ⭐⭐ the event loop you already know, and the failure it adds | 6 |
| [217](Day-217.md) | 📡 **`fetch`** — ⭐⭐ the HTTP client whose error model has no errors in it | 6 |
| [218](Day-218.md) | 🛡️ **TypeScript** — ⭐⭐ the structural typing you already know, and the `any` that deletes it | 6 |
| [219](Day-219.md) | 🧩 **TypeScript in practice** — ⭐ generics, narrowing, and typing the boundary | 6 |
| [220](Day-220.md) | 🌳 **The DOM as an API** — ⭐⭐ what the browser gives you, and why every framework exists | 6 |
| [221](Day-221.md) | 📦 **npm and the dependency surface** — ⭐⭐ 900 packages you did not choose | 6 |
| [222](Day-222.md) | 🏗️ **The build step** — ⭐⭐ what ships, and the secret you just published | 6 |
| [223](Day-223.md) | ⚛️ **React's actual model** — ⭐⭐ UI as a function of state, and what a re-render is not | 6 |
| [224](Day-224.md) | 🧱 **Components and props** — ⭐⭐ composition, and the twelve-boolean component | 6 |
| [225](Day-225.md) | 🎯 **State** — ⭐⭐ derived state is a bug, and the four questions | 6 |
| [226](Day-226.md) | 🪝 **Effects and the dependency array** — ⭐⭐ the infinite loop that bills you | 6 |
| [227](Day-227.md) | 🔄 **Data fetching, properly** — ⭐⭐ everything you must build by hand, once | 6 |
| [228](Day-228.md) | 🗃️ **The server cache** — ⭐⭐ server data was never client state | 6 |
| [229](Day-229.md) | 📝 **Forms** — ⭐⭐ two validators, one schema, and the 422 that lands on the right field | 6 |
| [230](Day-230.md) | 🔑 **Lists, keys and reconciliation** — ⭐⭐ the index key corrupts data, it is not just slow | 6 |
| [231](Day-231.md) | 🌍 **Context** — ⭐⭐ a dependency injection mechanism, not a state manager | 6 |
| [232](Day-232.md) | ⚡ **Performance** — ⭐⭐ measure first, because most `useMemo` makes things slower | 6 |
| [233](Day-233.md) | 🧭 **Routing** — ⭐⭐ the URL is state, and the loader that kills the waterfall | 6 |
| [234](Day-234.md) | 🖥️ **Rendering strategies** — ⭐⭐ CSR, SSR, SSG, ISR, RSC, and the question that picks one | 6 |
| [235](Day-235.md) | 🔐 **Auth in the browser** — ⭐⭐ where the token lives is the whole decision | 6 |
| [236](Day-236.md) | 🚫 **Authorization is server-side or it doesn't exist** — ⭐⭐ a hidden button is not a permission | 6 |
| [237](Day-237.md) | 🔀 **CORS from the other side** — ⭐⭐ it protects other sites from you | 6 |
| [238](Day-238.md) | 🎨 **Styling** — ⭐⭐ looking competent without being a designer | 6 |
| [239](Day-239.md) | ♿ **Accessibility** — ⭐⭐ four things, and the keyboard test that finds most of them | 6 |
| [240](Day-240.md) | 🩹 **Error boundaries and empty states** — ⭐⭐ the white screen, and what boundaries miss | 6 |
| [241](Day-241.md) | 📊 **Frontend performance** — ⭐⭐ why your p99 said 40 ms and the user waited 3 seconds | 6 |
| [242](Day-242.md) | 🖼️ **Images and fonts** — ⭐⭐ 70% of your bytes, and the cheapest wins available | 6 |
| [243](Day-243.md) | 🛡️ **Frontend security** — ⭐⭐ XSS, and the control that survives your mistake | 6 |
| [244](Day-244.md) | 🧪 **Testing the frontend** — ⭐⭐ behaviour, not implementation | 6 |
| [245](Day-245.md) | 🚀 **Deploying a frontend** — ⭐⭐ the deploy that breaks every open tab | 6 |
| [246](Day-246.md) | 🔗 **The full-stack contract** — ⭐⭐ making a renamed field a build failure | 6 |
| [247](Day-247.md) | 🚪🚪 **STAGE 6 EXIT GATE** — ⭐⭐ six shapes, five proofs, and an honest scope claim | 6 |
| [248](Day-248.md) | 🏗 **Choosing the flagship** — ⭐⭐ what makes a project defensible | 7 |
| [249](Day-249.md) | 🏗 **Requirements** — ⭐⭐ criteria you can write a failing test for | 7 |
| [250](Day-250.md) | 🏗 **Domain modelling & ERD** — ⭐⭐ the aggregate boundary, and the invariants table | 7 |
| [251](Day-251.md) | 🏗 **The API contract** — ⭐⭐ spec first, and the auth matrix as a grid | 7 |
| [252](Day-252.md) | 🚪 **The design review gate** — ⭐⭐ threat model, capacity, five ADRs | 7 |
| [253](Day-253.md) | 🏗 **The skeleton** — deployed on day one, ⭐⭐ four rules enforced by CI | 7 |
| [254](Day-254.md) | 🏗 **The domain layer** — ⭐⭐ the state machine as data, tests with no database | 7 |
| [255](Day-255.md) | 🏗 **Persistence** — repositories, projections, ⭐⭐ proving the query count | 7 |
| [256](Day-256.md) | 🏗 **The service layer** — ⭐⭐ the transaction boundary, and what it must refuse | 7 |
| [257](Day-257.md) | 🏗 **The web layer** — ⭐⭐ schemas as a security control, one exception handler | 7 |
| [258](Day-258.md) | 🔐 **Authentication** — ⭐⭐ refresh rotation, and reuse detection that revokes a family | 7 |
| [259](Day-259.md) | 🔐 **Authorisation** — ⭐⭐ the `WHERE` clause, RLS traps, the matrix as a test file | 7 |
| [260](Day-260.md) | 🏗 **The rest of the API** — cursors, allowlists, ⭐⭐ and the operational surface | 7 |
| [261](Day-261.md) | 🧪 **Testing the backend** — ⭐⭐ the four types that catch what matters | 7 |
| [262](Day-262.md) | 🚪 **The backend review gate** — ⭐⭐ audit it as if someone else wrote it | 7 |
| [263](Day-263.md) | 🏗 **The outbox in anger** — ⭐⭐ the dual write solved, and the third outcome | 7 |
| [264](Day-264.md) | 🏗 **Background jobs** — ⭐⭐ the lock, the watermark, and the dead-man check | 7 |
| [265](Day-265.md) | 🏗 **Async work** — ⭐⭐ the boundary of what may be lost | 7 |
| [266](Day-266.md) | ⚡ **Caching, measured** — ⭐⭐ the key as a security boundary, and the stampede | 7 |
| [267](Day-267.md) | 🏗 **File uploads** — presigned URLs, ⭐⭐ and the eight ways this goes wrong | 7 |
| [268](Day-268.md) | 🏗 **Real-time updates** — ⭐⭐ SSE vs WebSockets, and the socket that is not the truth | 7 |
| [269](Day-269.md) | 🔎 **Search and reporting** — ⭐⭐ the read path that has different rules | 7 |
| [270](Day-270.md) | 🚪 **The data and async review gate** — ⭐⭐ ten failure injections, and the silence table | 7 |
| [271](Day-271.md) | 🏗️ **The flagship's UI I** — ⭐⭐ the screen list, and what you refuse to build | 7 |
| [272](Day-272.md) | 🎨 **The design system for one app** — ⭐⭐ eight components, and knowing when to stop | 7 |
| [273](Day-273.md) | 🔌 **The API layer** — ⭐⭐ one place for auth, errors, retries and keys | 7 |
| [274](Day-274.md) | 🔑 **Auth end to end** — ⭐⭐ the expired session that must not lose the user's work | 7 |
| [275](Day-275.md) | 🔀 **The workflow screen** — ⭐⭐ the state machine, made visible | 7 |
| [276](Day-276.md) | 📋 **Lists against a real API** — ⭐⭐ the search box that DDoSes your own server | 7 |
| [277](Day-277.md) | 📡 **Real-time in the browser** — ⭐⭐ the tab that has been open since Tuesday | 7 |
| [278](Day-278.md) | 🎭 **Optimistic updates** — ⭐⭐ where the UI lies, and how it confesses | 7 |
| [279](Day-279.md) | 🔭 **Frontend observability** — ⭐⭐ your error count is mostly noise | 7 |
| [280](Day-280.md) | 🚪🚪 **THE FLAGSHIP FRONTEND GATE** — ⭐⭐ the demo, the failure drill, the findings | 7 |
| [281](Day-281.md) | 🧪 **Coverage and quality gates** — ⭐⭐ making the suite mean something | 7 |
| [282](Day-282.md) | 📦 **The container image** — ⭐⭐ signals, layers, and the probe that causes the outage | 7 |
| [283](Day-283.md) | 🔁 **The pipeline** — build once and promote, ⭐⭐ and the five minutes when both versions are live | 7 |
| [284](Day-284.md) | 🏗 **Infrastructure** — ⭐⭐ what breaks when you lose something, and what it costs | 7 |
| [285](Day-285.md) | 📈 **Observability** — the trace id across six processes, ⭐⭐ and the 3 a.m. test | 7 |
| [286](Day-286.md) | 💥 **Load testing** — ⭐⭐ the closed loop that hides the cliff, and the cascade of healthy pods | 7 |
| [287](Day-287.md) | 🚪 **The production readiness gate** — ⭐⭐ the flagship is finished, which means defensible | 7 |
| [288](Day-288.md) | 🏗 **The second project** — ⭐⭐ a different shape, where a wrong answer looks right | 7 |
| [289](Day-289.md) | 🏗 **Two data models** — effective dating, ⭐⭐ and a decision you can reproduce in March | 7 |
| [290](Day-290.md) | ⚙️ **The rules engine I** — rules as data, ⭐⭐ and the trace that *is* the product | 7 |
| [291](Day-291.md) | ⚙️ **The rules engine II** — backtesting, shadow mode, ⭐⭐ and the override rate | 7 |
| [292](Day-292.md) | 🔎 **RAG I — ingestion** — parsing as the ceiling, ⭐⭐ and the ancestry every chunk needs | 7 |
| [293](Day-293.md) | 🔎 **RAG II — retrieval** — hybrid and reranking, ⭐⭐ and recall@k as the hard ceiling | 7 |
| [294](Day-294.md) | 🛡️ **RAG III — failure modes** — ⭐⭐ indirect injection, spans, and the cost arithmetic | 7 |
| [295](Day-295.md) | 📝 **The ADRs you never wrote** — ⭐⭐ every magic number is an unrecorded decision | 7 |
| [296](Day-296.md) | 🔍 **Auditing `ticketed`** — ⭐⭐ improving your own past work without rewriting it | 7 |
| [297](Day-297.md) | 🚪 **STAGE 7 EXIT GATE** — ⭐⭐ two defensible projects, and fifty questions | 7 |
| [298](Day-298.md) | 🏛️ **Why architecture exists** — connascence, ⭐⭐ and the cost of change as the only justification | 8 |
| [299](Day-299.md) | 🏛️ **Layered architecture** — ⭐⭐ the four ways it rots, and the contract that stops all four | 8 |
| [300](Day-300.md) | 🏛️ **Clean & hexagonal** — ports, adapters, ⭐⭐ and why `Protocol` makes it cheaper in Python | 8 |
| [301](Day-301.md) | 🏛️ **The repository pattern** — its three real reasons, ⭐⭐ and the read path that bypasses it | 8 |
| [302](Day-302.md) | 🏛️ **The service layer** — ⭐⭐ the paper-form test for where any code belongs | 8 |
| [303](Day-303.md) | 🏛️ **Dependency injection by hand** — the composition root, ⭐⭐ and the import that opens a socket | 8 |
| [304](Day-304.md) | 🧭 **DDD I** — ubiquitous language, ⭐⭐ and value objects that kill primitive obsession | 8 |
| [305](Day-305.md) | 🧭 **DDD II** — ⭐⭐ the aggregate as a contention boundary, events, bounded contexts | 8 |
| [306](Day-306.md) | 🧭 **The modular monolith** — ⭐⭐ the default, and the three honest reasons to split | 8 |
| [307](Day-307.md) | 🔧 **Refactoring the flagship** — ⭐⭐ four numbers measured before and after | 8 |
| [308](Day-308.md) | ⏱️ **The LLD method** — ⭐⭐ the forty-five-minute clock, and the minute-30 rule | 8 |
| [309](Day-309.md) | 📐 **UML that earns its keep** — ⭐⭐ and the state diagram nobody draws | 8 |
| [310](Day-310.md) | ⚡ **Concurrency in LLD** — ⭐⭐ the question that ends most interviews | 8 |
| [311](Day-311.md) | 🅿️ **Parking Lot** — ⭐⭐ the method applied end to end, race and all | 8 |
| [312](Day-312.md) | 🛗 **Elevator** — ⭐⭐ who is in charge, and the floor that starves | 8 |
| [313](Day-313.md) | 🗄️ **LRU cache** — ⭐⭐ the design problem hiding behind the algorithm | 8 |
| [314](Day-314.md) | 🚦 **Rate limiter** — four algorithms, ⭐⭐ and the boundary burst | 8 |
| [315](Day-315.md) | 💸 **Splitwise** — ⭐⭐ a ledger not a balance, and the penny problem | 8 |
| [316](Day-316.md) | 🎟️ **BookMyShow** — ⭐⭐ holding a seat across a payment | 8 |
| [317](Day-317.md) | 🎰 **Vending machine & ATM** — ⭐⭐ write-ahead logging in a vending machine | 8 |
| [318](Day-318.md) | 🔔 **Logging & notifications** — ⭐⭐ extension without imagination | 8 |
| [319](Day-319.md) | ♟️ **Chess & Snakes and Ladders** — ⭐⭐ the move that must be simulated | 8 |
| [320](Day-320.md) | 🚕 **Delivery & cab booking** — ⭐⭐ an offer with an expiry, not an assignment | 8 |
| [321](Day-321.md) | 🚪 **STAGE 8 EXIT GATE** — ⭐⭐ two live LLD interviews, recorded and scored | 8 |
| [322](Day-322.md) | 📏 **Scalability, latency & SLOs** — ⭐⭐ Little's Law and the utilisation curve | 9 |
| [323](Day-323.md) | 🧮 **Back-of-the-envelope estimation** — ⭐⭐ the numbers that pick the architecture | 9 |
| [324](Day-324.md) | ↔️ **Vertical vs horizontal** — ⭐⭐ what "stateless" really means, and the four hidden states | 9 |
| [325](Day-325.md) | ⚖️ **Load balancing** — L4 vs L7, ⭐⭐ and the health-check death spiral | 9 |
| [326](Day-326.md) | 🗃️ **Caching at scale** — four failure modes, ⭐⭐ consistent hashing derived | 9 |
| [327](Day-327.md) | 🌍 **CDN & object storage** — ⭐⭐ the upload that must not touch your API | 9 |
| [328](Day-328.md) | 🔁 **Replication** — ⭐⭐ three anomalies and the failover that loses writes | 9 |
| [329](Day-329.md) | 🪓 **Sharding** — the key, the hotspot, ⭐⭐ and the reshard you cannot afford | 9 |
| [330](Day-330.md) | ⚖️ **CAP done correctly** — the three-line proof, ⭐⭐ and PACELC | 9 |
| [331](Day-331.md) | 🧊 **Consistency models** — ⭐⭐ the four session guarantees | 9 |
| [332](Day-332.md) | 🗄️ **SQL vs NoSQL at scale** — ⭐⭐ when you must commit to your queries | 9 |
| [333](Day-333.md) | 📬 **Queues & event-driven** — ⭐⭐ orchestrate the transaction, choreograph the effects | 9 |
| [334](Day-334.md) | 🪵 **Kafka fundamentals** — ⭐⭐ where ordering actually lives | 9 |
| [335](Day-335.md) | 🎯 **Delivery semantics** — ⭐⭐ why exactly-once is a lie | 9 |
| [336](Day-336.md) | 🌊 **Rate limiting & backpressure** — ⭐⭐ the half nobody prepares | 9 |
| [337](Day-337.md) | 🔎 **Search & inverted indexes** — ⭐⭐ keeping a derived index honest | 9 |
| [338](Day-338.md) | 🔭 **Observability at scale** — ⭐⭐ cardinality, and alerts people trust | 9 |
| [339](Day-339.md) | 🧯 **Resilience patterns** — ⭐⭐ the anatomy of a cascading failure | 9 |
| [340](Day-340.md) | 🧩 **Microservices vs monolith** — Conway's law, ⭐⭐ and deployment coupling | 9 |
| [341](Day-341.md) | 🚪 **Gateway, discovery & mesh** — ⭐⭐ and when you need none of them | 9 |
| [342](Day-342.md) | 🔗 **Distributed transactions** — 2PC, sagas, ⭐⭐ compensations that aren't rollbacks | 9 |
| [343](Day-343.md) | 🗳️ **Consensus** — Raft, quorums, ⭐⭐ and the fencing token | 9 |
| [344](Day-344.md) | ⏱️ **The design interview framework** — ⭐⭐ forty-five minutes, seven moves | 9 |
| [345](Day-345.md) | 🔗 **Design 1 — URL shortener** — ⭐⭐ and the analytics write nobody sizes | 9 |
| [346](Day-346.md) | 📋 **Design 2 — Pastebin** — ⭐⭐ where large content actually lives | 9 |
| [347](Day-347.md) | 🚥 **Design 3 — distributed rate limiter** — ⭐⭐ local decisions, global truth | 9 |
| [348](Day-348.md) | 🗝️ **Design 4 — key-value store** — ⭐⭐ LSM trees and version vectors | 9 |
| [349](Day-349.md) | 🕷️ **Design 5 — web crawler** — ⭐⭐ the frontier, and politeness by structure | 9 |
| [350](Day-350.md) | 📣 **Design 6 — notifications at scale** — ⭐⭐ the self-inflicted DDoS | 9 |
| [351](Day-351.md) | 📰 **Design 7 — the news feed** — ⭐⭐ fan-out on write vs read | 9 |
| [352](Day-352.md) | 💬 **Design 8 — chat** — ⭐⭐ routing a message to a connection | 9 |
| [353](Day-353.md) | 🎬 **Design 9 — video streaming** — ⭐⭐ adaptive bitrate, and egress as the business | 9 |
| [354](Day-354.md) | 📂 **Design 10 — file sync** — ⭐⭐ chunking, and the conflict you must not resolve | 9 |
| [355](Day-355.md) | 🚗 **Design 11 — ride hailing** — ⭐⭐ 250,000 location writes a second | 9 |
| [356](Day-356.md) | 🎫 **Design 12 — ticket booking** — ⭐⭐ the virtual waiting room | 9 |
| [357](Day-357.md) | 💳 **Design 13 — payments** — ⭐⭐ the double-entry ledger and reconciliation | 9 |
| [358](Day-358.md) | ⌨️ **Design 14 — autocomplete** — ⭐⭐ precompute top-K, and the hot layer | 9 |
| [359](Day-359.md) | 🚪🚪 **STAGE 9 EXIT GATE** — three design interviews scored · ✅✅ **COMPLETE SDE** | 9 |
| [360](Day-360.md) | 📦 **Why containers** — ⭐⭐ and what they are not | 10 |
| [361](Day-361.md) | 🔬 **Container internals** — ⭐⭐ the Python process that gets OOMKilled | 10 |
| [362](Day-362.md) | 🧱 **Images and layers** — the cache, ⭐⭐ the tag lie, the secret you cannot delete | 10 |
| [363](Day-363.md) | 🏗️ **Multi-stage and scanning** — ⭐⭐ the venv you can copy, and distroless | 10 |
| [364](Day-364.md) | 🔌 **Volumes, networks, config** — ⭐⭐ what persists and what is injected | 10 |
| [365](Day-365.md) | 🧬 **Docker Compose** — ⭐⭐ and the seven things dev can never catch | 10 |
| [366](Day-366.md) | 🔧 **Containerising the flagship** — ⭐⭐ properly, and measured | 10 |
| [367](Day-367.md) | 🔀 **Nginx** — ⭐⭐ the slow client that kills a service at 2% CPU | 10 |
| [368](Day-368.md) | 🚦 **CI/CD concepts** — ⭐⭐ the feedback budget, and flakiness arithmetic | 10 |
| [369](Day-369.md) | ⚙️ **GitHub Actions** — caching, OIDC, ⭐⭐ and five supply-chain traps | 10 |
| [370](Day-370.md) | 🛠️ **A real pipeline** — lint → test → build → scan → deploy | 10 |
| [371](Day-371.md) | 🚀 **Deployment strategies** — ⭐⭐ and the precondition nobody states | 10 |
| [372](Day-372.md) | 🔐 **Config and secrets** — ⭐⭐ rotation is the thing that matters | 10 |
| [373](Day-373.md) | 📊 **Prometheus and PromQL** — ⭐⭐ why `rate()` before `sum()` | 10 |
| [374](Day-374.md) | 📈 **Grafana and SLO alerting** — ⭐⭐ alert fatigue as the real failure | 10 |
| [375](Day-375.md) | 📜 **Centralised logging** — ⭐⭐ the most expensive signal | 10 |
| [376](Day-376.md) | 🕸️ **OpenTelemetry** — ⭐⭐ the two hops that always break propagation | 10 |
| [377](Day-377.md) | ☸️ **Kubernetes I** — ⭐⭐ the reconciliation loop, and four objects | 10 |
| [378](Day-378.md) | ☸️ **Kubernetes II** — ⭐⭐ shutdown, disruption budgets, and when it is overkill | 10 |
| [379](Day-379.md) | 🚪 **Terraform · STAGE 10 EXIT GATE** — ⭐⭐ state, and five proofs · ✅ ships own work | 10 |
| [380](Day-380.md) | ☁️ **The cloud model** — ⭐⭐ regions, AZs, and the two planes | 11 |
| [381](Day-381.md) | 🔑 **IAM I** — ⭐⭐ the evaluation algorithm, in order | 11 |
| [382](Day-382.md) | 🎭 **IAM II** — roles, STS, ⭐⭐ and the death of the access key | 11 |
| [383](Day-383.md) | 🕸️ **VPC I** — ⭐⭐ what actually makes a subnet public | 11 |
| [384](Day-384.md) | 🧱 **VPC II** — groups vs NACLs, endpoints, ⭐⭐ and the debugging ladder | 11 |
| [385](Day-385.md) | 🖥️ **EC2 and EBS** — ⭐⭐ the burst-credit trap | 11 |
| [386](Day-386.md) | 🪣 **S3** — presigned uploads, ⭐⭐ and four cost traps with arithmetic | 11 |
| [387](Day-387.md) | 🐘 **RDS and Aurora** — ⭐⭐ the failover your pool refuses to notice | 11 |
| [388](Day-388.md) | ⚡ **ElastiCache** — ⭐⭐ the eviction policy that isn't | 11 |
| [389](Day-389.md) | ⚖️ **ELB and Auto Scaling** — ⭐⭐ the 502 nobody can reproduce | 11 |
| [390](Day-390.md) | 🚢 **ECS and Fargate** — ⭐⭐ and ECS vs EKS decided honestly | 11 |
| [391](Day-391.md) | λ **Lambda** — cold starts, ⭐⭐ and the cost curve that inverts | 11 |
| [392](Day-392.md) | 🌍 **The edge** — ⭐⭐ the cache key as a security boundary | 11 |
| [393](Day-393.md) | 📬 **SQS, SNS and EventBridge** — ⭐⭐ three questions, not three features | 11 |
| [394](Day-394.md) | 🔭 **CloudWatch, X-Ray, CloudTrail** — ⭐⭐ observability with a price tag | 11 |
| [395](Day-395.md) | 🔏 **KMS and Secrets Manager** — ⭐⭐ envelope encryption, and the lockout | 11 |
| [396](Day-396.md) | 💰 ⭐⭐ **Cost as an engineering constraint** — unit economics | 11 |
| [397](Day-397.md) | 🚪 **The flagship on AWS · STAGE 11 EXIT GATE** — ⭐⭐ five proofs, six shapes · ✅ cloud deployed | 11 |
| [398](Day-398.md) | 🕳️ **The eight fallacies** — ⭐⭐ and the ninth, plus the three outcomes | 12 |
| [399](Day-399.md) | 🧩 **Decomposition** — ⭐⭐ sync vs async as an availability decision | 12 |
| [400](Day-400.md) | 📡 **gRPC and protobuf** — ⭐⭐ why the field *number* is the schema | 12 |
| [401](Day-401.md) | 🪵 **Kafka I** — the log, partitions, ⭐⭐ and the `acks=all` lie | 12 |
| [402](Day-402.md) | 🎣 **Kafka II** — offsets, ⭐⭐ the rebalance spiral, static membership | 12 |
| [403](Day-403.md) | 🛡️ **Kafka III** — ISR, compaction, ⭐⭐ exactly-once described honestly | 12 |
| [404](Day-404.md) | 🐰 **RabbitMQ** — ⭐⭐ four things Kafka cannot do, and Celery's defaults | 12 |
| [405](Day-405.md) | 📜 **Event sourcing** — ⭐⭐ five costs, in the order they hurt | 12 |
| [406](Day-406.md) | 🔭 **CQRS and read models** — ⭐⭐ projection lag as a first-class concept | 12 |
| [407](Day-407.md) | 📤 ⭐⭐ **The outbox, CDC and Debezium** — and the gap bug nobody warns you about | 12 |
| [408](Day-408.md) | 🔀 **Sagas** — ⭐⭐ the pivot transaction, and compensation as a new fact | 12 |
| [409](Day-409.md) | ⏱️ **Time and ordering** — ⭐⭐ why last-write-wins silently loses data | 12 |
| [410](Day-410.md) | 🗳️ ⭐⭐ **Consensus — Raft in detail**: the election restriction, proved | 12 |
| [411](Day-411.md) | 🔒 **Distributed locking** — ⭐⭐ the fencing token, and four ways to not need it | 12 |
| [412](Day-412.md) | 💔 **Failure detection, gossip, split brain** — ⭐⭐ and replication at depth | 12 |
| [413](Day-413.md) | 🚪 **Chaos, load shedding · STAGE 12 EXIT GATE** — ⭐⭐ five proofs, six shapes · ✅ senior-track | 12 |
| [414](Day-414.md) | 🧠 **What an LLM actually is** — ⭐⭐ next-token prediction, and every consequence | 13/14 |
| [415](Day-415.md) | 🔢 **Tokens and the context window** — ⭐⭐ the unit you are billed in | 13/14 |
| [416](Day-416.md) | 🏛️ **The transformer and attention** — ⭐⭐ enough architecture to reason about cost | 13/14 |
| [417](Day-417.md) | 🎓 **The training pipeline** — pretraining, SFT, RLHF, ⭐⭐ and what each buys you | 13/14 |
| [418](Day-418.md) | 🎲 **Inference and sampling** — ⭐⭐ the determinism you cannot have | 13/14 |
| [419](Day-419.md) | 🧭 **Embeddings** — ⭐⭐ what a vector means, and why similarity is not relevance | 13/14 |
| [420](Day-420.md) | 🔌 **The API surface in Python** — ⭐⭐ the wrapper you write once | 13/14 |
| [421](Day-421.md) | 💸 ⭐⭐ **Cost, latency and the token budget** — the unit economics of a feature | 13/14 |
| [422](Day-422.md) | ✍️ **Prompt engineering I** — the four pillars, ⭐⭐ and abstention as the most valuable line | 13/14 |
| [423](Day-423.md) | 🪜 **Prompt engineering II** — few-shot, chain-of-thought, decomposition | 13/14 |
| [424](Day-424.md) | 🧾 **Structured output as schema design** — ⭐⭐ a required field manufactures fabrication | 13/14 |
| [425](Day-425.md) | 📏 ⭐⭐ **Evals** — the thing that replaces unit tests | 13/14 |
| [426](Day-426.md) | ⚖️ **LLM-as-judge** — ⭐⭐ its four failure modes, and the humans that keep it honest | 13/14 |
| [427](Day-427.md) | 🏷️ **Prompts as versioned code** — ⭐⭐ a prompt change is a deploy | 13/14 |
| [428](Day-428.md) | 🚪 **13B checkpoint** — ⭐⭐ one prompted feature, shipped and measured | 13/14 |
| [429](Day-429.md) | 📚 **Why RAG exists** — ⭐⭐ an economic argument, not an architectural preference | 13/14 |
| [430](Day-430.md) | ✂️ **Chunking is the ceiling** — ⭐⭐ no reranker fixes a bad chunk | 13/14 |
| [431](Day-431.md) | 🧭 **Embeddings in practice** — ⭐⭐ changing the model is a migration, not a config change | 13/14 |
| [432](Day-432.md) | 🗄️ **Vector databases, decided honestly** — ⭐⭐ start with the Postgres you already run | 13/14 |
| [433](Day-433.md) | 🎯 **Hybrid search and reranking** — ⭐⭐ retrieve wide, rerank narrow | 13/14 |
| [434](Day-434.md) | 🔐 **Multi-tenancy and the permission filter** — ⭐⭐ retrieval is where these systems leak | 13/14 |
| [435](Day-435.md) | 📐 **RAG evaluation** — ⭐⭐ two failures, opposite fixes, so measure them apart | 13/14 |
| [436](Day-436.md) | 🔮 **Advanced retrieval** — ⭐⭐ and the questions that are not retrieval questions | 13/14 |
| [437](Day-437.md) | 🚪 **13C build day** — ⭐⭐ one retrieval service, four invariants, six shapes | 13/14 |
| [438](Day-438.md) | 🔧 **Tool calling** — ⭐⭐ the model never calls anything; your for-loop does | 13/14 |
| [439](Day-439.md) | 🔁 **The agent loop and its budgets** — ⭐⭐ MAX_STEPS is a correctness boundary | 13/14 |
| [440](Day-440.md) | 🩹 **When the agent is wrong** — ⭐⭐ score the trajectory, gate the action | 13/14 |
| [441](Day-441.md) | 🕸️ **Multi-agent systems** — ⭐⭐ a distributed system wearing a costume | 13/14 |
| [442](Day-442.md) | 🧰 **Frameworks, or nothing** — ⭐⭐ can you print the bytes you send? | 13/14 |
| [443](Day-443.md) | 🧠 **Memory** — ⭐⭐ four different problems sharing one word | 13/14 |
| [444](Day-444.md) | ♻️ **Idempotency in a non-deterministic system** — ⭐⭐ retry no longer replays | 13/14 |
| [445](Day-445.md) | 🚪 **14A build day** — ⭐⭐ one agent, six invariants, proved by chaos | 13/14 |
| [446](Day-446.md) | 🧨 **Prompt injection** — ⭐⭐ a new trust boundary with no complete fix | 13/14 |
| [447](Day-447.md) | ☣️ **Output as untrusted input** — ⭐⭐ the model is an anonymous internet user | 13/14 |
| [448](Day-448.md) | 🚧 **Guardrails** — ⭐⭐ a check is not a boundary | 13/14 |
| [449](Day-449.md) | 👻 **Hallucination mechanics** — ⭐⭐ the model is working correctly | 13/14 |
| [450](Day-450.md) | 🔏 **Data privacy** — ⭐⭐ your traces are the most sensitive store you own | 13/14 |
| [451](Day-451.md) | 🎚️ **Fine-tuning, honestly** — ⭐⭐ you are forking somebody else's model | 13/14 |
| [452](Day-452.md) | 🖥️ **Open models and self-hosting** — ⭐⭐ the GPU is billed by the hour, not the token | 13/14 |
| [453](Day-453.md) | 🛰️ **Serving** — ⭐⭐ requests that last seconds break every default you have | 13/14 |
| [454](Day-454.md) | 🗃️ **Caching** — ⭐⭐ four layers, four keys, and one that quietly serves wrong answers | 13/14 |
| [455](Day-455.md) | 🔭 **Observability without a correct answer** — ⭐⭐ everything returns 200 while broken | 13/14 |
| [456](Day-456.md) | 🪜 **Reliability and fallback** — ⭐⭐ an untested fallback is not a fallback | 13/14 |
| [457](Day-457.md) | 💰 **Cost engineering** — ⭐⭐ the p99 request is a bug, not a heavy user | 13/14 |
| [458](Day-458.md) | 🚦 **Rate limits and multi-provider** — ⭐⭐ your eval job can starve your users | 13/14 |
| [459](Day-459.md) | 🎚️ **Rollout with eval gates** — ⭐⭐ rank every change by its rollback cost | 13/14 |
| [460](Day-460.md) | 🎤 **The AI Engineer interview** — ⭐⭐ demo or system? | 13/14 |
| [461](Day-461.md) | 🚪 **STAGE 13/14 EXIT GATE** — ⭐⭐ ten shapes, eight proofs · ✅ AI Engineer | 13/14 |
| [462](Day-462.md) | 📄 **The resume** — ⭐⭐ every line is a question you invited | 15 |
| [463](Day-463.md) | 🔍 **Defending the page** — ⭐⭐ never write a number you cannot reconstruct | 15 |
| [464](Day-464.md) | 🗣️ **Behavioural I** — ⭐⭐ ten stories cover every question they can ask | 15 |
| [465](Day-465.md) | ⚔️ **The failure and conflict stories** — ⭐⭐ the two that decide the round | 15 |
| [466](Day-466.md) | 🎙️ **Communication under evaluation** — ⭐⭐ "I don't know" is a scoring answer | 15 |
| [467](Day-467.md) | 🎬 **Project defence I** — ⭐⭐ thirty seconds, three minutes, thirty minutes | 15 |
| [468](Day-468.md) | 📓 **The decision log** — ⭐⭐ force, alternative, price | 15 |
| [469](Day-469.md) | 🔬 **The failure interrogation** — ⭐⭐ what breaks first, and how you would know | 15 |
| [470](Day-470.md) | 📂 **The code walk** — ⭐⭐ ninety seconds decide whether they read further | 15 |
| [471](Day-471.md) | 🎯 **The 45-minute deep dive** — ⭐⭐ run it, score it, find the two rows you lose | 15 |
| [472](Day-472.md) | 🎯 **DSA mock 1 — arrays, hashing, two pointers** — ⭐⭐ the protocol, and the Python traps | 15 |
| [473](Day-473.md) | 🌳 **DSA mock 2 — trees and graphs** — ⭐⭐ one traversal, two containers | 15 |
| [474](Day-474.md) | 🧮 **DSA mock 3 — dynamic programming** — ⭐⭐ four steps, and never start with the table | 15 |
| [475](Day-475.md) | ⛰️ **DSA mock 4 — heaps and intervals** — ⭐⭐ the sort key is the algorithm | 15 |
| [476](Day-476.md) | 🩺 **DSA mock 5 — mixed** — ⭐⭐ four different failures need four different fixes | 15 |
| [477](Day-477.md) | 🅿️ **LLD 1 — the parking lot** — ⭐⭐ the clock, the six questions, check-then-act | 15 |
| [478](Day-478.md) | 📨 **LLD 2 — a notification service** — ⭐⭐ if adding one needs a deploy, the design failed | 15 |
| [479](Day-479.md) | 🔒 **LLD 3 — a thread-safe cache** — ⭐⭐ the GIL does not make your code correct | 15 |
| [480](Day-480.md) | 💷 **LLD 4 — a wallet** — ⭐⭐ the balance is derived, not stored | 15 |
| [481](Day-481.md) | 🔗 **SD 1 — a URL shortener** — ⭐⭐ the estimation decides the architecture | 15 |
| [482](Day-482.md) | 📰 **SD 2 — a news feed** — ⭐⭐ one user with 50M followers breaks the design | 15 |
| [483](Day-483.md) | 💬 **SD 3 — chat** — ⭐⭐ A is on server 3, B is on server 17 | 15 |
| [484](Day-484.md) | 💳 **SD 4 — payments** — ⭐⭐ where refusing service is the correct answer | 15 |
| [485](Day-485.md) | ⏰ **SD 5 — a distributed scheduler** — ⭐⭐ exactly-once is not on the menu | 15 |
| [486](Day-486.md) | 🏢 **Company prep and the loop** — ⭐⭐ nobody scores you; a committee does | 15 |
| [487](Day-487.md) | 📊 **Levelling** — ⭐⭐ scope, not difficulty | 15 |
| [488](Day-488.md) | 🚪🚪 **Negotiation, the decision, the first 90 days** — ✅✅ **488 DAYS COMPLETE** | 15 |

**✅ Stage 0 complete (22/22).** **✅ Stage 1 complete (55/55)** — the whole language.
**✅ Stage 2 complete (24 days + 4 additions)** — Linux, Git, clean code, testing, tooling, debugging.
**✅ Stage 3 complete (28/28)** — a backend from a socket to a security review.
**✅✅ Stage 4 COMPLETE (56/56)** — FastAPI to a deployed, load-tested service; Django and DRF taught
comparatively; the same endpoint in both, and the twelve invariants underneath.
**✅✅ Stage 5 COMPLETE (28/28)** — Postgres from pages to PITR, Redis from the event loop to
Streams, MongoDB from the document model to the shard key. Almost none of it was about a vendor.
**✅✅ Stage 6 (214–247) COMPLETE** — ⭐⭐ the frontend, written for a backend engineer:
the API findings only a client reveals, React's model, auth in the browser, accessibility,
frontend performance, and the generated contract — [gate](Day-247.md).
**✅✅ STAGE 7 COMPLETE (50/50)** — two defensible projects and an audit: the flagship built,
operated, load-tested and gated; a rules-and-retrieval system where correctness is *measured* rather
than asserted; and `ticketed` re-opened and improved without being rewritten.
**✅✅ STAGE 8 COMPLETE (24/24)** — architecture and low-level design, asked about systems you have
actually built: change cost and connascence, layers and their rots, ports and adapters, DDD, the
modular monolith, a measured refactor, the 45-minute LLD method, and nine design problems that turn
out to be five shapes.
**✅✅ STAGE 9 COMPLETE (38/38) — ✅✅ COMPLETE SDE.** The longest stage: twenty-two concepts from
Little's Law to consensus, the 45-minute framework, and fourteen full designs — closing with three
unseen design interviews, recorded and scored on nine rows.
**✅✅ STAGE 12 COMPLETE (16/16)** — the mechanisms and the proofs behind Stage 9's shapes: the
three outcomes of a remote call, gRPC deadlines and protobuf evolution, Kafka in depth, RabbitMQ's
four capabilities, event sourcing costed honestly, CQRS and projection lag, ⭐⭐ **the outbox and its
gap bug**, sagas and the pivot, clocks that lie, ⭐⭐ **Raft's safety proved in three lines**, the
fencing token, split brain — closing with five proofs and the six shapes the stage keeps producing.
**✅✅ STAGE 13/14 COMPLETE (48/48) — ✅✅ AI ENGINEER.** The second-longest stage, and the
one that changes what you are hireable as. **13A** the model honestly — everything follows from
next-token prediction, latency is linear in *output*, and ⭐⭐ **cost is consumed rather than
provisioned, so a user raises your marginal cost by typing more**. **13B** prompting as engineering —
the four pillars, ⭐⭐ **a required schema field manufactures fabrication**, and evals as the
*precondition* for every optimisation rather than a quality practice. **13C** retrieval measured —
chunking as the ceiling, hybrid search and reranking, RAG evals, and ⭐⭐ **the permission filter,
where a leak returns a fluent well-cited answer and no error**. **14A** agents bounded — the loop as a
durable workflow, gates by reversibility, and ⭐⭐ **idempotency when retry no longer replays**.
**14B** safety — ⭐⭐ **prompt injection has no complete fix**, output as untrusted input,
a check is not a boundary, and hallucination as the model working correctly. **14C** production —
serving, caching, ⭐⭐ **observability when everything returns 200 while broken**, the degradation
ladder, cost engineering, and rollout ranked by rollback cost.

**✅✅ STAGE 15 COMPLETE (27/27) — INTERVIEW CONVERSION.** The stage that turns 461 days of
building into offers. **Resume** (462–463) — every line is a question you invited, and the third
question to prepare for every number is ⭐⭐ **"why isn't it higher?"**. **Behavioural** (464–466) —
ten stories cover everything they can ask, the failure story where a fake one answers the real question
in the negative, and ⭐⭐ **the four-part "I don't know" that scores**. **Project defence** (467–471) —
three lengths, the decision log as ⭐⭐ **force / alternative / price**, the failure interrogation, the
code walk, and a 45-minute deep dive against a 15-row rubric. **Five DSA mocks** (472–476) in Python,
closing with ⭐⭐ **a diagnosis table where only one of five failure modes is fixed by more problems**.
**Four LLD rounds** (477–480) — every one hides a check-then-act. **Five system design rounds**
(481–485) — the estimation decides the architecture every time. **The close** (486–488) — the loop
archetypes, ⭐⭐ **levelling as scope rather than difficulty**, and negotiation.

**✅✅✅ ALL 488 DAYS WRITTEN — 001 THROUGH 488, NO GAPS.**
⭐ **The track is self-contained**: every stage, including the frontend (214–247, 271–280), is a
day file here.
⭐ **Days 414–461 are also Stages 13 & 14 of the [Java track](../../02-Java-Developer/), routed by
[the crossing](../../02-Java-Developer/Roadmap/Stage-13-14-Bridge.md).**
⭐⭐ **Knowledge is no longer the constraint** — see [Day 488](Day-488.md).

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
