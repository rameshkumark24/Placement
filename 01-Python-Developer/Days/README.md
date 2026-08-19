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
| 214–247 | 🌐 **Stage 6 — the frontend.** Not day files: worked as a phase guide in [`03-Web-Developer`](../../03-Web-Developer/), gate by gate | 6 |
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
| 271–280 | 🌐 **The flagship's frontend.** Not day files: worked in [`03-Web-Developer`](../../03-Web-Developer/) | 7 |
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

**✅ Stage 0 complete (22/22).** **✅ Stage 1 complete (55/55)** — the whole language.
**✅ Stage 2 complete (24 days + 4 additions)** — Linux, Git, clean code, testing, tooling, debugging.
**✅ Stage 3 complete (28/28)** — a backend from a socket to a security review.
**✅✅ Stage 4 COMPLETE (56/56)** — FastAPI to a deployed, load-tested service; Django and DRF taught
comparatively; the same endpoint in both, and the twelve invariants underneath.
**✅✅ Stage 5 COMPLETE (28/28)** — Postgres from pages to PITR, Redis from the event loop to
Streams, MongoDB from the document model to the shard key. Almost none of it was about a vendor.
**🌐 Stage 6 (214–247)** is the frontend, and is *not* written as day files — it is a phase guide in
[`03-Web-Developer`](../../03-Web-Developer/), worked in order. Day numbering resumes at 248.
⚡ **Stage 7 in progress (38/50)** — ✅ **the flagship is complete (248–287)**: design and gates,
a deployed skeleton, the backend end to end, everything that runs where nobody is watching it fail,
and the production block — gates, the image, the pipeline, infrastructure, observability, load
testing, and a readiness gate whose standard is *defensible*, not perfect.
**✅✅ Days 001–213, 248–270 and 281–295 written.** 🌐 **Days 271–280** are the flagship's frontend
and are *not* day files — they are worked in [`03-Web-Developer`](../../03-Web-Developer/).
⚡ Next: **296–297** — auditing `ticketed` with everything you have learned since, and the Stage 7
exit gate: two defensible projects.

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
