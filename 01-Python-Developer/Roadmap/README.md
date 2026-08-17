# Python Developer — Day-by-Day Roadmap

The Python SDE path. **Same skeleton as the [Java track](../../02-Java-Developer/Roadmap/README.md),
Python flesh** — the day numbers line up stage for stage, so the common SDE material (networking,
databases, LLD, system design, DevOps, cloud, distributed systems, interview conversion) is the same
curriculum, and only the language and framework stages differ.

| | |
|---|---|
| **Total** | 341 days to Complete SDE (Stages 0–12), + Stage 15 conversion |
| **Written lessons** | [`../Days/`](../Days/) — one file per day. **Days 001–072 written** — ✅ **Stage 0 complete**; 🔵 **Stage 1 in progress (50/55)**. See the [Days index](../Days/) |
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
| 23–77 | [Stage 1 — Python Mastery](#stage-1--python-mastery) | Interview-grade Python |
| 78–101 | [Stage 2 — Professional Engineering](#stage-2--professional-engineering) | Git, Linux, clean code, pytest |
| 102–129 | [Stage 3 — Backend, framework-free](#stage-3--backend-engineering-framework-free) | Can build an API with no framework |
| 130–185 | [Stage 4 — FastAPI & Django](#stage-4--fastapi--django) | Backend interview-ready |
| 186–213 | [Stage 5 — Databases](#stage-5--database-engineering) | Can fix a slow query live |
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
| 073 | ⭐ CPython internals — bytecode, `dis`, the frame stack, why Python is slow and where |
| 074 | ⭐ Modules, packages, imports — `sys.path`, circular imports, `__init__.py`, namespace packages |
| 075 | ⭐⭐ **Packaging with `uv` and `pyproject.toml`** — virtualenvs, lockfiles, dependency resolution |
| 076 | The stdlib worth knowing — `pathlib`, `datetime`, `json`, `logging`, `subprocess`, `re` |
| 077 | 🚪 **Stage 1 exit gate** — the whole language, out loud |

**🚪 Exit gate** — explain the GIL, the data model and the event loop with no hedging · implement a
decorator, a context manager, a descriptor and an async pipeline from scratch · make a threaded
program fail, explain why, and fix it three ways · pass `mypy --strict` on 500 lines of your own code

---

## Stage 2 — Professional Engineering

> Same curriculum as the [Java track](../../02-Java-Developer/Roadmap/README.md#stage-2--professional-engineering),
> with Python tooling.

**Days 078–101** — Git properly (rebase, bisect, reflog, the commit history as evidence) · the Linux
you actually need (processes, signals, permissions, pipes, `strace`, `lsof`) · shell scripting ·
clean code and naming · **pytest deep** (fixtures, parametrize, monkeypatch, markers, `conftest`) ·
test doubles and when mocking is a design smell · **coverage vs mutation testing** · property-based
testing with Hypothesis · **Ruff, mypy, pre-commit** · logging done properly · debugging with `pdb`
and `py-spy` · profiling (`cProfile`, `line_profiler`, `memray`) · code review as a skill

---

## Stage 3 — Backend Engineering (framework-free)

> **Why:** If you learn FastAPI before you learn HTTP, you will never know which parts are HTTP and
> which parts are FastAPI — and every interview probes exactly that seam.

**Days 102–129** — sockets in Python, by hand · parsing an HTTP request from bytes · **writing your
own micro-framework**: routing, path params, middleware, request/response objects · **WSGI** — the
synchronous contract that Django and Flask still speak · **ASGI** — the async contract FastAPI
speaks, and why it exists · serving with Gunicorn and Uvicorn workers · **authentication from
scratch** — sessions, then JWT with refresh rotation · authorisation and RBAC · input validation and
why it belongs at the boundary · content negotiation, compression, caching headers · idempotency
keys · rate limiting · pagination · error contracts · API versioning · OpenAPI by hand · **the
security block**: OWASP top ten in Python, SQL injection, SSRF, deserialisation (`pickle` is not a
data format), secrets handling

---

## Stage 4 — FastAPI & Django

> **Why two frameworks:** Python backend roles split roughly evenly. Knowing only one halves your
> market, and the second one takes a fraction of the time once the first is deep.

### 4A — FastAPI, deep (130–169)

Application structure and dependency injection · **Pydantic v2** — validation, serialisation,
`model_config`, validators, and why `response_model` is a security control · async routes and the
event loop in production · **SQLAlchemy 2.0 async** — sessions, unit of work, relationships, and the
**N+1 problem** · Alembic migrations · repository pattern and where it earns its keep · background
tasks vs **Celery** · Redis caching · WebSockets in FastAPI · testing with `TestClient` and
`httpx.AsyncClient` · dependency overrides · settings with `pydantic-settings` · structured logging
and request IDs · middleware and exception handlers · file uploads and presigned URLs · **the
production checklist**

### 4B — Django + DRF (170–185)

The Django philosophy and where it beats FastAPI · the ORM (and its N+1s) · `select_related` vs
`prefetch_related` · migrations · the admin as a genuine product feature · **Django REST Framework**
— serialisers, viewsets, permissions, throttling · Django async (and its limits) · when to choose
Django over FastAPI, argued honestly

**🚪 Exit gate** — the same API implemented in both frameworks, with a written comparison of what
each made easy and what each made hard

---

## Stage 5 — Database Engineering

> Same curriculum as the [Java track](../../02-Java-Developer/Roadmap/README.md#stage-5--database-engineering),
> with SQLAlchemy and `asyncpg` instead of JPA.

**Days 186–213** — relational modelling and normalisation · SQL to a professional level · **indexes
and B-trees** · `EXPLAIN ANALYZE` and reading a query plan · joins and the planner · transactions
and **isolation levels** · locking and deadlocks · connection pooling (and **why pool size × worker
count is a number you must compute**) · N+1 detection · migrations under load · Postgres specifics
(JSONB, partial indexes, `FOR UPDATE SKIP LOCKED`) · Redis as a data structure server · NoSQL and
when it is the right answer · **the slow-query clinic**

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
