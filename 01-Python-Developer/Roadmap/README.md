# Python Developer — Day-by-Day Roadmap

The Python SDE path. **Same skeleton as the [Java track](../../02-Java-Developer/Roadmap/README.md),
Python flesh** — the day numbers line up stage for stage, so the common SDE material (networking,
databases, LLD, system design, DevOps, cloud, distributed systems, interview conversion) is the same
curriculum, and only the language and framework stages differ.

| | |
|---|---|
| **Total** | 341 days to Complete SDE (Stages 0–12), + Stage 15 conversion |
| **Written lessons** | [`../Days/`](../Days/) — one file per day. **Days 001–213 written** — ✅ **Stages 0–5 complete**; ⚡ **Stage 6 next**. See the [Days index](../Days/) |
| **Default stack** | Python 3.12 · uv · FastAPI · Pydantic v2 · SQLAlchemy 2.0 (async) · Alembic · Postgres · Redis · Celery · pytest · Ruff · mypy |
| **Second framework** | Django + DRF (Stage 4B) — because Python backend roles split roughly evenly between the two, and knowing only one halves your market |

---

## The one rule

> **No AI-generated code during lessons or practice.** AI for explanation — always.
> AI writing your code — never.

You are building the thing an interviewer will attack for 45 minutes. Every line has to be yours.

---

## Every day, in addition to the lesson

| | Time | What |
|---|---|---|
| **DSA in Python** | 45–75 min | Plus a pattern journal entry: *what signal told me which pattern?* See [DSA-Parallel-Track.md](DSA-Parallel-Track.md) |
| **🎙️ Articulation drill** | 15 min | Record yourself explaining yesterday's concept for 2 min, as if to an interviewer. Listen back. |
| **Spaced repetition** | 10 min | Notes from days 1, 3, 7, 14, 30 ago |

The articulation drill is the highest-leverage 15 minutes in the day. **Knowledge that can't be spoken
doesn't exist in an interview room.**

---

## Modes

| Mode | When | What happens |
|---|---|---|
| **NEW** | You've never truly understood this | Full lesson, 60–90 min |
| **RECALL** | You genuinely already know it | 5 rapid questions. Pass 4+ → done in 20 min, next lesson same day. Fail → converts to NEW. |

You can never skip a lesson — but you can pass through it fast.

---

## Stages

