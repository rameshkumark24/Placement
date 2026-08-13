# Java Developer — Day-by-Day Roadmap

The Java SDE path, audited and gap-filled. Day numbers match your Notion roadmap; **added days use
letter suffixes** so nothing renumbers. See [Gap-Audit.md](Gap-Audit.md) for what was added and why.

| | |
|---|---|
| **Total** | 341 days to Complete SDE (Stages 0–12), + Stage 15 conversion |
| **Added** | 11 days ([audit](Gap-Audit.md)) |
| **Written lessons** | [`../Days/`](../Days/) — one file per day. **Days 001–020 written.** |

---

## The one rule

> **No AI-generated code during lessons or practice.** AI for explanation — always.
> AI writing your code — never.

---

## Every day, in addition to the lesson

| | Time | What |
|---|---|---|
| **DSA in Java** | 45–75 min | Plus a pattern journal entry: *what signal told me which pattern?* See [DSA-Parallel-Track.md](DSA-Parallel-Track.md) |
| **🎙️ Articulation drill** | 15 min | Record yourself explaining yesterday's concept for 2 min, as if to an interviewer. Listen back. |
| **Spaced repetition** | 10 min | Notes from days 1, 3, 7, 14, 30 ago |

The articulation drill is the highest-leverage 15 minutes in the day. Knowledge that can't be spoken
doesn't exist in an interview room.

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
| 1–22 | [Stage 0 — Ground Zero](#stage-0--ground-zero) | Can explain servers, HTML, WebSockets |
| 23–77 (+9) | [Stage 1 — Java Mastery](#stage-1--java-mastery) | Interview-grade Java |
| 78–101 (+1) | [Stage 2 — Professional Engineering](#stage-2--professional-engineering) | Git, Linux, clean code, testing |
| 102–129C | [Stage 3 — Backend Concepts](#stage-3--backend-engineering-framework-free) | Can design an API with no framework |
| 130–185B (+1) | [Stage 4 — Spring Boot](#stage-4--spring--spring-boot) | Backend interview-ready |
| 186–213 | [Stage 5 — Databases](#stage-5--database-engineering) | Can fix a slow query live |
| 248–297 | [Stage 7 — Projects](#stage-7--full-stack-integration) | Two defensible projects |
| 298–321 | [Stage 8 — LLD](#stage-8--architecture--low-level-design) | LLD rounds cleared |
| 322–359 | [Stage 9 — System Design](#stage-9--system-design) | ✅ **COMPLETE SDE** |
| 360–379 | [Stage 10 — DevOps](#stage-10--devops) | Ships own work |
| 380–397 | [Stage 11 — AWS](#stage-11--cloud-aws) | Cloud deployed |
| 398–413 | [Stage 12 — Distributed Systems](#stage-12--distributed-systems) | Senior-track conversations |
| 462–488 | [Stage 15 — Interview Conversion](#stage-15--interview-conversion) | Offers |

> Stage 6 (Frontend) → [`03-Web-Developer`](../../03-Web-Developer/) ·
> Stage 13 (Python) → [`01-Python-Developer`](../../01-Python-Developer/) ·
> Stage 14 (AI) → [`05-ML-Engineer`](../../05-ML-Engineer/)

---

## Stage 0 — Ground Zero

> **Why:** You used the web for years without opening it. This stage opens it.
> **After this:** Explain what a server is, what HTML is, what WebSockets are — no hesitation.

| Day | Lesson | Parallel track |
|---|---|---|
| [001](../Days/Day-001.md) | What a computer is — CPU, RAM, disk, bus · von Neumann · what "running" means | C-01 · Why layering exists · OSI vs TCP/IP · encapsulation |
| [002](../Days/Day-002.md) | Source code to execution — compiler vs interpreter, machine code, the loader | C-02 · Physical & data link · MAC · switches · ARP |
| [003](../Days/Day-003.md) | What a process is · what a port is · how two programs stay separate | |
| [004](../Days/Day-004.md) | **What a server actually is** — a program in a loop, bound to a port, calling `accept()` | C-03 · Network layer — IP, subnetting, CIDR, routing, NAT |
| [005](../Days/Day-005.md) | What a client is · request/response as a model · why the web is client-server | C-04 · TCP I — handshake, teardown, seq/ack |
| [006](../Days/Day-006.md) | The Internet — packets, routers, ISPs, how data physically moves | |
| [007](../Days/Day-007.md) | DNS — full resolution from browser cache to root server | C-05 · TCP II — retransmission, sliding window, flow control |
| [008](../Days/Day-008.md) | **What a TCP connection actually is** · TIME_WAIT, CLOSE_WAIT | C-06 · TCP III — congestion control (slow start, AIMD, Reno, BBR) |
| [009](../Days/Day-009.md) | TLS / HTTPS — what encryption protects, certificates, the handshake | |
| [010](../Days/Day-010.md) | HTTP anatomy — request line, methods, headers, body, status families | C-07 · UDP — what you gain and lose |
| [011](../Days/Day-011.md) | HTTP evolution — 1.0 → 1.1 → 2 → 3 (QUIC) | |
| [012](../Days/Day-012.md) | **What HTML actually is** — a document, a tree, the DOM | C-08 · DNS internals — recursive vs iterative, records, TTL |
| [013](../Days/Day-013.md) | What CSS actually is — box model, cascade, specificity, layout | C-09 · HTTP on the wire — framing, HPACK, QPACK |
| [014](../Days/Day-014.md) | What JavaScript is in a browser — engine, call stack, event loop | |
| [015](../Days/Day-015.md) | Browser rendering pipeline — parse → DOM → CSSOM → layout → paint → composite | C-10 · Crypto primitives — hashing vs encryption vs signing |
| [016](../Days/Day-016.md) | REST — what Fielding actually said · vs RPC vs GraphQL | C-11 · Sockets — the API under every server |
| [017](../Days/Day-017.md) | **WebSockets** — why polling fails, upgrade handshake, frames, full-duplex | |
| [018](../Days/Day-018.md) | WebSockets vs SSE vs long-polling — the decision table | C-12 · Proxies, reverse proxies, load balancers (L4/L7), CDN |
| [019](../Days/Day-019.md) | Where state lives — cookies, sessions, localStorage, tokens | C-13 · Network debugging — ping, traceroute, dig, curl -v, tcpdump |
| [020](../Days/Day-020.md) | Same-origin policy and CORS — why the browser blocks you, preflight | |
| 021 | What deployment actually does — build, artifacts, static hosting, CDN, edge | C-14 · Networks interview drill — 40 rapid-fire |
| 022 | Putting it together — trace one full request end-to-end | |

**🚪 Exit gate** — raw TCP server in Python (`socket` only), speaking HTTP by hand · WebSocket chat
across two tabs, server hand-written · 10-min oral on *"what happens when you type google.com"* ·
explain HTML vs server to a non-technical person

---

## Stage 1 — Java Mastery

> **Why:** Java is your DSA language AND your backend language. Every hour pays twice.
> **After this:** Whiteboard HashMap internals and the JMM. Write flawless interview Java.

### JVM & memory
| Day | Lesson | Parallel |
|---|---|---|
| 023 | JVM architecture — class loader, runtime data areas, execution engine, JIT | B-01 · What an OS is · kernel vs user mode · syscalls |
| 024 | `javac` → bytecode → JVM · reading bytecode with `javap` | |
| 025 | Memory model — stack vs heap, method area, string pool location | |
| 026 | Garbage collection — generational hypothesis, minor/major GC, G1 vs ZGC | B-02 · Process — address space, PCB, states, fork/exec/wait |
| 027 | GC tuning, memory leaks, `OutOfMemoryError` diagnosis | |

### Language core
| Day | Lesson | Parallel |
|---|---|---|
| 028 | Primitives vs objects, wrappers, autoboxing, the Integer cache trap | |
| 029 | Strings — immutability, pool, `StringBuilder`, why `+=` in a loop is fatal | B-03 · Threads vs processes · user vs kernel threads |
| 030 | Operators, control flow, switch expressions, `var`, text blocks | |
| 031 | Arrays, 2D arrays, `Arrays` utility, `System.arraycopy` | |
| 032 | Methods, overloading, varargs, pass-by-value (Java has no pass-by-reference) | |
| 033 | `static` — fields, methods, blocks, nested classes, init order | B-04 · Context switching — what is saved, why it costs |
| 034 | `final` — variables, methods, classes · immutability by design | |
| **034A** | ➕ **Immutability & defensive copying** — `final` isn't deep · `List.copyOf` vs `unmodifiableList` | |
| 035 | Classes, objects, constructors, `this`, constructor chaining | |
| 036 | Encapsulation, access modifiers, packages, JPMS overview | B-05 · CPU scheduling — FCFS, SJF, RR, MLFQ, Linux CFS |

### OOP
| Day | Lesson | Parallel |
|---|---|---|
| 037 | Inheritance, `super`, overriding vs hiding, `@Override` | |
| 038 | Polymorphism — static vs dynamic dispatch, the vtable intuition | |
| 039 | Abstract classes vs interfaces · default and static methods | |
| 040 | **`equals()` / `hashCode()` contract** — and exactly what breaks | B-06 · Concurrency — races, critical section, atomicity |
| 041 | `toString`, `Comparable` vs `Comparator`, `Cloneable` and why it's broken | |
| 042 | Records, sealed classes, pattern matching for switch | |
| **042A** | ➕ **`java.time`** — `LocalDate`/`Instant`/`ZonedDateTime`, `Duration` vs `Period`, DST, why `Date` is broken | |
| **042B** | ➕ **`BigDecimal` & floating point** — why `double` is fatal for money, `equals` vs `compareTo`, rounding modes | |
| 043 | Enums — with fields, methods, as singleton/state machine | B-07 · Synchronization — mutex, semaphore, monitor, condvars |
| 044 | Inner classes, static nested, anonymous, local | |

### Collections
| Day | Lesson | Parallel |
|---|---|---|
| 045 | The hierarchy — Collection, List, Set, Queue, Map | |
| 046 | `ArrayList` internals — growth factor, `System.arraycopy`, cost per op | |
| 047 | `LinkedList` internals · when it actually beats `ArrayList` (rarely) | B-08 · Deadlock — 4 conditions, prevention, banker's, detection |
| 048 | ⭐ **`HashMap` internals** — buckets, hash spreading, treeification at 8, resize | |
| 049 | `LinkedHashMap` (LRU via `removeEldestEntry`), `TreeMap` and red-black trees | |
| 050 | `HashSet`, `LinkedHashSet`, `TreeSet`, `EnumMap`, `EnumSet` | B-09 · Memory — contiguous allocation, fragmentation, segmentation |
| 051 | `ArrayDeque`, `PriorityQueue`, `Stack` vs `Deque` | |
| 052 | Iterators, `ConcurrentModificationException`, fail-fast vs fail-safe | |
| 053 | Concurrent collections — `ConcurrentHashMap` internals, `CopyOnWriteArrayList` | B-10 · Virtual memory — paging, page tables, MMU, TLB |

### Generics & functional
| Day | Lesson | Parallel |
|---|---|---|
| 054 | Generics — type parameters, bounded types, generic methods | |
| 055 | Type erasure — what it costs, bridge methods, heap pollution | |
| 056 | Wildcards — `? extends` vs `? super`, the PECS rule | |
| **056A** | ➕ **Reflection & custom annotations** — `Class`, `setAccessible`, `@Retention`/`@Target` · **prerequisite for understanding Spring** | |
| 057 | Lambdas & functional interfaces — Function, Predicate, Supplier, Consumer | B-11 · Demand paging, page faults, thrashing, replacement |
| 058 | Method references, `Optional` used correctly (not as a field) | |
| 059 | Streams I — sources, intermediate vs terminal, laziness | |
| 060 | Streams II — `collect`, Collectors, grouping, parallel streams' real cost | B-12 · File systems — inodes, directories, journaling, VFS |

### Exceptions, I/O
| Day | Lesson | Parallel |
|---|---|---|
| 061 | Exceptions — hierarchy, checked vs unchecked, custom, try-with-resources | |
| 062 | Exception design — when to wrap, when to propagate, never swallow | |
| 063 | I/O and NIO — streams, readers, `Files`, `Path`, buffering | |
| **063A** | ➕ **Java serialization** — `Serializable`, `serialVersionUID`, `transient` · **why it's an RCE hazard**, and why you use JSON | |
| **063B** | ➕ **Regex in Java** — `Pattern`/`Matcher`, groups, greedy vs lazy · **catastrophic backtracking (ReDoS)** | |

### Concurrency
| Day | Lesson | Parallel |
|---|---|---|
| 064 | Threads — creation, lifecycle, `Runnable` vs `Callable`, daemon threads | B-13 · I/O models — blocking, non-blocking, multiplexed (epoll) |
| 065 | ⭐ **The Java Memory Model** — visibility, happens-before, reordering | |
| 066 | `synchronized`, intrinsic locks, `volatile` — and what `volatile` does *not* do | |
| **066A** | ➕ **`ThreadLocal`** — how Spring knows "the current user" · **the thread-pool memory leak** | |
| 067 | `java.util.concurrent` — `ReentrantLock`, `ReadWriteLock`, `Condition` · `wait`/`notify`/`notifyAll` | B-14 · Why epoll matters — the foundation under every async runtime |
| 068 | Atomics, CAS, `AtomicInteger`, the ABA problem | |
| 069 | `ExecutorService`, thread pools, sizing them, `Future`, `CompletableFuture` | |
| 070 | `CountDownLatch`, `Semaphore`, `CyclicBarrier`, producer-consumer | |
| 071 | Deadlock, livelock, starvation — reproduce each one yourself | B-15 · Linux in practice — /proc, ps, top, strace, lsof |
| 072 | Virtual threads (Project Loom) — what changes and what doesn't | |

### Tooling
| Day | Lesson | Parallel |
|---|---|---|
| 073 | Maven & Gradle — dependency management, lifecycle, multi-module | |
| **073A** | ➕ **Logging with SLF4J/Logback** — the facade pattern, levels, appenders, MDC, what never to log | |
| 074 | JUnit 5 — assertions, lifecycle, parameterized tests | B-16 · OS interview drill — 40 rapid-fire |
| 075 | Mockito — mocks, stubs, spies, captors, when mocking is a smell | |
| 076 | Debugging Java — debugger, thread dumps, heap dumps, JFR | |
| **076A** | ➕ **Classpath & class loading in practice** — `ClassNotFoundException` vs `NoClassDefFoundError`, jar hell, `mvn dependency:tree` | |
| 077 | Java interview traps drill — 40 gotcha questions | |

**🚪 Exit gate** — in-memory KV store with TTL, concurrent and thread-safe · custom `ArrayList` and
`HashMap` from scratch · whiteboard `HashMap` internals · explain the JMM and why `volatile` doesn't
make `i++` safe · live-code a thread-safe bounded blocking queue, no reference

---

## Stage 2 — Professional Engineering

| Day | Lesson | Parallel |
|---|---|---|
| 078 | Filesystem hierarchy, paths, permissions, users, chmod/chown | D-01 · Why databases exist · DBMS architecture |
| 079 | Shell fundamentals — pipes, redirection, exit codes, env vars | |
| 080 | Text processing — grep, sed, awk, find, xargs, sort, uniq | |
| 081 | Processes & services — ps, top, kill, signals, systemd | D-02 · Relational model — relations, tuples, domains, keys |
| 082 | Networking & files on Linux — ssh, scp, curl, journalctl | |
| 083 | Bash scripting — variables, loops, functions, `set -euo pipefail` | |
| 084 | **Git object model** — blob, tree, commit, ref · content-addressing | D-03 · ER modelling — entities, cardinality, ER → relational |
| 085 | Staging area, commits, `.gitignore`, commit messages that survive review | |
| 086 | Branching, merge vs rebase, fast-forward, three-way merge | |
| 087 | Conflicts, reflog, reset vs revert vs restore, cherry-pick, bisect | D-04 · Functional dependencies · closure · minimal cover |
| 088 | Remotes, workflows (trunk-based, git-flow), PR discipline | |
| 089 | Clean code — naming, function size, side effects, when comments are a failure | |
| 090 | SOLID I — Single Responsibility, Open/Closed | |
| 091 | SOLID II — Liskov, Interface Segregation, Dependency Inversion | D-05 · Normalization — 1NF→BCNF · deliberate denormalization |
| 092 | DRY, YAGNI, KISS, composition over inheritance, Law of Demeter | |
| 093 | Creational patterns — factory, builder, singleton (and why it's usually wrong) | |
| 094 | Structural patterns — adapter, decorator, facade, proxy, composite | D-06 · Relational algebra — the mental model behind SQL |
| 095 | Behavioural patterns — strategy, observer, command, template method, state | |
| 096 | Refactoring — code smells catalogue, extract method/class, safe refactoring | |
| 097 | Testing philosophy — unit vs integration vs e2e, the pyramid, test doubles | D-07 · SQL I — SELECT, WHERE, ORDER BY, NULL semantics |
| 098 | TDD in practice — red/green/refactor on a real feature | |
| 099 | **Debugging as a methodology** — hypothesis-driven, bisection, stack traces | |
| 100 | Code review — how to give it, how to receive it, what reviewers look for | D-08 · SQL II — all join types, self joins, anti-joins, join order |
| **100A** | ➕ **Dependency & supply chain hygiene** — CVE scanning, lockfiles, transitive deps, licences, slopsquatting | |
| 101 | Documentation — READMEs, ADRs, diagrams that stay true | |

**🚪 Exit gate** — take one AI-generated project, refactor by hand to SOLID, add a real test suite,
write three ADRs, present the diff as a code review

---

## Stage 3 — Backend Engineering (Framework-Free)

> **Why this ordering matters:** learn auth, caching and rate limiting **before** Spring, or you'll
> only ever know Spring's opinion of them.

| Day | Lesson | Parallel |
|---|---|---|
| 102 | HTTP for backend engineers — idempotency, safety, caching headers, conditional requests | |
| 103 | REST API design — resource modelling, URI design, nesting, HATEOAS reality check | |
| 104 | Status codes & error contracts — RFC 7807, consistent error shapes | D-09 · SQL III — GROUP BY, HAVING, aggregates, window functions |
| 105 | Request validation & the trust boundary — never trust the client | |
| 106 | Serialization, content negotiation, JSON pitfalls (dates, floats, big ints) | |
| 107 | Pagination — offset vs cursor vs keyset · why offset breaks at scale | D-10 · SQL IV — subqueries, correlated, CTEs, recursive CTEs |
| 108 | Filtering, sorting, searching, sparse fieldsets | |
| 109 | API versioning — URI, header, media type · deprecation policy | |
| 110 | OpenAPI / Swagger — contract-first vs code-first | D-11 · **Indexes** — B+ tree, hash, clustered vs non-clustered, covering |
| 111 | Authentication vs authorization — the distinction people fail on | |
| 112 | **Password storage** — hashing vs encryption, salt, pepper, bcrypt/argon2, work factors | |
| 113 | Session-based auth — server-side sessions, stores, cookie flags | D-12 · Query processing — parsing, planning, cost estimation, EXPLAIN |
| 114 | **JWT internals** — header/payload/signature, HS256 vs RS256, the `alg:none` attack | |
| 115 | Access vs refresh tokens, rotation, revocation, browser storage | |
| 115b | **API keys** — issuing, hashed storage, scoping, rotation · HMAC signing · mTLS overview | |
| 116 | OAuth 2.0 — the four grants, auth code + PKCE, why implicit died | |
| 117 | OpenID Connect — ID tokens, "Login with Google" | D-13 · **Transactions & ACID** — the four properties, precisely |
| 118 | Authorization models — RBAC, ABAC, ACLs, multi-tenancy | |
| 119 | **API security** — OWASP API Top 10, IDOR, mass assignment, SSRF, CSRF | |
| 119b | **SQL injection in depth** — why concatenation is fatal, prepared statements as the fix | |
| 120 | Caching theory — cache-aside, read-through, write-through, write-behind | D-14 · **Isolation levels & anomalies** — dirty, non-repeatable, phantom, write skew |
| 121 | Cache invalidation, TTL strategy, stampede, stale-while-revalidate | |
| 122 | **Rate limiting** — fixed window, sliding window, token bucket, leaky bucket | |
| 123 | Idempotency keys, retries, exponential backoff with jitter, at-least-once | D-15 · Concurrency control — 2PL, MVCC, optimistic vs pessimistic |
| 124 | Background jobs — why you don't do work in the request cycle | |
| 125 | Task queues & brokers — producer/consumer, ack, DLQ, visibility timeout | |
| 126 | File uploads — multipart, streaming, presigned URLs, object storage | D-16 · NoSQL landscape · CAP intro · polyglot persistence |
| 127 | Structured logging, correlation IDs, log levels, what never to log | |
| 128 | Metrics & health checks — RED/USE method, liveness vs readiness | |
| 129 | Webhooks — delivery guarantees, signature verification, replay protection | |

**🐳 Docker primer** — 129A: why containers, images vs containers, run/ps/logs/exec ·
129B: Dockerfile, layers, build cache, volumes, port mapping ·
129C: docker-compose, Postgres + Redis stack (your dev environment for Stages 4–7)

**🚪 Exit gate** — on paper, no framework: full API contract for a multi-tenant SaaS. Auth flow,
permission model, error format, pagination, rate limits, idempotency. Defend every choice.

---

## Stage 4 — Spring & Spring Boot

<details>
<summary><b>Days 130–185B — expand</b></summary>

**Core container (130–138)** — why Spring exists · **DI** (constructor vs setter vs field) · IoC
container · bean lifecycle · bean scopes and the singleton-with-state bug · component scanning ·
`@Qualifier`/`@Primary`/`@Profile` · **AOP** (JDK vs CGLIB proxies, why self-invocation breaks
`@Transactional`) · SpEL and property resolution

**Boot (139–144)** — starters and opinionated defaults · **auto-configuration internals**
(`@Conditional`, how to debug it) · project structure · configuration and profiles · Actuator ·
DevTools and fat JARs

**Web (145–155)** — **Spring MVC architecture** (DispatcherServlet lifecycle end to end) ·
`@RestController` and mappings · **DTOs vs entities** (never expose your entity) · Bean Validation ·
**global exception handling** · response entities and Jackson control · filters vs interceptors vs
AOP · pagination with `Pageable` · versioning and springdoc · `RestTemplate` vs `WebClient` · CORS

**Data (156–168)** — JDBC and HikariCP · **JPA vs Hibernate vs Spring Data** · entity mapping ·
relationships and owning side · **lazy vs eager, the N+1 problem, `LazyInitializationException`** ·
fetch strategies (join fetch, entity graphs, projections) · persistence context and dirty checking ·
Spring Data repositories · 163b: injection safety in JPA · **`@Transactional`** (propagation,
isolation, rollback rules, common bugs) · auditing and locking · Flyway · second-level cache ·
Spring Data Redis and `@Cacheable`

**Security (169–176)** — **the filter chain** · `AuthenticationManager` and `UserDetailsService` ·
password encoding · stateless APIs, sessions, CSRF · **JWT authentication** (custom filter, refresh
rotation) · `@PreAuthorize` and RBAC · OAuth2 client and resource server · multi-tenancy and common
misconfigurations

**Advanced (177–185B)** — `@Async` · `@Scheduled` and the distributed scheduling problem (ShedLock) ·
application events · WebSockets and STOMP · Resilience4j · structured logging and MDC ·
testing with slices and MockMvc · **Testcontainers** · production (profiles, secrets, graceful
shutdown, JVM flags, Docker) · 185A: Spring Kafka and RabbitMQ ·
**185B ➕ Spring Boot 3 specifics — the Jakarta namespace migration, Micrometer, GraalVM native**

</details>

**🚪 Exit gate** — flagship API by hand: JWT + refresh rotation, RBAC, JPA without N+1, Redis, rate
limiting, async jobs, WebSockets, Flyway, Testcontainers, 80%+ coverage · whiteboard a request
through DispatcherServlet then the security filter chain · explain why `@Transactional` silently
fails on self-invocation · diagnose and fix an N+1 live · empty directory → working JWT auth + CRUD
+ tests in one live session

---

## Stage 5 — Database Engineering

<details>
<summary><b>Days 186–213 — expand</b></summary>

**Postgres (186–202)** — architecture (postmaster, shared buffers, WAL, checkpoints) · data types
and constraints · schema design in practice · **Indexes I** (B-tree, when it's used and when
ignored) · Indexes II (composite column order, partial, covering, GIN/GiST/BRIN) ·
**`EXPLAIN ANALYZE`** · join algorithms · query optimization workshop · transactions, **MVCC**,
vacuum, bloat · isolation levels — reproduce every anomaly yourself · locks and deadlock debugging ·
connection pooling and PgBouncer · JSONB · full-text search · partitioning · replication, failover,
PITR · tuning with `pg_stat_statements`

**Redis (203–209)** — single-threaded event loop · data structures (incl. sorted sets, bitmaps,
HyperLogLog) · RDB vs AOF · caching patterns, key design, eviction · **distributed locks and why
naive SETNX is wrong**, the Redlock debate · rate limiting with sorted sets · Pub/Sub and Streams

**MongoDB (210–213)** — document model, when it fits and when it's a mistake · embedding vs
referencing · indexes and aggregation pipeline · transactions, replica sets, sharding ·
**decision framework: Postgres vs Mongo**

</details>

**🚪 Exit gate** — given a schema and three slow queries, diagnose with `EXPLAIN ANALYZE` and fix
live · defend a Postgres-vs-Mongo choice for a system described to you

---

## Stage 7 — Full Stack Integration

> **⚠️ Every line of both projects is written by you. No AI code. This stage is the whole point.**

| Days | Focus |
|---|---|
| 248–252 | Flagship: requirements, domain modelling, ER diagram, API contract **before any code** |
| 253–262 | Flagship: Spring Boot backend — JWT + refresh rotation, RBAC, domain layer, error contract |
| 263–270 | Flagship: data & async — N+1 solved *and verified*, Redis, jobs, WebSockets, uploads |
| 271–280 | Flagship: frontend ([Stage 6 →](../../03-Web-Developer/)) |
| 281–287 | Flagship: production — 80%+ coverage with Testcontainers, Docker, CI, deployed, monitored |
| 288–297 | NexOps rebuild by hand — re-derive the data model, rebuild the risk engine and RAG pipeline, write the ADR you never wrote |

**🚪 Exit gate** — 45-minute project deep-dive where every architectural decision is attacked

---

## Stage 8 — Architecture & Low-Level Design

<details>
<summary><b>Days 298–321 — expand</b></summary>

**Principles (298–307)** — why architecture exists (coupling, cohesion, change cost) · layered
architecture and how it rots · **clean/hexagonal architecture**, ports & adapters, the dependency
rule · repository pattern done properly · service layer, application vs domain logic · DI by hand vs
Spring's container · DDD I (ubiquitous language, entities, value objects) · DDD II (aggregates,
domain events, bounded contexts) · **modular monolith — the architecture you should default to** ·
refactor your flagship into clean architecture

**Method (308–310)** — **the LLD method**: requirements → actors → entities → relationships →
classes → code · UML class and sequence diagrams · concurrency in LLD

**Problems (311–321)** — Parking Lot · Elevator · LRU Cache · Rate Limiter · Splitwise · BookMyShow
(with concurrency) · Vending Machine & ATM (state pattern) · Logging framework & notification
service · Chess / Snake & Ladder · Food delivery / cab booking · Library management, Amazon-style
orders

</details>

**🚪 Exit gate** — two live 45-minute LLD interviews: requirements to working Java, narrating tradeoffs

---

## Stage 9 — System Design

<details>
<summary><b>Days 322–359 — expand</b></summary>

**Fundamentals (322–343)** — scalability, latency vs throughput, SLA/SLO/SLI ·
**back-of-the-envelope estimation** · vertical vs horizontal scaling · load balancing (L4 vs L7) ·
caching at scale and **consistent hashing** · CDN and object storage · replication · **sharding**
(shard keys, hotspots, resharding pain) · **CAP done correctly** and PACELC · consistency models ·
SQL vs NoSQL framework · message queues and event-driven architecture · Kafka fundamentals ·
delivery semantics · distributed rate limiting and backpressure · search and inverted indexes ·
observability at scale · resilience patterns · microservices vs monolith (Conway's law) · API
gateway, discovery, service mesh · **distributed transactions — 2PC, Saga, outbox** · consensus
(Raft intuition, quorum, split brain)

**344 — the interview framework**: clarify → estimate → API → data model → HLD → deep dive → bottlenecks

**Designs (345–359)** — URL shortener · Pastebin · distributed rate limiter · distributed KV store ·
web crawler · notification system · **news feed** (fan-out on write vs read) · **chat system**
(presence, receipts, WebSockets at scale) · video streaming · file storage & sync · ride hailing
(geospatial indexing) · ticket booking (seat locking, payment) · **payment system** (idempotency,
ledger, reconciliation) · autocomplete · distributed job scheduler + metrics system

</details>

**🚪 Exit gate — ✅ COMPLETE SDE** — three full mock system design interviews, scored. Pass on
consistent senior-level *structure*, not perfect answers.

---

## Stage 10 — DevOps

<details>
<summary><b>Days 360–379 — expand</b></summary>

Why containers · **container internals** (namespaces, cgroups, union filesystems — Docker is not
magic) · images and layers · multi-stage builds, image size, security scanning · volumes, networks,
env injection · Compose · containerizing your Spring Boot + Postgres + Redis stack properly · Nginx
as reverse proxy and TLS termination · CI/CD concepts · GitHub Actions (matrix builds, caching,
secrets) · a real pipeline: lint → test → build → scan → deploy · deployment strategies (rolling,
blue-green, canary, feature flags) · config and secrets, 12-factor · Prometheus and PromQL ·
Grafana, SLO-based alerting, alert fatigue · centralized logging · distributed tracing with
OpenTelemetry · Kubernetes I and II (and when K8s is overkill) · Terraform basics

</details>

## Stage 11 — Cloud (AWS)

<details>
<summary><b>Days 380–397 — expand</b></summary>

Service models, regions, AZs, shared responsibility · **IAM** (roles, policies, least privilege,
assume-role) · EC2 · **VPC** (subnets, route tables, NAT, NACLs) · S3 (storage classes, lifecycle,
presigned URLs) · RDS · ElastiCache · Lambda and cold starts · API Gateway · SQS & SNS · ECS &
Fargate · ELB and auto scaling · Route 53 & CloudFront · CloudWatch · Secrets Manager, KMS · **cost
management as an engineering constraint** · Well-Architected Framework · **deploy your flagship to
AWS end to end with IaC**

</details>

## Stage 12 — Distributed Systems

<details>
<summary><b>Days 398–413 — expand</b></summary>

The eight fallacies · microservice decomposition by domain not layer · sync vs async communication ·
gRPC and protobuf · **Kafka deep** (partitions, ISR, retention, compaction, exactly-once) ·
RabbitMQ and when it beats Kafka · event sourcing & CQRS — power and cost · **outbox pattern**, CDC,
Debezium · Saga orchestration vs choreography · time & ordering (Lamport, vector clocks) ·
**consensus — Raft in detail** · distributed locking (ZooKeeper/etcd) · replication and partitioning
at production depth · failure detection, gossip, split brain · chaos testing, load shedding ·
interview drill

</details>

---

## Stage 15 — Interview Conversion

| Days | Focus |
|---|---|
| 462–463 | Resume — claim only what you can defend line by line |
| 464–466 | Behavioural — STAR per project, a real failure story, a conflict story |
| 467–471 | Project defence drills — flagship and NexOps, until nothing is unanswerable |
| 472–476 | Five timed 45-min DSA mocks, narrated aloud, scored |
| 477–480 | Four full LLD rounds in Java |
| 481–485 | Five full system design rounds, scored on structure |
| 486–488 | Company-specific prep, levelling, salary negotiation |
