# Python Developer — Day-by-Day Roadmap

The Python SDE path. **Same skeleton as the [Java track](../../02-Java-Developer/Roadmap/README.md),
Python flesh** — the day numbers line up stage for stage, so the common SDE material (networking,
databases, LLD, system design, DevOps, cloud, distributed systems, interview conversion) is the same
curriculum, and only the language and framework stages differ.

| | |
|---|---|
| **Total** | 341 days to Complete SDE (Stages 0–12), + Stage 15 conversion |
| **Written lessons** | [`../Days/`](../Days/) — one file per day. **Days 001–213, 248–270 and 281–461 written** — ✅ **Stages 0–5 complete**; 🌐 **Stage 6 is a phase guide, not day files**; ✅✅ **Stages 7–14 COMPLETE — ✅✅ COMPLETE SDE + ships own work + cloud deployed + senior-track + ✅✅ AI ENGINEER**; ⚡ **Stage 15 next**. See the [Days index](../Days/) |
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
| 214–247 | Stage 6 — Frontend → [`03-Web-Developer`](../../03-Web-Developer/) 🌐 | Can build the UI for your own API |
| 248–297 | [Stage 7 — Projects](#stage-7--full-stack-integration) ⚡ | Two defensible projects |
| 298–321 | [Stage 8 — LLD](#stage-8--architecture--low-level-design) | LLD rounds cleared |
| 322–359 | [Stage 9 — System Design](#stage-9--system-design) ✅ | ✅✅ **COMPLETE SDE** |
| 360–379 | [Stage 10 — DevOps](#stage-10--devops) ✅ | ✅ **Ships own work** |
| 380–397 | [Stage 11 — AWS](#stage-11--cloud-aws) ✅ | ✅ **Cloud deployed** |
| 398–413 | [Stage 12 — Distributed Systems](#stage-12--distributed-systems) ✅ | ✅ **Senior-track conversations** |
| 414–461 | ✅✅ **Stage 13/14 — AI Engineering** — the model, prompting, retrieval, agents, safety, production | ⭐⭐ **The second market, and the one hiring** |
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

> ⭐⭐ **The Stage 7 rule: no AI-generated code.** AI for explanation always; AI writing your code
> never. You cannot defend a decision you did not make or a line you did not write, and the projects
> exist so that you can defend them.

### 7A — Design and the gate (248–252) ⚡ written

| Day | Lesson |
|---|---|
| 248 | 🏗 **Choosing the flagship** — the twelve non-negotiable requirements, three candidates analysed, ⭐⭐ and the out-of-scope list written on day one |
| 249 | 🏗 **Requirements** — ⭐⭐ criteria you can write a failing test for, the concurrent path as a requirement, and a risk register with observable triggers |
| 250 | 🏗 **Domain modelling and the ERD** — ⭐⭐ the aggregate boundary derived from the invariants, the rule that fits in no aggregate, and the invariants table |
| 251 | 🏗 **The API contract** — spec before code, transitions as commands not status patches, ⭐⭐ and the auth matrix as a grid whose empty cells are the bugs |
| 252 | 🚪 **The design review gate** — five review roles, a threat-model grid, the capacity estimate, ⭐⭐ and five ADRs before a line of code |

### 7B — The backend (253–262) ⚡ written

| Day | Lesson |
|---|---|
| 253 | 🏗 **The skeleton** — deployed on day one, ⭐⭐ and four rules made build failures rather than conventions |
| 254 | 🏗 **The domain layer** — ⭐⭐ the state machine as data, time as an input, and a unit suite that runs in under a second |
| 255 | 🏗 **Persistence** — the repository's real justification, projections for reads, ⭐⭐ and a query-count ceiling in CI |
| 256 | 🏗 **The service layer** — the transaction boundary as the one thing only it can own, ⭐⭐ and never a network call inside it |
| 257 | 🏗 **The web layer** — ⭐⭐ separate request and response schemas as a security control, and one exception handler |
| 258 | 🔐 **Authentication** — argon2 off the event loop, the four login traps, ⭐⭐ rotation *and* reuse detection |
| 259 | 🔐 **Authorisation** — three questions in three places, ⭐⭐ one predicate shared by list and detail, RLS's two silent failures |
| 260 | 🏗 **The rest of the API** — signed cursors with a tiebreaker, allowlists as a DoS boundary, ⭐⭐ and the operational surface |
| 261 | 🧪 **Testing the backend** — the rollback fixture, ⭐⭐ the four test types that carry the project, and what coverage cannot tell you |
| 262 | 🚪 **The backend review gate** — techniques that stop you confirming your own code, twelve adversarial probes, ⭐⭐ and `known-gaps.md` |

### 7C — Data and async work (263–270) ✅ written

| Day | Lesson |
|---|---|
| 263 | 🏗 **The outbox in anger** — `SKIP LOCKED` relay, at-least-once as the ceiling, ⭐⭐ `payment_unknown` and the reconciliation nobody writes |
| 264 | 🏗 **Background jobs** — one runner out of three replicas, ⭐⭐ the watermark that survives a missed run, and alerting on the absence of success |
| 265 | 🏗 **Async work** — acknowledgements and the two ways to lose it, poison messages, ⭐⭐ and the one line that enqueues a job for a row that does not exist |
| 266 | ⚡ **Caching, measured** — what it costs before it gives you anything, ⭐⭐ the key as a security boundary, and the stampede that arrives at 99.9% hit rate |
| 267 | 🏗 **File uploads** — presigned URLs, the confirm you cannot trust, ⭐⭐ the five checks that are not validation, and deletion as four deletes |
| 268 | 🏗 **Real-time** — the polling arithmetic first, SSE vs WebSockets, the `Origin` check nobody makes, ⭐⭐ and the socket as an optimisation rather than the truth |
| 269 | 🔎 **Search and reporting** — four features called search, the tenant filter that must be in the query, ⭐⭐ and the report that changes when nobody changed any orders |
| 270 | 🚪 **The data and async review gate** — ten failure injections against the deployed system, ⭐⭐ the silence table, and drift counted rather than shrugged at |

> 🌐 **Days 271–280 — the flagship's frontend.** Not day files: worked in
> [`03-Web-Developer`](../../03-Web-Developer/), building the UI for the API just defended.
> Day files resume at **281 — production readiness.**

### 7D — Production, and the flagship finished (281–287) ✅ written

| Day | Lesson |
|---|---|
| 281 | 🧪 **Coverage and quality gates** — patch coverage not global, mutation testing as the honest check, ⭐⭐ and the migration lock check almost nobody has |
| 282 | 📦 **The container image** — the shell-form `CMD` that swallows `SIGTERM`, layer ordering, non-root, ⭐⭐ and the liveness probe that turns a database blip into an outage |
| 283 | 🔁 **The pipeline** — build once and promote the digest, expand/contract, `lock_timeout`, ⭐⭐ and the honest asymmetry between rolling back code and rolling back data |
| 284 | 🏗 **Infrastructure** — the smallest thing that demonstrates the property, egress as the control nobody sets, ⭐⭐ a restore you timed, and a cost you can itemise |
| 285 | 📈 **Observability** — the trace id through six processes including the outbox row, ⭐⭐ why p99 cannot be averaged across pods, and the 3 a.m. test |
| 286 | 💥 **Load testing** — the closed loop that hides the cliff, the four bottlenecks in order, ⭐⭐ and the cascade where perfectly healthy pods take each other down |
| 287 | 🚪 **The production readiness gate** — ⭐⭐ finished means *defensible*: four live demonstrations, 24 attacking questions, and the numbers almost no candidate has |

### 7E — The second project (288–297) ✅ written

> ⭐⭐ **Deliberately a different shape.** The flagship's hard parts had right answers a test could
> assert. Here a wrong answer is fluent, confident and silent — so the test suite becomes an
> evaluation set, and "it works" becomes a number you moved.

| Day | Lesson |
|---|---|
| 288 | 🏗 **The second project** — why a different shape, the rule-versus-retrieval split as the whole architecture, ⭐⭐ and the rule that an LLM may not be in the path of a decision that must be correct |
| 289 | 🏗 **Two data models** — facts versus corpus, effective dating with an `EXCLUDE` constraint, ⭐⭐ and an immutable decision record you can explain six months later |
| 290 | ⚙️ **The rules engine I** — the representation ladder and where to stop, conflict resolution with static analysis in CI, ⭐⭐ and the trace (including `why_not`) as the product |
| 291 | ⚙️ **The rules engine II** — backtesting a change before it ships, shadow mode, ⭐⭐ and the override rate, where both extremes are bad |
| 292 | 🔎 **RAG I — ingestion** — parsing as the ceiling, structural chunking, ⭐⭐ and the ancestry prefix that beats changing embedding models |
| 293 | 🔎 **RAG II — retrieval** — hybrid with RRF because scores aren't comparable, cross-encoder reranking, ⭐⭐ and recall@k as the hard ceiling on the whole system |
| 294 | 🛡️ **RAG III — failure modes** — indirect injection through your own corpus, capability as the only real boundary, ⭐⭐ verified spans, and the cost arithmetic |
| 295 | 📝 **The ADRs you never wrote** — archaeology over constants and reverts, ⭐⭐ the honest format that resists retconning, and the ADR where you were wrong |
| 296 | 🔍 **Auditing `ticketed`** — the nine-question lens, characterisation tests before any change, ⭐⭐ the `CHECK` constraint that makes the bug impossible rather than unlikely, and the triage that stops it becoming a rewrite |
| 297 | 🚪 **STAGE 7 EXIT GATE** — fifty adversarial questions, six live demonstrations, ⭐⭐ and the two-minute comparison showing one discipline producing two different systems |

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

### 8A — Foundations (298–303) ✅ written

| Day | Lesson |
|---|---|
| 298 | 🏛️ **Why architecture exists** — the cost of change as the only justification, coupling and cohesion measured from the git log, connascence as precise vocabulary, ⭐⭐ and the fact that your import graph is not your dependency graph |
| 299 | 🏛️ **Layered architecture** — what layers buy stated as change costs, the four rots (leaky model, anaemic domain, the shortcut, the god service), ⭐⭐ and the import-linter contract that makes the boundary a build failure |
| 300 | 🏛️ **Clean & hexagonal** — the dependency rule and what people misread about it, ports owned by the inside, ⭐⭐ `Protocol` giving zero import coupling in both directions, and an honest list of what it does *not* buy |
| 301 | 🏛️ **The repository pattern** — the three real justifications and the two false ones, why a `Session` isn't one, ⭐⭐ and the read path that bypasses it on purpose |
| 302 | 🏛️ **The service layer** — the paper-form test, the anaemic domain as a correctness problem, cross-aggregate rules, ⭐⭐ and "can a Celery task call this unchanged?" |
| 303 | 🏛️ **Dependency injection by hand** — the composition root, Python's four affordances, ⭐⭐ the module-level engine that makes `pytest --collect-only` open a database, and `mock.patch` count as a design metric |

### 8B — DDD, structure, and the LLD method (304–311) ✅ written

| Day | Lesson |
|---|---|
| 304 | 🧭 **DDD I** — the strategic half nobody copies, the ten-second language test, qualifiers as missing types, ⭐⭐ and value objects that turn a swapped-argument bug into a type error |
| 305 | 🧭 **DDD II** — the aggregate as a contention boundary sized by "what must be true at commit", references by ID, events versus commands, ⭐⭐ and one canonical model serving three teams badly |
| 306 | 🧭 **The modular monolith** — what a network call really costs, the three honest reasons to split, `api.py` as a rehearsal, ⭐⭐ the table-ownership rule no linter can see, and the extraction paradox |
| 307 | 🔧 **Refactoring the flagship** — four measurements taken first, bottom-up before top-down, one refactoring per commit, ⭐⭐ and reporting the number that didn't move |
| 308 | ⏱️ **The LLD method** — the forty-five-minute clock, five questions that each change the design, pruning nouns as a scored skill, ⭐⭐ and the minute-30 rule |
| 309 | 📐 **UML that earns its keep** — multiplicity as the information, the failure sequence rather than the happy path, ⭐⭐ the state diagram nobody draws, and generating it from the transition table |
| 310 | ⚡ **Concurrency in LLD** — shared state, invariant, cheapest mechanism, what the GIL doesn't do, check-then-act as one bug in four costumes, ⭐⭐ and the moment an in-process lock becomes silently wrong |
| 311 | 🅿️ **Parking Lot** — the method end to end: pruning, who owns occupancy, ⭐⭐ check-and-act as one operation with a retry window, and the conditional `UPDATE` that survives two servers |

### 8C — The design problems (312–321) ✅ written

> ⭐⭐ **Nine problems, five shapes.** Hold-act-commit with an expiry · check-and-act as one
> operation · a ledger rather than a balance · the third outcome · a strategy only on the axis that
> varies. Day 321 consolidates them into a pattern card.

| Day | Lesson |
|---|---|
| 312 | 🛗 **Elevator** — hall versus car requests, cars bidding rather than a controller reaching in, ⭐⭐ starvation, and concurrency solved by a single consumer instead of a lock |
| 313 | 🗄️ **LRU cache** — policy as an interface, why doubly linked, `while` not `if`, ⭐⭐ and the stampede that kills the database at a 99.9% hit ratio |
| 314 | 🚦 **Rate limiter** — four algorithms compared honestly, ⭐⭐ the boundary burst that fires exactly when every scheduled client does, atomicity across replicas, and fail-open versus fail-closed |
| 315 | 💸 **Splitwise** — a ledger rather than stored balances, largest-remainder allocation, ⭐⭐ the invariant enforced at construction, and settlement as an admitted heuristic |
| 316 | 🎟️ **BookMyShow** — the three-state seat, one conditional `UPDATE` with lazy expiry, the payment outside the transaction, ⭐⭐ and `payment_unknown` with an extended hold |
| 317 | 🎰 **Vending machine & ATM** — state pattern plus transition table, greedy change and where it breaks, ⭐⭐ write-ahead logging in a vending machine, and hold-dispense-capture |
| 318 | 🔔 **Logging & notifications** — three axes that vary independently, consent checked at send time, ⭐⭐ derived idempotency keys, and an abstraction declined out loud |
| 319 | ♟️ **Chess & Snakes and Ladders** — over-engineering as the test in one and ⭐⭐ make-test-unmake in the other, where pins need no code and the undo record is the game history |
| 320 | 🚕 **Delivery & cab booking** — an offer with an expiry because a driver can decline, batched escalation as a product trade-off, ⭐⭐ cell-boundary search, and 12,500 writes/second going to Redis |
| 321 | 🚪 **STAGE 8 EXIT GATE** — the pattern card, two unseen problems recorded and scored on nine rows, ⭐⭐ and the audio-only test |

---

## Stage 9 — System Design

> Same curriculum as the [Java track](../../02-Java-Developer/Roadmap/README.md#stage-9--system-design).

**Days 322–359** — Little's Law · back-of-the-envelope estimation · SLI/SLO/error budgets · load
balancing · caching at scale and consistent hashing · CDN and object storage · replication and its
three lag anomalies · sharding · **CAP done correctly** and PACELC · consistency models · SQL vs
NoSQL · queues and choreography vs orchestration · Kafka · delivery semantics · backpressure and
deadline propagation · search · observability at scale · resilience and cascading failure ·
microservices and Conway's law · gateways and service mesh · 2PC, sagas and compensation ·
consensus · **the 45-minute framework** · fourteen full designs · 🚪🚪 **COMPLETE SDE**

### 9A — The fundamentals of scale (322–332) ✅ written

| Day | Lesson |
|---|---|
| 322 | 📏 **Scalability, latency and SLOs** — what "scalable" actually asserts, tail amplification, ⭐⭐ Little's Law used three ways, the utilisation curve that explains most outages, and error budgets as arbitration |
| 323 | 🧮 **Estimation** — an estimate is only worth doing when it rules something out, the numbers you memorise, peak factors, ⭐⭐ and one worked example where the answer is "one box" |
| 324 | ↔️ **Vertical vs horizontal** — the scale cube, and ⭐⭐ the four kinds of hidden state, including the module-level dict that becomes twelve divergent caches |
| 325 | ⚖️ **Load balancing** — L4 vs L7 and why gRPC forces L7, six algorithms, ⭐⭐ the health-check death spiral and its four defences |
| 326 | 🗃️ **Caching at scale** — stampede, penetration, avalanche, hot key; probabilistic early expiry; ⭐⭐ consistent hashing derived, and what virtual nodes actually fix |
| 327 | 🌍 **CDN & object storage** — the cache key as a security boundary, content-addressed URLs so purge is never needed, ⭐⭐ the presigned upload, and egress as 4× storage cost |
| 328 | 🔁 **Replication** — three topologies, semi-sync, ⭐⭐ the three anomalies by symptom, and failover's four failure modes including the pool that keeps a dead primary |
| 329 | 🪓 **Sharding** — seven things to try first, the four key questions, six things it takes away, ⭐⭐ pre-splitting into logical shards, and dual-write before backfill |
| 330 | ⚖️ **CAP done correctly** — the three-line proof, the choice as per-operation, ⭐⭐ and PACELC's else-branch, which is the one paid on every request |
| 331 | 🧊 **Consistency models** — the ladder, causal as the strongest available under partition, ⭐⭐ the four session guarantees delivered by a token, and measuring convergence |
| 332 | 🗄️ **SQL vs NoSQL** — five families, ⭐⭐ the real question of *when* you must commit to your queries, single-table design as the aggregate enforced by the engine, and Postgres's five limits |

### 9B — Distribution, messaging and operations (333–343) ✅ written

| Day | Lesson |
|---|---|
| 333 | 📬 **Queues & event-driven** — the delete-every-subscriber test, ⭐⭐ orchestrate the transaction and choreograph the side effects, and five ways queues build a distributed monolith |
| 334 | 🪵 **Kafka** — the log and its four consequences, ⭐⭐ ordering inside a partition and nowhere else, the acks/min-insync lie, the rebalance spiral, and compaction as a table |
| 335 | 🎯 **Delivery semantics** — the four-line impossibility, effectively-once as the right word, ⭐⭐ what Kafka's transactions actually cover, and the dedup window that must outlast a human replay |
| 336 | 🌊 **Rate limiting & backpressure** — local decisions with global truth, ⭐⭐ six unbounded queues hiding in Python, deadlines rather than timeouts, and congestive collapse |
| 337 | 🔎 **Search** — inverted indexes, BM25 as three intuitions, the ranking trap, ⭐⭐ and the derived-index rule with divergence measured rather than assumed |
| 338 | 🔭 **Observability at scale** — ⭐⭐ cardinality as the thing that bankrupts you, tail sampling, four tests an alert must pass, burn-rate windows, and the outbox row that breaks traces |
| 339 | 🧯 **Resilience** — the cascade in eight steps, ⭐⭐ retry budgets rather than counts, bulkheads, static stability, and recovery ramped rather than instant |
| 340 | 🧩 **Microservices vs monolith** — Conway's law, seven costs beyond latency, the three honest reasons, ⭐⭐ and deployment coupling from the git log as the real architecture diagram |
| 341 | 🚪 **Gateway, discovery & mesh** — what belongs at the edge and what never does, header identity as a trust boundary, ⭐⭐ the Python DNS-failover trap, and the honest mesh threshold |
| 342 | 🔗 **Distributed transactions** — 2PC as a blocking protocol, sagas with a pivot, ⭐⭐ compensation as a new visible fact rather than a rollback, and four ways to avoid the problem |
| 343 | 🗳️ **Consensus** — quorum intersection in one sentence, Raft's election restriction, ⭐⭐ the four-step TTL-lock failure and the fencing token that fixes it |

### 9C — The framework and the designs (344–359) ✅ written

> ⭐⭐ **Fourteen designs, eight shapes.** Reserve-then-confirm · precompute vs compute-on-read ·
> cheap filter then expensive rank · ephemeral vs durable · admit don't scale · the log as source of
> truth · derived stores must be rebuildable · recovery must be ramped. Day 359 consolidates them.

| Day | Lesson |
|---|---|
| 344 | ⏱️ **The framework** — five things scored and three that aren't, ⭐⭐ seven moves on a clock, a data model by minute 18, and offering two deep dives rather than being handed one |
| 345 | 🔗 **URL shortener** — the key-length calculation, four generation strategies, 302 for revocation, ⭐⭐ and the click write that is 100× the creation rate |
| 346 | 📋 **Pastebin** — payload 50× the metadata, the inline/S3 hybrid, size pinned in the signature, ⭐⭐ signed CDN URLs, and the dedup side channel |
| 347 | 🚥 **Distributed rate limiter** — why central counting fails on latency *and* the hot key, ⭐⭐ local enforcement with asynchronous allocation, and a graded degradation ladder |
| 348 | 🗝️ **Key-value store** — vnodes and replica placement, tunable quorums, version vectors and siblings, ⭐⭐ LSM trees and the three amplifications you can only optimise two of |
| 349 | 🕷️ **Web crawler** — politeness as the real constraint, ⭐⭐ the two-stage frontier that makes it structural, Bloom filters with the cheap error, and traps you can't out-crawl |
| 350 | 📣 **Notifications at scale** — transactional vs broadcast, consent at send time, chunked fan-out, ⭐⭐ and the self-inflicted DDoS the notification itself causes |
| 351 | 📰 **News feed** — followers-per-poster as the load parameter, ⭐⭐ the hybrid and the reason it's cheap, active-follower filtering, and dynamic thresholds under lag |
| 352 | 💬 **Chat** — the session registry with a TTL, pub/sub per gateway not per user, persist-before-deliver, ⭐⭐ per-conversation sequence numbers, and the read-receipt N² |
| 353 | 🎬 **Video streaming** — it's file serving, aligned keyframes, chunked parallel transcoding, buffer-based ABR, ⭐⭐ and eleven petabytes of egress a day |
| 354 | 📂 **File sync** — content-defined chunking, the per-namespace cursor, ⭐⭐ conflicts detected by parent version and resolved by keeping both, and conservative chunk GC |
| 355 | 🚗 **Ride hailing** — 250k writes/s against 55 requests/s, Redis with a TTL as the health check, geohash with neighbours, ⭐⭐ and matching partitioned by geography |
| 356 | 🎫 **Ticket booking** — a 100× spike with no ramp, ⭐⭐ the virtual waiting room and randomised position, bucketed inventory, and the seat map as advisory |
| 357 | 💳 **Payments** — the append-only double-entry ledger, authorise/capture, idempotency at three layers, the unknown state, ⭐⭐ and reconciliation as a first-class component |
| 358 | ⌨️ **Autocomplete** — a latency-budget problem at 15× search traffic, precomputed top-K, base plus hot layer, ⭐⭐ and the popularity feedback loop that ossifies it |
| 359 | 🚪🚪 **STAGE 9 EXIT GATE** — the eight-shape pattern card, three unseen designs recorded and scored on nine rows, the audio-only test · ✅✅ **COMPLETE SDE** |

---

## Stage 10 — DevOps

**Days 360–379** — containers and what they are not · **container internals** (and the Python-specific
version of the OOMKill and PID-1 signal problem) · images and layers · multi-stage builds, small
Python images, and why dependency layering matters · volumes, networks, config · Compose ·
containerising the flagship, measured · Nginx and the slow client · CI/CD concepts · GitHub Actions
and **OIDC** · a real pipeline · deployment strategies · config and secrets · **Prometheus and
PromQL** · Grafana and SLO burn-rate alerting · centralised logging · **OpenTelemetry** · Kubernetes
I and II · Terraform · 🚪 ships own work

> ⭐⭐ **Stage 10 deepens ground Stage 7 touched (281–287) rather than repeating it**: mechanism
> instead of recipe, operations instead of shipping, and judgement about when each tool is wrong.

### 10A — Containers, properly (360–367) ✅ written

| Day | Lesson |
|---|---|
| 360 | 📦 **Why containers** — a container as ordinary Linux processes with a restricted view, the Python-specific problem they solve (C extensions built against a particular libc and ABI), ⭐⭐ and six things they do not solve |
| 361 | 🔬 **Container internals** — namespaces and cgroups, ⭐⭐ PID 1 ignoring signals, `os.cpu_count()` reporting the host and giving 129 workers in a one-CPU container, exit 137 with no traceback, and CFS throttling as invisible latency |
| 362 | 🧱 **Images and layers** — the cache as a prefix match and the two-line reorder that saves three minutes, ⭐⭐ `RUN rm` writing a whiteout so the secret must be *rotated*, the tag lie, and the Alpine/musl trap |
| 363 | 🏗️ **Multi-stage and scanning** — the venv as the transfer artefact, the `-dev`/runtime package pairing, ⭐⭐ size reframed as attack surface and CVE noise, distroless with both halves, and an actionable scanning policy |
| 364 | 🔌 **Volumes, networks, config** — the three mount types, ⭐⭐ binding `127.0.0.1` as the commonest connection-refused bug, config validated at startup, and why `depends_on` hides a missing retry |
| 365 | 🧬 **Docker Compose** — what it is genuinely excellent at, ⭐⭐ the seven gaps a laptop cannot catch with cheap fixes for five, and the honest production threshold |
| 366 | 🔧 **Containerising the flagship** — seven measured baselines, workers computed from the cgroup and capped by memory, ⭐⭐ migrations out of the entrypoint, and a graceful-shutdown CI test |
| 367 | 🔀 **Nginx** — the slow-client arithmetic, ⭐⭐ `client_body_timeout` as the actual defence, the forwarded-header spoof, and 499 as a latency signal your error rate never shows |

### 10B — Pipelines and delivery (368–372) ✅ written

| Day | Lesson |
|---|---|
| 368 | 🚦 **CI/CD concepts** — the feedback-time budget and three tiers, ⭐⭐ flakiness as multiplicative arithmetic, trunk-based development with flag debt named, and three gates most teams lack |
| 369 | ⚙️ **GitHub Actions** — cache keys and the scoping rule that explains cold PRs, `mode=max` layer caching, OIDC, ⭐⭐ and the wildcard trust policy that is worse than a static key |
| 370 | 🛠️ **A real pipeline** — the migration lock check, ⭐⭐ smoke tests asserting the git sha, deploys that wait for convergence, and a rehearsed break-glass procedure |
| 371 | 🚀 **Deployment strategies** — ⭐⭐ the precondition all of them share, five compatibility requirements, blue-green's rollback caveat, canary's two statistical limits, and flags separating deploy from release |
| 372 | 🔐 **Config and secrets** — ⭐⭐ rotation speed as the real question, where env vars stop being enough, the Postgres one-password-per-role constraint, and config as the riskiest production change |

### 10C — Observability and platform (373–379) ✅ written

| Day | Lesson |
|---|---|
| 373 | 📊 **Prometheus and PromQL** — ⭐⭐ `rate()` before `sum()` and the reset that makes it silently wrong during deploys, histograms over summaries, and the multiprocess bug that makes traffic appear to flap |
| 374 | 📈 **Grafana and SLO alerting** — dashboards with a ten-second test, burn-rate windows with the short window that makes them resolve, ⭐⭐ and alert fatigue as the failure that ends teams |
| 375 | 📜 **Centralised logging** — cost per event rather than per series, ⭐⭐ one wide event per unit of work, contextvars in async code, and error SDKs attaching your environment by default |
| 376 | 🕸️ **OpenTelemetry** — ⭐⭐ the queue and the outbox row as the hops that always break propagation, thread-pool context loss, tail sampling, and the four trace shapes you read instantly |
| 377 | ☸️ **Kubernetes I** — ⭐⭐ the reconciliation loop as the one idea, four objects, three probes with liveness touching no dependency, and the eventually-consistent endpoints behind deploy 502s |
| 378 | ☸️ **Kubernetes II** — termination's parallel race and the `preStop` sleep, disruption budgets and their traps, ⭐⭐ why CPU is the wrong autoscaling metric, and six failures that are not your code |
| 379 | 🚪 **Terraform · STAGE 10 EXIT GATE** — state as the whole story, blast-radius layering, applying the saved plan, ⭐⭐ and five proofs that are demonstrations rather than claims · ✅ **ships own work**

---

## Stage 11 — Cloud (AWS)

**Days 380–397** — the cloud model and **control plane vs data plane** · IAM I and II · VPC I and II
· EC2 and EBS · S3 and its cost traps · RDS and **the failover trap** (Python's is different from
Java's, and it is covered) · ElastiCache · ELB and auto scaling · ECS and Fargate · **Lambda** ·
the edge · SQS/SNS/EventBridge · CloudWatch · KMS and Secrets Manager · ⭐⭐ **cost as an
engineering constraint** · 🚪 the flagship on AWS

> ⭐⭐ **The stage produces six recurring shapes, and they are the transferable content** (Day 397):
> the two planes · **pools outlive DNS** · small things burst and bursting ends · the cache key is a
> security boundary · the managed service's limit is still your limit · **every arrow that crosses a
> boundary has a price**.

### 11A — Foundations (380–386) ✅ written

| Day | Lesson |
|---|---|
| 380 | ☁️ **The cloud model** — regions and AZs with the three latency numbers, ⭐⭐ control plane vs data plane and why your recovery plan may need the thing that is down, static stability, and shared responsibility as *durability theirs, correctness yours* |
| 381 | 🔑 **IAM I** — ⭐⭐ the six-step evaluation order, the bucket-vs-objects trap, tenant-scoped policies with variables, `iam:PassRole` as the escalation nobody reads, and least privilege as a ratchet tightened with evidence |
| 382 | 🎭 **IAM II** — roles as trust plus permission, STS, ⭐⭐ **IMDSv1 SSRF and both v2 mechanisms**, the credential ladder ranked by rotation speed, OIDC with the wildcard that undoes it, and the worker that dies after exactly one hour |
| 383 | 🕸️ **VPC I** — CIDR arithmetic and the five reserved addresses, ⭐⭐ **what actually makes a subnet public** plus both symmetrical corollaries, NAT gateway arithmetic, and the three-tier layout with an honest caveat |
| 384 | 🧱 **VPC II** — stateful vs stateless and the ephemeral-port bug, security groups referencing groups, gateway vs interface endpoints with the break-even, ⭐⭐ **the debugging ladder and the symptom that names the layer** |
| 385 | 🖥️ **EC2 and EBS** — an instance as four budgets, Graviton for Python, ⭐⭐ **the burst-credit trap that degrades you hours after a successful deploy**, the third I/O ceiling, and the savings plan that beats any code change |
| 386 | 🪣 **S3** — not a filesystem, strong consistency with its date, presigned POST with all four pins, ⭐⭐ **four cost traps with arithmetic**, and why 32 threads gave you no speedup |

### 11B — Managed services (387–393) ✅ written

| Day | Lesson |
|---|---|
| 387 | 🐘 **RDS and Aurora** — Multi-AZ vs read replica, ⭐⭐ **the failover where reads work, writes fail and nothing reconnects**, the three settings that fix it, connection arithmetic, RDS Proxy pinning, and the RTO you have not measured |
| 388 | ⚡ **ElastiCache** — ⭐⭐ **the default eviction policy that only evicts keys with a TTL**, cluster mode and CROSSSLOT, `ReadOnlyError` retries, `KEYS *` as an outage, and the one question to ask of every key |
| 389 | ⚖️ **ELB and Auto Scaling** — health-check arithmetic, ⭐⭐ **the 502 keep-alive race and the rule that fixes it**, the three-timeout ordering, and why autoscaling handles the trend but never the spike |
| 390 | 🚢 **ECS and Fargate** — four nouns, the honest Fargate cost comparison, execution vs task role, ⭐⭐ **the circuit breaker that is off by default**, `awsvpc` IP arithmetic, and ECS vs EKS decided by ownership |
| 391 | λ **Lambda** — one request per environment and everything that follows, Python cold starts measured, ⭐⭐ **the spike that killed the database**, the cost curve and its crossover, and the wheel that will not load |
| 392 | 🌍 **The edge** — alias records, CloudFront as a bill reducer, ⭐⭐ **the cache key as a security boundary**, versioned URLs instead of invalidation, `stale-if-error`, and the origin that is still directly reachable |
| 393 | 📬 **SQS, SNS and EventBridge** — three questions rather than three feature lists, ⭐⭐ **visibility timeout as a lease**, the batch-delete bug, SNS→SQS as the default fan-out, and archive-and-replay as a managed rebuildable store |

### 11C — Operating and paying for it (394–397) ✅ written

| Day | Lesson |
|---|---|
| 394 | 🔭 **CloudWatch, X-Ray, CloudTrail** — ⭐⭐ **the custom-metric arithmetic that reaches $4,500/month**, EMF reconciling cardinality with wide events, the alarm default that hides a dead service, and an audit log the attacker cannot delete |
| 395 | 🔏 **KMS and Secrets Manager** — envelope encryption derived, crypto-shredding, ⭐⭐ **the key policy lockout support cannot fix**, KMS throttling on a data job, and rotation as a four-step state machine |
| 396 | 💰 ⭐⭐ **Cost as an engineering constraint** — the feedback loop nobody builds, the elimination ladder ranked by return per hour, **unit economics as the only number that survives growth**, `infracost` in the PR, and cost theatre named |
| 397 | 🚪 **The flagship on AWS · STAGE 11 EXIT GATE** — Well-Architected as questions rather than a certificate, ⭐⭐ **five proofs and six recurring shapes**, and the four-clause sentence the stage buys · ✅ **cloud deployed**

---

## Stage 12 — Distributed Systems

**Days 398–413** — the eight fallacies and the ninth · decomposition · gRPC and protobuf in Python ·
**Kafka deep** (three days) · RabbitMQ and when it beats Kafka · event sourcing · CQRS ·
⭐⭐ **the outbox, CDC and Debezium** · sagas · time and ordering · ⭐⭐ **Raft in detail** ·
distributed locking and the fencing token · failure detection, gossip and split brain · chaos
engineering and load shedding · 🚪 senior-track conversations

> ⭐⭐ **Stage 9 gave you the shapes; Stage 12 gives you the mechanisms and the proofs.** Stage 9 lets
> you say "I'd use a quorum". Stage 12 lets you say **why a quorum is safe, and therefore when it is
> not** — which is the answer that survives a follow-up question.

> ⭐⭐ **The stage produces six recurring shapes, and they are the transferable content** (Day 413):
> the unknown outcome is the whole problem · **ordering is always per-something, never global** ·
> correctness lives in the resource, not the coordinator · every guarantee is a quorum intersection
> or a timing assumption · **derived state must be rebuildable, and you must have rebuilt it** ·
> compensation is a new fact, not an undo.

### 12A — Foundations and transport (398–400) ✅ written

| Day | Lesson |
|---|---|
| 398 | 🕳️ **The eight fallacies** — each with the Python line that embodies it, ⭐⭐ **the ninth (the other end is not the version you tested against)**, the three outcomes of a remote call with only three possible responses, FLP read usefully, and the "waits forever by default" audit |
| 399 | 🧩 **Decomposition** — the three wrong axes, invariants as the right one, four tests with `git log` as the evidence, ⭐⭐ **the arithmetic that makes sync vs async an availability decision**, and enforcing module boundaries in Python with `import-linter` |
| 400 | 📡 **gRPC and protobuf** — ⭐⭐ **the field number is the schema**, so every evolution rule derives itself; the reused-number catastrophe; deadline propagation as the feature REST cannot match; which status codes are retryable; and why an NLB breaks gRPC scaling |

### 12B — Messaging in depth (401–404) ✅ written

| Day | Lesson |
|---|---|
| 401 | 🪵 **Kafka I** — a log with a cursor rather than a queue, partitions as parallelism *and* ordering, ⭐⭐ **`acks=all` with `min.insync.replicas=1` is exactly `acks=1`**, what the idempotent producer does not cover, and the missing `flush()` that drops messages silently during a deploy |
| 402 | 🎣 **Kafka II** — parallelism capped by partitions, the two offset placements and why auto-commit is at-most-once in disguise, ⭐⭐ **the rebalance spiral and its one-line fix**, static membership, time-to-catch-up as the alert, and offsets in your own transaction |
| 403 | 🛡️ **Kafka III** — the ISR as the subject of every guarantee, ⭐⭐ **`unclean.leader.election` as CAP in one boolean**, retention per segment, compaction as the stream–table duality, exactly-once bounded honestly, and the Python stream-processing gap named |
| 404 | 🐰 **RabbitMQ** — broker-side routing, ⭐⭐ **four things per-message acknowledgement buys that Kafka structurally cannot do**, four durability settings of which three loses data, the DLX+TTL retry chain, and Celery's five dangerous defaults |

### 12C — Data patterns (405–408) ✅ written

| Day | Lesson |
|---|---|
| 405 | 📜 **Event sourcing** — retroactive projections as the real benefit, ⭐⭐ **five costs in the order they hurt** including a schema with no deprecation path, crypto-shredding for erasure, four versioning techniques, and a unique index as the entire concurrency mechanism |
| 406 | 🔭 **CQRS and read models** — three levels of commitment and why most teams need the first, ⭐⭐ **projection lag with three fixes, best first**, the rebuild that measures divergence rather than asserting it, and "a projection may be late; it may not be wrong" |
| 407 | 📤 ⭐⭐ **The outbox, CDC and Debezium** — no ordering of the dual write works; the outbox and where its atomicity comes from; **the gap bug that silently skips rows and only appears under concurrency**; the replication slot that fills your disk; and outbox-vs-CDC decided by who consumes it |
| 408 | 🔀 **Sagas** — losing the I and the anomalies that follow, orchestrate the transaction and choreograph the side effects, ⭐⭐ **the pivot transaction and the two rules it produces**, compensation as a new visible fact, and the stuck-saga alarm nobody builds |

### 12D — Coordination and failure (409–413) ✅ written

| Day | Lesson |
|---|---|
| 409 | ⏱️ **Time and ordering** — ⭐⭐ **why last-write-wins silently discards the newer write**, `time.time()` not being monotonic, Lamport's asymmetry, vector clocks as the only ones that *detect* a conflict, hybrid logical clocks, and what Spanner actually did |
| 410 | 🗳️ ⭐⭐ **Consensus — Raft in detail**: terms as a logical clock, randomised timeouts as the assumption that escapes FLP, Log Matching, **the election restriction proved in three lines from quorum intersection**, the figure-8 problem, and why four nodes is worse than three |
| 411 | 🔒 **Distributed locking and leader election** — the four-step failure and seven reasons the pause happens, ⭐⭐ **the fencing token moving correctness into the resource**, Redlock settled without taking a side, and **four ways to not need a lock — starting with a unique constraint** |
| 412 | 💔 **Failure detection, gossip, split brain** — crashed and slow are indistinguishable so you choose your error type, phi-accrual, SWIM's indirect probes, ⭐⭐ **split brain's real damage being that merging is a business problem**, CRDTs with their limits, and the four questions to ask of any failover |
| 413 | 🚪 **Chaos, load shedding · STAGE 12 EXIT GATE** — chaos as falsifying a belief, the fault menu led by 200 ms of latency, **LIFO under overload**, the six senior-track conversations, ⭐⭐ **five proofs and the six shapes** · ✅ **senior-track conversations**

---

## Stage 13/14 — AI Engineering ✅✅

**Days 414–461** — the model honestly · tokens and the context window · the transformer · sampling ·
embeddings · ⭐⭐ **token economics** · prompting as engineering · structured output ·
⭐⭐ **evals as the precondition** · LLM-as-judge · prompts as versioned code · **retrieval in depth**
(nine days) · ⭐⭐ **the permission filter** · agents and tool calling · idempotency without replay ·
⭐⭐ **prompt injection** · guardrails · hallucination mechanics · privacy · fine-tuning · self-hosting ·
serving · caching · observability · fallback · cost · rollout · 🚪 the AI Engineer interview

> ⭐⭐ **An LLM application is a distributed system with a non-deterministic, expensive, untrusted
> component in the middle.** So Stages 9–12 apply unchanged — the ingestion pipeline is a job with
> retries, the index is derived state, the agent run is a durable workflow, event emission is the
> outbox — and **everything this stage adds is a consequence of those three adjectives.**

> ⭐⭐ **The stage produces ten shapes** (Day 461): everything follows from next-token prediction ·
> the token is the unit of everything · **a change you cannot measure is a change you cannot make** ·
> chunking is the ceiling and the free fixes beat the paid ones · **make abstention possible or you
> manufacture fabrication** · constrain at the sampler, validate in ordinary Python · **the permission
> filter fails silently** · instructions and data share one channel with no prepared statement ·
> **a check is not a boundary** · retry no longer replays.

### 13A — The model, honestly (414–421) ✅ written

| Day | Lesson |
|---|---|
| 414 | 🧠 **What an LLM actually is** — next-token prediction and its six consequences: no memory, no lookup, ⭐⭐ **latency linear in *output* and flat in input**, and fluency uncorrelated with correctness |
| 415 | 🔢 **Tokens and the context window** — the unit of cost, latency, context and rate limits; non-English text 2–5× worse; what 128k does *not* mean; ⭐⭐ **the $90k/month arithmetic that is the honest argument for retrieval**; prompt caching and the timestamp-at-the-top mistake |
| 416 | 🏛️ **The transformer and attention** — attention as n², ⭐⭐ **prefill compute-bound and decode memory-bandwidth-bound**, the KV cache, and the context-versus-concurrency tradeoff that becomes a capacity formula in 14C |
| 417 | 🎓 **The training pipeline** — ⭐⭐ **pretraining knows, post-training does**; SFT teaches form not knowledge; preference-tuning artefacts including sycophancy; chat templates as convention rather than a security boundary |
| 418 | 🎲 **Inference and sampling** — temperature versus top-p, ⭐⭐ **`temperature=0` is greedy, not deterministic**, log-probabilities as the only real confidence signal, constrained decoding as a guarantee versus JSON mode as a request |
| 419 | 🧭 **Embeddings** — ⭐⭐ **similarity is not relevance**: questions match questions, negation is invisible, identifiers are destroyed, topical drift is real — and vector search is recall, not precision |
| 420 | 🔌 **The API surface in Python** — the wrapper with eight cross-cutting concerns, ⭐⭐ **async as a 50× capacity difference**, `finish_reason` checking, and cost accounting on every call |
| 421 | 💸 ⭐⭐ **Cost, latency and the token budget** — consumed rather than provisioned, so **a user raises your marginal cost by typing more**; the $22,800/month worked example; the eight-rung ladder; the cascade arithmetic |

### 13B — Prompting as engineering (422–428) ✅ written

| Day | Lesson |
|---|---|
| 422 | ✍️ **Prompt engineering I** — the four pillars, conditioning a distribution as the mental model, ⭐⭐ **abstention as the single most valuable line you write**, and why negative instructions fail mechanically |
| 423 | 🪜 **Prompt engineering II** — few-shot teaches *format*, not answers, and plateaus at five; CoT as buying compute with reasoning-is-not-explanation; ⭐⭐ **decomposition argued on engineering grounds**; the four signs prompting has hit its ceiling |
| 424 | 🧾 **Structured output as schema design** — the schema is read *before* generation, six design rules, ⭐⭐ **a required field manufactures fabrication — and you caused it with a type annotation**; four validation layers; the quote-in-source check; the repair loop as a silent degradation absorber |
| 425 | 📏 ⭐⭐ **Evals** — score not boolean, thirty cases this afternoon, 40/30/20/10 composition, four grader types, and **an aggregate gate with a tolerance band above measured noise** |
| 426 | ⚖️ **LLM-as-judge** — its four failure modes, and ⭐⭐ **measuring human–human agreement first: if two humans agree 70% of the time, no judge beats 70%** |
| 427 | 🏷️ **Prompts as versioned code** — content-hash versions over the whole `TaskConfig`, shadow deploys, and the nightly eval against production config |
| 428 | 🚪 **13B checkpoint** — one prompted feature shipped and measured: five proofs, seven shapes |

### 13C — Retrieval, measured (429–437) ✅ written

| Day | Lesson |
|---|---|
| 429 | 📚 **Why RAG exists** — the three gaps it closes and the one it does not, ⭐⭐ **43× cheaper, 10× faster to first token *and* more accurate**, the two things it does not do, and exact NumPy search as the 100%-recall baseline |
| 430 | ✂️ ⭐⭐ **Chunking is the ceiling** — one object doing two jobs that want opposite sizes; the five strategies ranked; **the contextual prefix as the best ratio in the sub-stage**; tables as the silent killer; the sweep that picks a size instead of an argument |
| 431 | 🧭 **Embeddings in practice** — choosing without trusting MTEB, silent truncation at 512, ⭐⭐ **the query/passage prefixes whose omission costs 10 points with no error**, the content-hash cache, and **the reindex as an alias swap** |
| 432 | 🗄️ **Vector databases, decided honestly** — start with pgvector; what ANN actually trades away and how to measure your recall; ⭐⭐ **pre-filter versus post-filter**; the four pgvector traps; the four honest triggers for leaving |
| 433 | 🎯 **Hybrid search and reranking** — where dense reliably loses, RRF and ⭐⭐ **why you must not add scores**, bi-encoder versus cross-encoder, the measured ladder (0.62 → 0.91), and the rerank floor that lets retrieval return nothing |
| 434 | 🔐 ⭐⭐ **Multi-tenancy and the permission filter** — the security day: **generation launders leaked data, so every normal signal stays silent**; four defence layers; RLS; stale ACLs; deletion; and the canary test |
| 435 | 📐 **RAG evaluation** — ⭐⭐ **two failures with opposite fixes**, recall@k as a ceiling, a gold set built backwards in an afternoon, **the twenty unanswerable questions**, and the debugging table |
| 436 | 🔮 **Advanced retrieval** — query rewriting as table stakes, HyDE and when it hurts, parent-document, contextual retrieval, graph RAG honestly, and ⭐⭐ **the questions that are not retrieval questions at all** |
| 437 | 🚪 **13C build day** — one retrieval service, four invariants, six shapes, five proofs |

### 14A — Agents, bounded (438–445) ✅ written

| Day | Lesson |
|---|---|
| 438 | 🔧 **Tool calling** — ⭐⭐ **the model never calls anything; your for-loop does**, and the tool contract that makes tenant and idempotency non-optional |
| 439 | 🔁 **The agent loop and its budgets** — ⭐⭐ **an agent run is a durable workflow, not a `while` loop**; MAX_STEPS as a correctness boundary; the quadratic transcript |
| 440 | 🩹 **When the agent is wrong** — ⭐⭐ **score the trajectory, gate the action**: repeat detection, doom loops, and gates by reversibility rather than by risk |
| 441 | 🕸️ **Multi-agent systems** — ⭐⭐ **a distributed system wearing a costume**: the four things a second agent buys, the handoff as an untyped RPC, six topologies ranked, and 3–10× the cost |
| 442 | 🧰 **Frameworks, or nothing** — ⭐⭐ **can you print the exact bytes you send?**; adopt what is boring and standard, own what is your product; the four seams that are your exit plan |
| 443 | 🧠 **Memory** — ⭐⭐ **four different problems sharing one word**; why vector memory usually loses to a summary; the prompt-cache tax; and memory as a *persistent* injection vector |
| 444 | ♻️ ⭐⭐ **Idempotency in a non-deterministic system** — **retry no longer replays**, so keys come from the caller at the point of intent; resume replays the log rather than re-deciding; compensation is computed from the log, never by asking the model |
| 445 | 🚪 **14A build day** — one agent, six invariants, proved by chaos rather than claimed |

### 14B — Safety and trust (446–452) ✅ written

| Day | Lesson |
|---|---|
| 446 | 🧨 ⭐⭐ **Prompt injection** — **there is no prepared statement for a prompt**; injection is not jailbreaking; **the lethal trifecta**; the exfiltration channels a system with no send tool still has; and why detection is a speed bump |
| 447 | ☣️ **Output as untrusted input** — ⭐⭐ **the model is an anonymous internet user**: SQL, shell, paths, **SSRF at the metadata endpoint**, markdown rendering as execution, and schema-valid ≠ safe |
| 448 | 🚧 **Guardrails** — ⭐⭐ **a check is not a boundary**; the six free deterministic controls first; the latency and cost economics; and **false positives as a fairness problem** |
| 449 | 👻 **Hallucination mechanics** — ⭐⭐ **the model is working correctly**; five types with five different fixes; the plausibility gradient; confidence signals ranked; and mitigations by *measured* effect |
| 450 | 🔏 **Data privacy** — the seven stores your users' data lands in, ⭐⭐ **traces as the most sensitive store with the weakest controls**, what "we don't train on your data" does not mean, and the erasure test |
| 451 | 🎚️ **Fine-tuning, honestly** — the one case where it clearly wins, why facts is not that case, what LoRA actually is, and ⭐⭐ **the maintenance trap of forking somebody else's model** |
| 452 | 🖥️ **Open models and self-hosting** — ⭐⭐ **the GPU is billed by the hour, not the token**, so the crossover is a utilisation question; vLLM and continuous batching; quantisation; and the hybrid that usually wins |

### 14C — Production (453–461) ✅ written

| Day | Lesson |
|---|---|
| 453 | 🛰️ **Serving** — ⭐⭐ **three-second requests invalidate every default you have**: the sync/async arithmetic, Little's Law and admission control, LIFO under overload, the three ways streaming breaks infrastructure, and why CPU is the wrong autoscaling signal |
| 454 | 🗃️ **Caching** — four layers with four keys; ⭐⭐ **the cache key as a security boundary that *bypasses* the permission filter**; why semantic caching serves wrong answers confidently; and the two metrics nobody has |
| 455 | 🔭 ⭐⭐ **Observability without a correct answer** — **everything returns 200 while broken**, so you monitor distributions and user behaviour; the wide event; the eight metrics that detect real problems; **and the rephrase as the densest quality signal there is** |
| 456 | 🪜 **Reliability and fallback** — the seven-rung degradation ladder, retry budgets, the three streaming timeouts, why a global circuit breaker causes outages here, and ⭐⭐ **an untested fallback is not a fallback** |
| 457 | 💰 ⭐⭐ **Cost engineering** — unit economics before optimisation; **the p99 request is a bug, not a heavy user**; the ladder priced; and the point where cutting cost becomes a product decision |
| 458 | 🚦 **Rate limits and multi-provider** — ⭐⭐ **TPM, not RPM**, so a RAG app hits the ceiling at half a request per second; proactive token accounting; **the 02:00 incident where your eval suite starves your users**; and why prompts are not portable |
| 459 | 🎚️ **Rollout with eval gates** — ⭐⭐ **rank every change by its rollback cost**; shadow as the best value on the ladder; the sample sizes that make most A/B tests noise; and the two kinds of CI gate |
| 460 | 🎤 **The AI Engineer interview** — ⭐⭐ **demo or system?**; the five sentences that mark seniority; the six portfolio artefacts; and the four questions to ask them |
| 461 | 🚪 **STAGE 13/14 EXIT GATE** — ⭐⭐ **ten shapes, eight proofs, six gate conditions**, and an honest statement of what you can and cannot claim · ✅✅ **AI ENGINEER**

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