| Days | Stage | Milestone |
|---|---|---|
| 1–22 | [Stage 0 — Ground Zero](#stage-0--ground-zero) ✅ | Can explain servers, HTTP, WebSockets |
| 23–77 | [Stage 1 — Python Mastery](#stage-1--python-mastery) ✅ | Interview-grade Python |
| 78–101 (+4) | [Stage 2 — Professional Engineering](#stage-2--professional-engineering) ✅ | Git, Linux, clean code, pytest |
| 102–129 | [Stage 3 — Backend, framework-free](#stage-3--backend-engineering-framework-free) ✅ | Can build an API with no framework |
| 130–185 | [Stage 4 — FastAPI & Django](#stage-4--fastapi--django) ✅ | Backend interview-ready |
| 186–213 | [Stage 5 — Databases](#stage-5--database-engineering) ✅ | Can fix a slow query live |
| 214–247 | Stage 6 — Frontend → [`03-Web-Developer`](../../03-Web-Developer/) | Can build the UI for your own API |
| 248–297 | [Stage 7 — Projects](#stage-7--full-stack-integration) | Two defensible projects |
| 298–321 | [Stage 8 — LLD](#stage-8--architecture--low-level-design) | LLD rounds cleared |
| 322–359 | [Stage 9 — System Design](#stage-9--system-design) | ✅ **COMPLETE SDE** |
| 360–379 | [Stage 10 — DevOps](#stage-10--devops) | Ships own work |
| 380–397 | [Stage 11 — AWS](#stage-11--cloud-aws) | Cloud deployed |
| 398–413 | [Stage 12 — Distributed Systems](#stage-12--distributed-systems) | Senior-track conversations |
| 414–461 | Stage 13/14 — Data & AI → [`05-ML-Engineer`](../../05-ML-Engineer/) | Python's second market |
| 462–488 | [Stage 15 — Interview Conversion](#stage-15--interview-conversion) | Offers |

> **Why the numbers match Java.** Stages 0, 2, 5, 8, 9, 10, 11, 12 and 15 are language-independent —
> a load balancer, a B-tree index and a saga do not care what you write. Those stages are the **same
> curriculum**, taught with Python examples. Only Stages 1, 3, 4 and 7 are genuinely different.

---

## Stage 0 — Ground Zero

> **Why:** You have used the web for years without opening it. This stage opens it.
> **After this:** Explain what a server is, what HTTP is, what WebSockets are — no hesitation.

| Day | Lesson | Parallel track |
|---|---|---|
| [001](../Days/Day-001.md) | What a computer is — CPU, RAM, disk, bus · von Neumann · what "running" means | C-01 · Why layering exists · OSI vs TCP/IP · encapsulation |
| [002](../Days/Day-002.md) | Source to execution — **compiled vs interpreted, and where Python actually sits** | C-02 · Physical & data link · MAC · switches · ARP |
| [003](../Days/Day-003.md) | What a process is · what a port is · how two programs stay separate | |
| [004](../Days/Day-004.md) | **What a server actually is** — a program in a loop, bound to a port, calling `accept()` | C-03 · Network layer — IP, subnetting, CIDR, routing, NAT |
| [005](../Days/Day-005.md) | What a client is · request/response as a model · why the web is client-server | C-04 · TCP I — handshake, teardown, seq/ack |
| [006](../Days/Day-006.md) | The Internet — packets, routers, ISPs, how data physically moves | |
| [007](../Days/Day-007.md) | DNS — full resolution from browser cache to root server | C-05 · TCP II — retransmission, sliding window, flow control |
| [008](../Days/Day-008.md) | **What a TCP connection actually is** · TIME_WAIT, CLOSE_WAIT | C-06 · TCP III — congestion control (slow start, AIMD, BBR) |
| [009](../Days/Day-009.md) | TLS / HTTPS — what encryption protects, certificates, the handshake | |
| [010](../Days/Day-010.md) | HTTP anatomy — request line, methods, headers, body, status families | C-07 · UDP — what you gain and lose |
| [011](../Days/Day-011.md) | HTTP evolution — 1.0 → 1.1 → 2 → 3 (QUIC) | |
| [012](../Days/Day-012.md) | **What HTML actually is** — a document, a tree, the DOM | C-08 · DNS internals — recursive vs iterative, records, TTL |
| [013](../Days/Day-013.md) | What CSS actually is — box model, cascade, specificity, layout | C-09 · HTTP on the wire — framing, HPACK, QPACK |
| [014](../Days/Day-014.md) | What JavaScript is in a browser — engine, call stack, **event loop** (you meet this again in asyncio) | |
| [015](../Days/Day-015.md) | Browser rendering pipeline — parse → DOM → CSSOM → layout → paint → composite | C-10 · Crypto primitives — hashing vs encryption vs signing |
| [016](../Days/Day-016.md) | REST — what Fielding actually said · vs RPC vs GraphQL | C-11 · Sockets — the API under every server |
| [017](../Days/Day-017.md) | **WebSockets** — why polling fails, upgrade handshake, frames, full-duplex | |
| [018](../Days/Day-018.md) | WebSockets vs SSE vs long-polling — the decision table | C-12 · Proxies, reverse proxies, load balancers (L4/L7), CDN |
| [019](../Days/Day-019.md) | Where state lives — cookies, sessions, localStorage, tokens | C-13 · Network debugging — ping, traceroute, dig, `curl -v`, tcpdump |
| [020](../Days/Day-020.md) | Same-origin policy and CORS — why the browser blocks you, preflight | |
| [021](../Days/Day-021.md) | What deployment actually does — build, artefacts, static hosting, CDN, edge | C-14 · Networks interview drill — 40 rapid-fire |
| [022](../Days/Day-022.md) | 🚪 **Capstone** — trace one full request end to end through every layer | |

**🚪 Exit gate** — a raw TCP server in Python using `socket` only, speaking HTTP by hand · a
WebSocket chat across two browser tabs with the server hand-written · a 10-minute oral on *"what
happens when you type google.com"* · explain HTML vs server to a non-technical person

---

## Stage 1 — Python Mastery

> **Why:** Python is your DSA language *and* your backend language. Every hour pays twice.
> **After this:** Explain the data model, the GIL and the event loop without hedging, and write
> Python that a senior reviewer would not rewrite.

### The object model and memory (023–032)

| Day | Lesson |
|---|---|
| [023](../Days/Day-023.md) | **Everything is an object** — `PyObject`, type vs class, `id()`, `type()`, `isinstance` |
| [024](../Days/Day-024.md) | ⭐ **Names, bindings and references** — assignment binds a name; there are no variables |
| [025](../Days/Day-025.md) | ⭐⭐ **Mutability** — the mutable default argument, aliasing, `copy` vs `deepcopy` |
| [026](../Days/Day-026.md) | Reference counting, `sys.getrefcount`, and why CPython frees immediately |
| [027](../Days/Day-027.md) | ⭐ The **cyclic garbage collector** — generations, `gc` module, when it actually runs |
| [028](../Days/Day-028.md) | Memory: object overhead, `__slots__`, interning, small-int cache |
| [029](../Days/Day-029.md) | ⭐ **Truthiness, identity vs equality** — `is` vs `==`, `__eq__`/`__hash__` contract |
| [030](../Days/Day-030.md) | Scope and the LEGB rule · `global`, `nonlocal` · closures over loop variables |
| [031](../Days/Day-031.md) | ⭐⭐ **The data model** — dunder methods as the whole language's interface |
| [032](../Days/Day-032.md) | `__getattr__` vs `__getattribute__`, descriptors, `property` |

### Built-in types, done properly (033–041)

| Day | Lesson |
|---|---|
| [033](../Days/Day-033.md) | ⭐ `list` internals — over-allocation, amortised append, why `insert(0, x)` is O(n) |
| [034](../Days/Day-034.md) | `tuple`, `namedtuple`, and when immutability buys you something |
| [035](../Days/Day-035.md) | ⭐⭐ **`dict` internals** — open addressing, compact dicts, insertion order, resize |
| [036](../Days/Day-036.md) | ⭐ `set` and `frozenset` — hashing, set algebra as an algorithm |
| [037](../Days/Day-037.md) | ⭐ **Strings** — immutability, interning, encoding, `str` vs `bytes`, why `+=` in a loop is O(n²) |
| [038](../Days/Day-038.md) | `collections` — `deque`, `defaultdict`, `Counter`, `ChainMap` |
| [039](../Days/Day-039.md) | `heapq`, `bisect`, `array` — the stdlib that replaces hand-written data structures |
| [040](../Days/Day-040.md) | ⭐ Slicing, unpacking, star-args, and the walrus operator |
| [041](../Days/Day-041.md) | Numbers — int arbitrary precision, float traps, `decimal`, `fractions` |

### Functions, iteration and functional Python (042–052)

| Day | Lesson |
|---|---|
| [042](../Days/Day-042.md) | Functions as objects · `*args`/`**kwargs` · keyword-only and positional-only params |
| [043](../Days/Day-043.md) | ⭐ **Closures** and the cell mechanism · late binding |
| [044](../Days/Day-044.md) | ⭐⭐ **Decorators** — the pattern, `functools.wraps`, decorators with arguments |
| [045](../Days/Day-045.md) | Class and static methods, `classmethod` factories, `functools.partial` |
| [046](../Days/Day-046.md) | ⭐ **Iterators and the iteration protocol** — `__iter__`/`__next__`, `StopIteration` |
| [047](../Days/Day-047.md) | ⭐⭐ **Generators** — lazy evaluation, memory, `yield from` |
| [048](../Days/Day-048.md) | Generators as coroutines — `send`, `throw`, `close` (the ancestor of `async`) |
| [049](../Days/Day-049.md) | ⭐ `itertools` — the composable iteration toolkit |
| [050](../Days/Day-050.md) | Comprehensions and generator expressions — and when a loop is clearer |
| [051](../Days/Day-051.md) | `functools` — `lru_cache`, `cached_property`, `reduce`, `singledispatch` |
| [052](../Days/Day-052.md) | ⭐ **Context managers** — `with`, `__enter__`/`__exit__`, `contextlib`, `ExitStack` |

### OOP, typing and errors (053–063)

| Day | Lesson |
|---|---|
| [053](../Days/Day-053.md) | Classes, instances, `__init__` vs `__new__`, class vs instance attributes |
| [054](../Days/Day-054.md) | ⭐ **Inheritance and the MRO** — C3 linearisation, `super()` in multiple inheritance |
| [055](../Days/Day-055.md) | ⭐ Composition over inheritance · mixins · when multiple inheritance is defensible |
| [056](../Days/Day-056.md) | ⭐⭐ **ABCs and protocols** — nominal vs **structural** typing, `Protocol`, duck typing formalised |
| [057](../Days/Day-057.md) | ⭐ `dataclasses` — `frozen`, `slots`, `field`, and when to reach for Pydantic instead |
| [058](../Days/Day-058.md) | `enum`, `NamedTuple`, `TypedDict` — modelling closed sets and shapes |
| [059](../Days/Day-059.md) | ⭐⭐ **Exceptions** — the hierarchy, custom exceptions, `raise ... from`, exception groups |
| [060](../Days/Day-060.md) | ⭐ Error handling design — EAFP vs LBYL, what to catch, what to never catch |
| [061](../Days/Day-061.md) | ⭐⭐ **Type hints that earn their keep** — generics, `TypeVar`, variance, `Optional` vs `\|` |
| [062](../Days/Day-062.md) | ⭐ **mypy in strict mode** — what it catches, what it cannot, `Any` as a defect |
| [063](../Days/Day-063.md) | Operator overloading, `__repr__` vs `__str__`, and designing a class people can debug |

### Concurrency — the stage that decides Python interviews (064–072)

| Day | Lesson |
|---|---|
| [064](../Days/Day-064.md) | ⭐ Processes, threads and the OS scheduler — the model everything below assumes |
| [065](../Days/Day-065.md) | ⭐⭐ **THE GIL** — what it actually locks, why it exists, what it does and does not prevent |
| [066](../Days/Day-066.md) | ⭐⭐ **Threading** — when it genuinely helps (I/O), `Lock`, `RLock`, `Event`, `Condition` |
| [067](../Days/Day-067.md) | ⭐ Race conditions in Python — `+=` is not atomic; what *is* atomic and why that is fragile |
| [068](../Days/Day-068.md) | ⭐⭐ **`multiprocessing`** — true parallelism, pickling costs, shared memory, fork vs spawn |
| [069](../Days/Day-069.md) | `concurrent.futures` — the executor abstraction that unifies both |
| [070](../Days/Day-070.md) | ⭐⭐ **asyncio I** — the event loop, coroutines, `await`, and why it is not threads |
| [071](../Days/Day-071.md) | ⭐⭐ **asyncio II** — tasks, gather, timeouts, cancellation, `TaskGroup` |
| [072](../Days/Day-072.md) | ⭐⭐ **The blocking call in an `async def`** — the single most damaging Python backend bug |

### Runtime, packaging and the ecosystem (073–077)

| Day | Lesson |
|---|---|
| [073](../Days/Day-073.md) | ⭐ CPython internals — bytecode, `dis`, the frame stack, why Python is slow and where |
| [074](../Days/Day-074.md) | ⭐ Modules, packages, imports — `sys.path`, circular imports, `__init__.py`, namespace packages |
| [075](../Days/Day-075.md) | ⭐⭐ **Packaging with `uv` and `pyproject.toml`** — virtualenvs, lockfiles, dependency resolution |
| [076](../Days/Day-076.md) | The stdlib worth knowing — `pathlib`, `datetime`, `json`, `logging`, `subprocess`, `re` |
| [077](../Days/Day-077.md) | 🚪 **Stage 1 exit gate** — the whole language, out loud |

**🚪 Exit gate** — explain the GIL, the data model and the event loop with no hedging · implement a
decorator, a context manager, a descriptor and an async pipeline from scratch · make a threaded
program fail, explain why, and fix it three ways · pass `mypy --strict` on 500 lines of your own code

---

## Stage 2 — Professional Engineering

> Same curriculum as the [Java track](../../02-Java-Developer/Roadmap/README.md#stage-2--professional-engineering),
> with Python tooling.

> **Why:** Stage 1 made you good at the language. **This stage makes you someone other people can work
> with** — and every one of these is assumed by every job and taught by none.

### Linux and the shell (078–083)

| Day | Lesson | Parallel track |
|---|---|---|
| [078](../Days/Day-078.md) | Filesystem hierarchy, permissions, users, links — and **why your container writes root-owned files** | D-01 · Why databases exist · the layers of a DBMS |
| [079](../Days/Day-079.md) | ⭐ **The shell** — expansion, pipes, redirection, exit codes, and ⭐⭐ why quoting is the whole language | |
| [080](../Days/Day-080.md) | ⭐⭐ **Text processing** — `grep`, `sort`, `uniq`, `awk`, `sed`, `find`, `xargs`: a log file into an answer | |
| [081](../Days/Day-081.md) | ⭐⭐ **Processes and signals** — `SIGTERM` vs `SIGKILL`, graceful shutdown, systemd | D-02 · The relational model — tuples, domains, keys, NULL |
| [082](../Days/Day-082.md) | ⭐⭐ **Observing a live process** — `/proc`, `lsof`, `ss`, `strace`, `py-spy`, and the diagnostic ladder | |
| [083](../Days/Day-083.md) | Bash — ⭐⭐ `set -euo pipefail`, `trap`, `shellcheck`, and when to stop writing bash | |

### Git, properly (084–088)

| Day | Lesson | Parallel track |
|---|---|---|
| [084](../Days/Day-084.md) | ⭐⭐ **The Git object model** — blob, tree, commit, ref · **build a commit by hand** with plumbing | D-03 · ER modelling — entities, cardinality, ER → relational |
| [085](../Days/Day-085.md) | ⭐ **The three trees** — the index, `add -p`, commit messages, `.gitignore`, and ⭐⭐ **the committed secret** | |
| [086](../Days/Day-086.md) | ⭐⭐ **Branching, the three-way merge, merge vs rebase** — and interactive rebase before review | |
| [087](../Days/Day-087.md) | ⭐⭐ **Conflicts, `reflog`, `reset`/`revert`/`restore`, `bisect`** — nothing committed is ever lost | D-04 · Functional dependencies · closure · candidate keys · minimal cover |
| [088](../Days/Day-088.md) | Remotes and remote-tracking refs · trunk-based vs git-flow · ⭐⭐ **PR discipline** | |

### Code people can live with (089–091)

| Day | Lesson | Parallel track |
|---|---|---|
| [089](../Days/Day-089.md) | Clean code — naming, function size, ⭐ **command-query separation**, and when a comment is a failure | |
| [090](../Days/Day-090.md) | ⭐⭐ **Project structure and seams** — `src` layout, layering, import direction, and the circular import as a design bug | |
| [091](../Days/Day-091.md) | ⭐ **Refactoring** — the smells catalogue, characterisation tests, safe steps, **the strangler fig** | D-05 · Normalization — 1NF→BCNF · deliberate denormalization |

### Testing (092–098A)

| Day | Lesson | Parallel track |
|---|---|---|
| [092](../Days/Day-092.md) | 🧪 **Testing philosophy** — the pyramid and the trophy, FIRST, flaky tests, ⭐⭐ behaviour not implementation | |
| [093](../Days/Day-093.md) | 🧪 **pytest I** — discovery, assertion rewriting, ⭐⭐ **fixtures as dependency injection**, `conftest.py`, scope | |
| [094](../Days/Day-094.md) | 🧪 **pytest II** — ⭐⭐ `parametrize`, `raises`, markers, `monkeypatch`, `caplog`, and the config that earns trust | |
| [095](../Days/Day-095.md) | 🧪 **Test doubles** — the five kinds, `autospec`, fakes and contract tests, ⭐⭐ **when mocking is a design smell** | D-06 · Relational algebra — the mental model behind SQL |
| [096](../Days/Day-096.md) | 🧪 **TDD** — a rate limiter built test-first, and ⭐ an honest account of where TDD does not help | |

| [097](../Days/Day-097.md) | 🧪 **The hard parts** — async and `ASGITransport`, the transaction-rollback DB fixture, time, randomness, HTTP stubs and contract tests | D-07 · SQL I — `SELECT`, `NULL`, `GROUP BY`, and the logical order of operations |
| [098](../Days/Day-098.md) | 🧪 ⭐⭐ **Coverage vs mutation testing** — what coverage cannot see, and Goodhart's law in a CI config | |
| [098A](../Days/Day-098A.md) | ➕ 🧪 **Hypothesis** — properties, ⭐⭐ **shrinking**, and the six shapes that make properties findable | |

### Tooling, observability and review (099–101)

| Day | Lesson | Parallel track |
|---|---|---|
| [099](../Days/Day-099.md) | ⭐⭐ **Ruff, mypy, pre-commit** — the automated reviewer, gradual mypy adoption, and where the real gate is | |
| [099A](../Days/Day-099A.md) | ➕ **Logging done properly** — structured logs, ⭐⭐ **request IDs via `ContextVar`**, levels, and what must never be logged | |

| [100](../Days/Day-100.md) | ⭐⭐ **Debugging as a methodology** — reproduce, "what changed?", falsifiable hypotheses, `pdb` and post-mortem | D-08 · SQL II — every join type, semi/anti joins, **the fan-out bug** |
| [100A](../Days/Day-100A.md) | ➕ **Profiling** — `cProfile` (`tottime` vs `cumtime`), `line_profiler`, `py-spy`, `memray`, ⭐⭐ **the optimisation ladder** | |
| [100B](../Days/Day-100B.md) | ➕ **Code review, both sides** — the review order, the checklist, and ⭐⭐ how to say it | |
| [101](../Days/Day-101.md) | 🚪 **Documentation, ADRs, and the Stage 2 exit gate** — six answers, four artefacts, a 28-question audit | |

**🚪 Exit gate** — a repo with the full toolchain from empty · the Git exercise done cold (plumbing
commit, reflog recovery, `bisect run`, the force-push disaster reproduced) · the Linux diagnostic set
(hung process, fd leak, OOM, `SIGTERM` ignored) with **your own numbers** · one real bug taken through
every practice in the stage, end to end

---

## Stage 3 — Backend Engineering (framework-free)

> **Why:** If you learn FastAPI before you learn HTTP, you will never know which parts are HTTP and
> which parts are FastAPI — and every interview probes exactly that seam.

### The server, from a socket up (102–106)

| Day | Lesson |
|---|---|
| [102](../Days/Day-102.md) | 🌐 **HTTP for backends** — safe/idempotent/cacheable, status codes as a contract, ⭐⭐ `ETag`/`If-Match` and the **lost update**, `Cache-Control` and the `Vary` leak |
| [103](../Days/Day-103.md) | ⭐⭐ **Sockets by hand** — `bind`/`listen`/`accept`, `SO_REUSEADDR`, ⭐⭐ **TCP is a stream**, the three ways to frame, slowloris |
| [104](../Days/Day-104.md) | **Parsing HTTP from bytes** — the leftover buffer, header rules, chunked encoding, parser limits, ⭐⭐ **request smuggling** |
| [105](../Days/Day-105.md) | **Building the response** — the three ways to end a body, keep-alive, draining, streaming, ⭐⭐ **the hang** |
| [106](../Days/Day-106.md) | ⭐⭐ **Concurrency for your server** — thread per connection, a bounded pool, and **`selectors`: asyncio's engine, by hand** |

### The framework, and the two contracts (107–113)

| Day | Lesson |
|---|---|
| [107](../Days/Day-107.md) | **Routing and path params** — routes as data, ⭐⭐ **match path then method** (404 vs 405), the ordering trap, trailing-slash redirects |
| [108](../Days/Day-108.md) | **Request/Response objects** and ⭐⭐ **middleware** — the onion, why the order is a correctness property, the body-consumption trap |
| [109](../Days/Day-109.md) | **WSGI** — the whole spec in one page, streaming for free, and ⭐⭐ **why WebSockets are impossible** |
| [110](../Days/Day-110.md) | **ASGI** — `scope`/`receive`/`send`, ⭐⭐ one protocol for HTTP, WebSockets and **lifespan** |
| [111](../Days/Day-111.md) | **Serving in production** — worker counts derived, `--preload` hazards, ⭐⭐ **why nginx protects your workers**, liveness vs readiness |

| [112](../Days/Day-112.md) | 🚪 **The micro-framework capstone** — startup validation, MRO exception handlers, and ⭐⭐ **`Depends` demystified** by signature inspection |
| [113](../Days/Day-113.md) | **Testing the framework** — WSGI and ASGI test clients you write yourself, ⭐⭐ **one suite run against both adapters**, and the in-process blind spot |

### Authentication from scratch (114–118)

| Day | Lesson |
|---|---|
| [114](../Days/Day-114.md) | 🔐 **Passwords and login** — argon2id and memory-hardness, the timing oracle, ⭐⭐ **account enumeration in five places**, backoff over lockout |
| [115](../Days/Day-115.md) | 🔐 **Sessions from scratch** — `secrets` vs `random`, cookie attributes, ⭐⭐ **session fixation**, and CSRF from first principles |
| [116](../Days/Day-116.md) | 🔐 **JWT from scratch** — sign/verify by hand, ⭐⭐ **`alg=none` and HS/RS confusion**, and the revocation problem |

| [117](../Days/Day-117.md) | 🔐 **Refresh-token rotation** — hashed storage, token families, ⭐⭐ **reuse detection**, and the concurrent-refresh race |
| [118](../Days/Day-118.md) | 🔐 **Authorisation and RBAC** — ⭐⭐ **IDOR as a query-shape problem**, permissions over roles, deny-by-default, tenant isolation |

### API design (119–124)

| Day | Lesson |
|---|---|
| [119](../Days/Day-119.md) | **Input validation at the boundary** — ⭐⭐ **parse, don't validate**, mass assignment, unknown fields, and `response_model` as a security control |
| [120](../Days/Day-120.md) | **Error contracts** — problem details, ⭐⭐ **branch on `code`, read `detail`**, the 4xx/5xx leak rule, retryability |
| [121](../Days/Day-121.md) | **Pagination** — offset's two defects, ⭐⭐ **keyset with a tie-breaker**, opaque cursors, and the `ORDER BY` injection trap |

| [122](../Days/Day-122.md) | ⭐⭐ **Idempotency keys** — scoping, body fingerprints, the **atomic claim**, and agreeing with the side effect |
| [123](../Days/Day-123.md) | **Rate limiting** — five algorithms, ⭐⭐ **the fixed-window 2× burst**, the `X-Forwarded-For` trap, fail-open |
| [124](../Days/Day-124.md) | **Caching, negotiation, compression** — ⭐⭐ **four caches and the one that leaks**, invalidation, stampedes, BREACH |

### Contracts and security (125–129)

| Day | Lesson |
|---|---|
| [125](../Days/Day-125.md) | **API versioning and evolution** — ⭐⭐ **what actually breaks**, expand/migrate/contract, `Sunset` headers, transformers |
| [126](../Days/Day-126.md) | **OpenAPI by hand** — spec-first vs code-first, ⭐⭐ **making the spec load-bearing**, and what a schema cannot say |
| [127](../Days/Day-127.md) | 🔒 **Security I** — ⭐⭐ **the injection principle**, SQL, XSS and CSP, and **SSRF** |
| [128](../Days/Day-128.md) | 🔒 **Security II** — ⭐⭐ **`pickle` is RCE by design**, path traversal, secrets and rotation, supply chain |
| [129](../Days/Day-129.md) | 🚪 **Stage 3 exit gate** — six answers, four artefacts, a 32-question audit |

**🚪 Exit gate** — an HTTP server written from a socket, with three concurrency models benchmarked ·
a micro-framework running under **both** gunicorn and uvicorn, with one test suite against both
adapters · authentication built **and attacked** (`alg=none`, session fixation, IDOR, refresh reuse) ·
a production API with idempotency, keyset pagination, rate limiting, an error contract and a written
security review

---

## Stage 4 — FastAPI & Django

> **Why two frameworks:** Python backend roles split roughly evenly. Knowing only one halves your
> market, and the second one takes a fraction of the time once the first is deep.

### 4A — FastAPI, deep (130–169) ✅

| Day | Lesson |
|---|---|
| [130](../Days/Day-130.md) | ⚡ **FastAPI** — Starlette + Pydantic + signature inspection, parameter resolution, exception handlers, and what it does *not* give you |
| [131](../Days/Day-131.md) | **Pydantic v2** — the Rust core, `Field` over validators, strict vs lax coercion, serialisation, ⭐⭐ **three models per resource** |
| [132](../Days/Day-132.md) | **Dependency injection** — the graph and its per-request cache, `yield` teardown timing, ⭐⭐ **overrides instead of patching** |
| [133](../Days/Day-133.md) | **Application structure** — routers, `pydantic-settings`, the app factory, lifespan, ⭐⭐ **the domain never imports FastAPI** |
| [134](../Days/Day-134.md) | ⭐⭐ **`def` vs `async def`** — the 40-thread pool, **the 50× regression**, `run_in_threadpool`, and when sync is right |
| [135](../Days/Day-135.md) | **SQLAlchemy 2.0** — the unit of work, autoflush, session per request, ⭐⭐ `expire_on_commit` and detached objects |
| [136](../Days/Day-136.md) | ⭐⭐ **The N+1 problem** — `joinedload` vs `selectinload`, fan-out, `lazy="raise"`, query-count assertions |
| [137](../Days/Day-137.md) | **Async SQLAlchemy** — the greenlet bridge, ⭐⭐ **lazy loading is gone**, session concurrency, migration order |
| [138](../Days/Day-138.md) | **Alembic** — what autogenerate misses, ⭐⭐ the lock queue, safe vs unsafe DDL, **expand/contract**, batched backfills |
| [139](../Days/Day-139.md) | **The repository pattern** — the real justification, who owns the transaction, ⭐⭐ **the fake-repository trap** |
| [140](../Days/Day-140.md) | 🔐 **Auth in FastAPI** — ⭐⭐ **dependency not middleware**, the chain, cookie vs bearer, `def` on login, scopes vs ownership |
| [141](../Days/Day-141.md) | **Testing** — `TestClient` vs `httpx.AsyncClient`, ⭐⭐ **the savepoint fixture**, overrides, **query-count ceilings** |
| [142](../Days/Day-142.md) | **Background work** — `BackgroundTasks` vs Celery, at-least-once, poison pills, ⭐⭐ **the transactional outbox** |
| [143](../Days/Day-143.md) | **Redis** — structures, cache-aside, ⭐⭐ **the stampede and single-flight**, invalidation, eviction, locks |
| [144](../Days/Day-144.md) | **WebSockets** — the 101, ticket auth, ⭐⭐ **Redis fan-out**, backpressure, heartbeats, **and when SSE wins** |
| [145](../Days/Day-145.md) | **File uploads** — streaming, the four limit gates, filename and type as lies, ⭐⭐ **presigned URLs** |
| [146](../Days/Day-146.md) | **Structured logging** — events not sentences, ⭐⭐ **request IDs via `ContextVar`**, log once at the boundary, redaction as a control |
| [147](../Days/Day-147.md) | **Metrics and SLOs** — RED and USE, histograms vs summaries, ⭐⭐ **cardinality**, error budgets, burn-rate alerts |
| [148](../Days/Day-148.md) | **Distributed tracing** — spans and `traceparent`, propagation through queues, ⭐⭐ **tail sampling**, joining the pillars |
| [149](../Days/Day-149.md) | **Health checks & shutdown** — ⭐⭐ **liveness must check nothing a restart can't fix**, the `preStop` race, PID 1 |
| [150](../Days/Day-150.md) | **Config and secrets** — config as a parsed type, ⭐⭐ **fail fast at boot**, where secrets leak, rotation with overlap |
| [151](../Days/Day-151.md) | **Containerising Python** — ⭐⭐ **layer caching as an ordering problem**, multi-stage, reproducibility, PID 1, non-root |
| [152](../Days/Day-152.md) | **Resilience** — the timeout hierarchy, ⭐⭐ **the retry storm and metastable failure**, circuit breakers, bulkheads, load shedding |
| [153](../Days/Day-153.md) | **Rate limiting in production** — rate vs quota vs fairness, atomic distributed counters, the 429 contract, ⭐⭐ **fail open or closed** |
| [154](../Days/Day-154.md) | **CI/CD** — ⭐⭐ **build once, promote the artefact**, stage ordering, cache keys, flaky tests, semantic conflicts |
| [155](../Days/Day-155.md) | **Deployment strategies** — rolling vs blue-green vs canary, ⭐⭐ **deploy ≠ release**, feature flags, one-way doors |
| [156](../Days/Day-156.md) | **Load testing** — open vs closed models, ⭐⭐ **coordinated omission**, the knee, Little's Law, soak and stress |
| [157](../Days/Day-157.md) | **Profiling live** — `py-spy` on a running process, reading flame graphs, ⭐⭐ **the memory-leak playbook**, a worked incident |
| [158](../Days/Day-158.md) | **Webhooks** — the delivery pipeline, HMAC signing and replay, receiving rules, ⭐⭐ **SSRF and DNS rebinding** |
| [159](../Days/Day-159.md) | **Scheduled jobs** — where the schedule lives, lock vs idempotency key, DST bugs, ⭐⭐ **watermarks and dead-man alerts** |
| [160](../Days/Day-160.md) | **Multi-tenancy** — three models, ⭐⭐ **row-level security and its two silent failures**, tenant resolution, noisy neighbours |
| [161](../Days/Day-161.md) | **Event-driven architecture** — events vs commands, payload styles, schema evolution, ⭐⭐ **ordering you cannot have**, sagas |

**🏗 The capstone (162–169) — `ticketed`, an event ticketing API**, chosen because it forces a real
race, money, a third party, multi-tenancy and a traffic spike to appear naturally.

| Day | Lesson |
|---|---|
| [162](../Days/Day-162.md) | 🏗 **The design** — scope and its out-list, the data model, spec-first OpenAPI, ⭐⭐ **the threat model as an actor table**, five ADRs |
| [163](../Days/Day-163.md) | 🏗 **The walking skeleton** — ⭐⭐ **deployed on day one**, CI green, one empty endpoint with logs, metrics, traces and probes |
| [164](../Days/Day-164.md) | 🏗 **The domain** — holds as a short lock replacing a long one, ⭐⭐ **three defences against oversell**, and the 200-thread test |
| [165](../Days/Day-165.md) | 🏗 **Auth** — the chain, object-level scoping, RLS, login's four traps, ⭐⭐ **a test file with no overrides** |
| [166](../Days/Day-166.md) | 🏗 **Async work** — the relay with `SKIP LOCKED`, the worker, ⭐⭐ **`payment_unknown` and reconciliation**, webhooks both ways |
| [167](../Days/Day-167.md) | 🏗 **Observability & hardening** — four panels, five alerts, ⭐⭐ **a deliberate N+1 caught by your own tooling**, the hardening pass |
| [168](../Days/Day-168.md) | 🏗 **Load test & tune** — predict with Little's Law, find the knee, ⭐⭐ **one measured fix with a named cause**, behaviour past the knee |
| [169](../Days/Day-169.md) | 🚪 **Stage 4A exit gate** — the artefacts, ⭐⭐ **a 40-question audit**, the ten-minute walkthrough, and what you'd do differently |

**🚪 Exit gate** — a deployed service that never oversells under 200 concurrent buyers · a retried
purchase that charges once, proven · every "must not" in the threat model with a test number · a
load-test result stated as before/change/after/cause · and a ten-minute walkthrough delivered
without notes, ending on a **stated ceiling** rather than a claim


### 4B — Django + DRF (170–185) ✅

> **Taught comparatively.** You have already built all of this by hand, so every day names the
> problem you solved in Stage 4A, shows Django's different default, and asks what that default costs.

| Day | Lesson |
|---|---|
| [170](../Days/Day-170.md) | 🎸 **Django** — two philosophies stated fairly, the vocabulary translated, ⭐⭐ **`Model.objects` and the loss of query locality**, signals judged |
| [171](../Days/Day-171.md) | 🎸 **The ORM I** — what evaluates a QuerySet, the result cache, ⭐⭐ **`select_related` vs `prefetch_related` derived from row multiplication**, `assertNumQueries` |
| [172](../Days/Day-172.md) | 🎸 **The ORM II** — ⭐⭐ **autocommit means your view is not atomic**, `atomic`, `on_commit`, `F()`, `select_for_update`, and the bulk ops that skip signals and validation |
| [173](../Days/Day-173.md) | 🎸 **Migrations** — the DAG, `sqlmigrate`, ⭐⭐ **`apps.get_model` and historical models**, concurrent indexes, `makemigrations --check` |
| [174](../Days/Day-174.md) | 🎸 **Views, URLs, middleware** — CBVs and the MRO trap, middleware order as correctness, sessions, ⭐⭐ **CSRF and why it doesn't apply to header tokens** |
| [175](../Days/Day-175.md) | 🎸 **DRF I** — serializers as forms for APIs, ⭐⭐ **`__all__` and mass assignment**, four levels of validation, why nested writes are refused |
| [176](../Days/Day-176.md) | 🎸 **DRF II** — auth classes, ⭐⭐ **`has_object_permission` is not called on `list`**, throttling's four limits, cursor pagination, filtering as a DoS vector |
| [177](../Days/Day-177.md) | 🎸 **The admin** — weeks of work in twenty lines, ⭐⭐ **it edits the database, not your domain**, the N+1 machine pointed at production, actions that call services |

| [178](../Days/Day-178.md) | 🎸 **Django async** — what is genuinely async, ⭐⭐ **the ORM is a thread-pool façade**, `SynchronousOnlyOperation`, Channels |
| [179](../Days/Day-179.md) | 🎸 **Deploying Django** — settings as validated values, ⭐⭐ **static vs media and the stored-XSS trap**, gunicorn and connection maths, Celery |
| [180](../Days/Day-180.md) | 🎸 **Testing Django** — ⭐⭐ **the three things `TestCase` silently breaks**, factories over fixtures, the assertions that catch Django bugs |
| [181](../Days/Day-181.md) | 🎸 **Performance & caching** — the toolbar and silk, four cache levels, ⭐⭐ **`cache_page` serves Bob Alice's dashboard**, indexes, the order of operations |
| [182](../Days/Day-182.md) | ⚖️ **The same endpoint, both frameworks** — built line by line, ⭐⭐ **and ~80% of the difficulty was identical** |
| [183](../Days/Day-183.md) | ⚖️ **Choosing and migrating** — four product questions, why rewrites fail, ⭐⭐ **the strangler's five rules**, and the middle grounds |
| [184](../Days/Day-184.md) | ⚖️ **What actually transfers** — ⭐⭐ **the twelve invariants** true in any framework, and the ten questions to ask about an unfamiliar one |
| [185](../Days/Day-185.md) | 🚪 **Stage 4 exit gate** — proofs vs claims, ⭐⭐ **a 50-question audit**, two recorded walkthroughs, an honest self-assessment |

**🚪 Exit gate** — the same API implemented in both frameworks, with a written comparison of what
each made easy and what each made hard

---

## Stage 5 — Database Engineering ✅

> Same curriculum as the [Java track](../../02-Java-Developer/Roadmap/README.md#stage-5--database-engineering),
> with SQLAlchemy and `asyncpg` instead of JPA.

**Days 186–213.** ⭐⭐ *Stage 4 taught you to work* around *the database — pool sizes, N+1s,
migration locks, `EXPLAIN` at arm's length. This stage opens it.*

### 5A — Postgres, from the inside (186–193) ⚡ written

| Day | Lesson |
|---|---|
| 186 | **Postgres as a running program** — one process per connection, the 8 KB page, shared buffers, WAL, checkpoints. ⭐⭐ Why every fact in this stage is about pages |
| 187 | **Types and constraints** — `timestamptz`, NULL's three-valued logic, partial unique indexes, ⭐⭐ the `EXCLUDE` constraint that ends double bookings |
| 188 | **Schema design in practice** — normalisation from its anomalies, ⭐⭐ why an order line copies the price, and the full bill for soft deletes |
| 189 | ⭐ **Indexes I** — the B-tree and its fanout arithmetic, ⭐⭐ the eight reasons yours is ignored, and what an index costs on writes |
| 190 | **Indexes II** — ⭐⭐ composite order derived (E-S-R), partial indexes, `INCLUDE`, and GIN/GiST/BRIN/trigram |
| 191 | ⭐ **`EXPLAIN ANALYZE`** — ⭐⭐ the deepest bad estimate, `loops` as the misread number, spills, and `auto_explain` |
| 192 | **Join algorithms** — nested loop, hash, merge and why the planner chooses; ⭐⭐ the `LEFT JOIN` that silently isn't, and row multiplication |
| 193 | 🔧 **Query optimisation workshop** — ⭐⭐ ten slow queries against the clock, and the finding that only three were index problems |

### 5B — Transactions and operations (194–201) ⚡ written

| Day | Lesson |
|---|---|
| 194 | ⭐ **MVCC, vacuum and bloat** — why `UPDATE` writes a new row, ⭐⭐ the three things that block vacuum, and transaction ID wraparound |
| 195 | ⭐ **Isolation levels** — every anomaly reproduced in two windows, ⭐⭐ write skew, and why raising the level without retries makes things worse |
| 196 | ⭐ **Locks and deadlocks** — ⭐⭐ the FIFO queue behind "the migration took the site down", the FK lock nobody expects, and `pg_blocking_pids()` |
| 197 | **Pooling and PgBouncer** — both halves of the arithmetic, ⭐⭐ and the seven things transaction mode breaks (one of them a security bug) |
| 198 | **JSONB** — GIN vs expression indexes and the statistics problem, ⭐⭐ write amplification, and the ORM mutation that silently writes nothing |
| 199 | **Full-text search** — the four problems called "search", ranking that doesn't scale, ⭐⭐ and the dual-write problem you buy by leaving |
| 200 | **Partitioning** — ⭐⭐ a maintenance feature, the partition key that must be in every unique constraint, and the midnight outage |
| 201 | ⭐ **Replication, failover and PITR** — ⭐⭐ the three lag anomalies, `hot_standby_feedback`'s hidden cost, and why a replica is not a backup |

### 5C — Tuning, and Redis (202–209) ⚡ written

| Day | Lesson |
|---|---|
| 202 | ⭐ **`pg_stat_statements` and tuning** — rank by total not mean, ⭐⭐ wait events, twelve settings derived · 🚪 **Postgres block gate** |
| 203 | **Redis architecture** — ⭐⭐ why single-threaded is the feature, the banned commands, and the fork that causes most outages |
| 204 | ⭐ **Data structures** — ⭐⭐ five meanings of a sorted-set score, bitmap and HyperLogLog arithmetic, encodings and the bucketing trick |
| 205 | **Persistence** — RDB, AOF and both; ⭐⭐ the durability ceiling in one sentence, and why persistence does not protect a failover |
| 206 | ⭐ **Caching patterns and eviction** — ⭐⭐ delete-don't-update with its proof, three stampede defences, and why hit ratio is a vanity metric |
| 207 | ⭐ **Distributed locks** — four bugs built one at a time, ⭐⭐ fencing tokens, and the Redlock argument from both sides |
| 208 | ⭐ **Rate limiting** — four algorithms in Lua with their arithmetic, ⭐⭐ the boundary burst, and whose clock to trust |
| 209 | **Pub/Sub and Streams** — ⭐⭐ the pending entries list, `XAUTOCLAIM`, and choosing between Streams, Kafka and `SKIP LOCKED` · 🚪 **Redis block gate** |

### 5D — MongoDB and the gate (210–213) ⚡ written

| Day | Lesson |
|---|---|
| 210 | **The document model** — ⭐⭐ you model the queries, not the data; and the honest bar for adding a second database next to Postgres JSONB |
| 211 | ⭐ **Embedding vs referencing** — three questions, the unbounded-array failure in full, ⭐⭐ and cache-vs-snapshot per duplicated field |
| 212 | **Indexes and aggregation** — ⭐⭐ ESR again because B-trees have no vendor; `$lookup` as an N+1 with nice syntax; `$unwind` as row multiplication |
| 213 | ⭐ **Transactions, replica sets, sharding** — tunable consistency, causal sessions, ⭐⭐ the shard key's three properties · 🚪🚪 **Stage 5 exit gate** |

> ⭐⭐ **What the stage actually teaches:** B-trees, equality-sort-range, row multiplication,
> at-least-once delivery, snapshot-versus-current, and "measure before you fix" hold in all three
> systems. What changes between them is which wrong thing is the default.

---

## Stage 7 — Full Stack Integration

**Days 248–297** — two projects, every line written by you.

- **248–287 · The flagship**: a multi-tenant SaaS API in FastAPI — requirements, domain model, API
  contract before code, JWT with refresh rotation, RBAC and tenancy, SQLAlchemy without N+1,
  **the transactional outbox**, Celery jobs, caching measured before and after, uploads via presigned
  URLs, real-time, search, then the production block: coverage gates, the container image, the
  pipeline, infrastructure, observability, **load testing and chaos**
- **288–297 · The second project**: a retrieval-and-rules system — deterministic rule engine,
  backtesting, RAG ingestion and chunking, hybrid search and evaluation, **prompt injection**,
  hallucination and cost control, and the ADR you never wrote

**🚪 Exit gate** — a 45-minute project deep dive where every architectural decision is attacked

---

## Stage 8 — Architecture & Low-Level Design

> Same curriculum as the [Java track](../../02-Java-Developer/Roadmap/README.md#stage-8--architecture--low-level-design),
> written in Python.

**Days 298–321** — change cost and connascence · layered architecture and its rots · clean/hexagonal
and the dependency rule · repository vs DAO · **dependency injection without a framework** (Python
makes this easier than Java, and that is itself a lesson) · DDD — value objects, aggregates as the
contention boundary · the modular monolith · **the 45-minute LLD clock** · UML that earns its keep ·
concurrency in LLD · **eleven design problems**: parking lot, elevator, LRU cache, rate limiter,
Splitwise, ticket booking, vending machine, notifications, games, delivery, orders — plus the
pattern card that unifies them

---

## Stage 9 — System Design

> Same curriculum as the [Java track](../../02-Java-Developer/Roadmap/README.md#stage-9--system-design).

**Days 322–359** — Little's Law · back-of-the-envelope estimation · SLI/SLO/error budgets · load
balancing · caching at scale and consistent hashing · CDN and object storage · replication and its
three lag anomalies · sharding · **CAP done correctly** and PACELC · consistency models · SQL vs
NoSQL · queues and choreography vs orchestration · Kafka · delivery semantics · backpressure and
deadline propagation · search · observability at scale · resilience and cascading failure ·
microservices and Conway's law · gateways and service mesh · 2PC, sagas and compensation ·
consensus · **the 45-minute framework** · fifteen full designs · 🚪🚪 **COMPLETE SDE**

---

## Stage 10 — DevOps

**Days 360–379** — containers and what they are not · **container internals** (and the Python-specific
version of the OOMKill and PID-1 signal problem) · images and layers · multi-stage builds, small
Python images, and why `pip install` layering matters · volumes, networks, config · Compose ·
containerising the flagship, measured · Nginx and the slow client · CI/CD concepts · GitHub Actions
and **OIDC** · a real pipeline · deployment strategies · config and secrets · **Prometheus and
PromQL** · Grafana and SLO burn-rate alerting · centralised logging · **OpenTelemetry** · Kubernetes
I and II · Terraform · 🚪 ships own work

---

## Stage 11 — Cloud (AWS)

**Days 380–397** — the cloud model and **control plane vs data plane** · IAM I and II · VPC I and II
· EC2 and EBS · S3 and its cost traps · RDS and **the DNS-caching failover trap** (Python's is
different from Java's, and it is covered) · ElastiCache · ELB and auto scaling · ECS and Fargate ·
**Lambda** — where Python is genuinely the best runtime, and the cold-start story that makes it so ·
the edge · SQS/SNS/EventBridge · CloudWatch · KMS and Secrets Manager · ⭐⭐ **cost as an engineering
constraint** · 🚪 the flagship on AWS

---

## Stage 12 — Distributed Systems

**Days 398–413** — the eight fallacies and the ninth · decomposition · gRPC and protobuf in Python ·
**Kafka deep** (three days) · RabbitMQ and when it beats Kafka · event sourcing · CQRS ·
⭐⭐ **the outbox, CDC and Debezium** · sagas · time and ordering · ⭐⭐ **Raft in detail** ·
distributed locking and the fencing token · failure detection, gossip and split brain · chaos
engineering and load shedding · 🚪 senior-track conversations

---

## Stage 15 — Interview Conversion

> Same curriculum as the [Java track](../../02-Java-Developer/Roadmap/README.md#stage-15--interview-conversion),
> with Python rounds.

| Days | Focus |
|---|---|
| 462–463 | Resume — claim only what you can defend line by line |
| 464–466 | Behavioural — STAR, the failure story, the conflict story, communication under evaluation |
| 467–471 | Project defence drills — the decision log, the failure interrogation, the code walk |
| 472–476 | Five timed 45-minute DSA mocks **in Python**, narrated aloud, scored |
| 477–480 | Four full LLD rounds in Python |
| 481–485 | Five full system design rounds, scored on structure |
| 486–488 | Company prep, levelling, salary negotiation — and the close |

---

## What is different about interviewing as a Python developer

| | |
|---|---|
| ⭐⭐ **The GIL question is guaranteed** | ⭐ and most candidates answer it wrong — Day 065 |
| ⭐⭐ **"Async or threads?"** is the follow-up | ⭐ Day 070–072 |
| ⭐ **Dynamic typing invites "how do you keep it safe at scale?"** | ⭐ the answer is mypy strict, Pydantic at the boundary, and tests — Days 061–062 |
| ⭐ **DSA in Python is faster to write and slower to run** | ⭐ know your complexity *and* your constant factors — the parallel track covers both |
| ⭐ **You will be asked to justify Python** | ⭐⭐ "we chose it because X, and the cost is Y" — never "it's easy to write" |
