# Written Lessons

One file per day. Full lesson format: what it is → why it exists → how it works inside → diagrams →
code you type yourself → common mistakes → interview questions → mini task → exit questions →
articulation drill.

**Day index and the full path:** [../Roadmap/](../Roadmap/README.md)

---

## Written so far

| Day | Topic | Stage |
|---|---|---|
| [001](Day-001.md) | What a computer is · von Neumann · the latency ladder · C-01 layering & encapsulation | 0 |
| [002](Day-002.md) | Source → bytecode → JIT → machine code · C-02 MAC, switches, ARP | 0 |
| [003](Day-003.md) | Processes, threads, ports · the TCP 4-tuple | 0 |
| [004](Day-004.md) | ⭐ **What a server actually is** — write one by hand · C-03 IP, CIDR, routing, NAT | 0 |
| [005](Day-005.md) | The client side · request/response as a model · C-04 TCP handshake, seq/ack | 0 |
| [006](Day-006.md) | The Internet — packets, routers, BGP, submarine cables · the speed-of-light budget | 0 |
| [007](Day-007.md) | DNS resolution end to end · C-05 retransmission, sliding window, flow control | 0 |
| [008](Day-008.md) | ⭐ **What a TCP connection actually is** · TIME_WAIT, CLOSE_WAIT · C-06 congestion control | 0 |
| [009](Day-009.md) | TLS/HTTPS — what it protects and what it doesn't · certificates · forward secrecy | 0 |
| [010](Day-010.md) | HTTP anatomy — methods, idempotency, status families, headers · C-07 UDP | 0 |
| [011](Day-011.md) | HTTP 1.0 → 1.1 → 2 → 3 · head-of-line blocking · QUIC | 0 |
| [012](Day-012.md) | ⭐ **What HTML actually is** · HTML vs the DOM · C-08 DNS internals | 0 |
| [013](Day-013.md) | CSS — box model, cascade, specificity, layout · C-09 HTTP on the wire | 0 |
| [014](Day-014.md) | JavaScript — engine, call stack, event loop, micro vs macrotasks | 0 |
| [015](Day-015.md) | Rendering pipeline · layout vs paint vs composite · C-10 crypto primitives | 0 |
| [016](Day-016.md) | REST — the six constraints, Richardson levels · vs RPC/GraphQL · C-11 sockets | 0 |
| [017](Day-017.md) | ⭐ **WebSockets** — write one by hand *(exit-gate item)* | 0 |
| [018](Day-018.md) | WebSocket vs SSE vs polling — the decision table · C-12 proxies, LB, CDN | 0 |
| [019](Day-019.md) | Where state lives — cookies, sessions, JWTs · C-13 network debugging | 0 |
| [020](Day-020.md) | Same-origin policy and CORS — the security boundary, not the annoyance | 0 |
| [021](Day-021.md) | Deployment — build artifacts, content hashing, rollout strategies · C-14 networks drill (40 Qs) | 0 |
| [022](Day-022.md) | 🚪 **Capstone** — the full 12-phase request trace · Stage 0 exit gate | 0 |
| [023](Day-023.md) | JVM architecture — class loader, data areas, JIT · B-01 kernel mode, syscalls | 1 |
| [024](Day-024.md) | Reading bytecode with `javap` — the stack machine, five `invoke*`, what lambdas compile to | 1 |
| [025](Day-025.md) | Stack vs heap · where statics and the string pool live · why locals are thread-safe | 1 |
| [026](Day-026.md) | Garbage collection — reachability, generational hypothesis, G1 vs ZGC · B-02 processes | 1 |
| [027](Day-027.md) | GC tuning, the 7 leak patterns, diagnosing `OutOfMemoryError` | 1 |
| [028](Day-028.md) | Primitives vs wrappers, autoboxing, **the `Integer` cache trap** | 1 |
| [029](Day-029.md) | Strings — 5 reasons for immutability, why `+=` in a loop is O(n²) · B-03 threads vs processes | 1 |
| [030](Day-030.md) | Operators, `switch` expressions and exhaustiveness, `var`, text blocks · **RECALL candidate** | 1 |
| [031](Day-031.md) | Arrays — O(1) indexing, 2D layout, `System.arraycopy`, covariance | 1 |
| [032](Day-032.md) | ⭐ **Java is always pass-by-value** — proved · overload resolution · varargs | 1 |
| [033](Day-033.md) | `static`, the 6-step initialisation order, hiding vs overriding · B-04 context switching | 1 |
| [034](Day-034.md) | `final` — and why it is **not** immutability | 1 |
| [034A](Day-034A.md) | ➕ **Immutability & defensive copying** — the two attacks on a naive class | 1 |
| [035](Day-035.md) | Classes, constructors, chaining, the `this` escape, static factories | 1 |
| [036](Day-036.md) | Encapsulation (properly), access modifiers, package layout · B-05 CPU scheduling | 1 |
| [037](Day-037.md) | Inheritance, overriding vs hiding, **the fragile base class problem** | 1 |
| [038](Day-038.md) | Polymorphism — vtables, and how the JIT deletes the virtual call | 1 |
| [039](Day-039.md) | Abstract vs interface, default methods, the diamond | 1 |
| [040](Day-040.md) | ⭐ **`equals`/`hashCode` contract** — and mechanically what breaks · B-06 races | 1 |
| [041](Day-041.md) | `toString`, `Comparable` vs `Comparator`, why `Cloneable` is broken | 1 |
| [042](Day-042.md) | Records, sealed classes, pattern matching — algebraic data types in Java | 1 |
| [042A](Day-042A.md) | ➕ **`java.time`** — the API the roadmap was missing entirely | 1 |
| [042B](Day-042B.md) | ➕ **`BigDecimal`** — why `double` is fatal for money | 1 |
| [043](Day-043.md) | Enums with state, as state machines, the correct singleton · B-07 mutex/semaphore/monitor | 1 |
| [044](Day-044.md) | Inner classes — `this$0` and the leak it causes | 1 |
| [045](Day-045.md) | The collections hierarchy · the decision table · complexity | 1 |
| [046](Day-046.md) | `ArrayList` internals — 1.5× growth, amortised O(1) | 1 |
| [047](Day-047.md) | `LinkedList` — and why it almost never wins · B-08 deadlock | 1 |
| [048](Day-048.md) | ⭐⭐ **`HashMap` internals** — spread, masking, treeify at 8, resize bit-split | 1 |
| [049](Day-049.md) | `LinkedHashMap` + **LRU cache** · `TreeMap` and red-black trees | 1 |
| [050](Day-050.md) | The `Set` family — every set is a map · `EnumSet` as a bit vector · B-09 fragmentation | 1 |
| [051](Day-051.md) | `ArrayDeque`, `PriorityQueue` (binary heap), why never `Stack` | 1 |
| [052](Day-052.md) | Iterators, `modCount`, why fail-fast is only best-effort | 1 |
| [053](Day-053.md) | `ConcurrentHashMap` internals, `CopyOnWriteArrayList` · B-10 paging and the TLB | 1 |
| [054](Day-054.md) | Generics — bounded types, recursive bounds, why they're invariant | 1 |
| [055](Day-055.md) | Type erasure — bridge methods, heap pollution, what survives | 1 |
| [056](Day-056.md) | Wildcards and **PECS**, derived rather than recited | 1 |
| [056A](Day-056A.md) | ➕ **Reflection & custom annotations** — why `@Transactional` fails on self-invocation | 1 |
| [057](Day-057.md) | Lambdas, the four functional shapes, composition · B-11 page replacement | 1 |
| [058](Day-058.md) | Method references · **where `Optional` does not belong** | 1 |
| [059](Day-059.md) | Streams I — laziness, short-circuiting, infinite sources | 1 |
| [060](Day-060.md) | Streams II — collectors, grouping, the real cost of parallel · B-12 inodes | 1 |
| [061](Day-061.md) | Exceptions — the hierarchy, checked vs unchecked, try-with-resources | 1 |
| [062](Day-062.md) | Exception **design** — where the boundary goes, never swallow | 1 |
| [063](Day-063.md) | I/O and NIO — decorators, charsets, buffering, `Path`, `ByteBuffer` | 1 |
| [063A](Day-063A.md) | ➕ **Java serialization** — and why it is an RCE hazard | 1 |
| [063B](Day-063B.md) | ➕ **Regex** — greedy/lazy/possessive · **catastrophic backtracking (ReDoS)** | 1 |
| [064](Day-064.md) | Threads — lifecycle, interruption, daemons · B-13 I/O models, `epoll` | 1 |
| [065](Day-065.md) | ⭐ **The Java Memory Model** — happens-before *(exit-gate item)* | 1 |
| [066](Day-066.md) | `synchronized`, intrinsic locks, lock scope · what `volatile` does **not** do | 1 |
| [066A](Day-066A.md) | ➕ **`ThreadLocal`** — how Spring knows the current user · the pool leak | 1 |
| [067](Day-067.md) | `ReentrantLock`, `ReadWriteLock`, `Condition`, `wait`/`notify` · B-14 `epoll` | 1 |
| [068](Day-068.md) | Atomics, **CAS**, `LongAdder`, the **ABA** problem | 1 |
| [069](Day-069.md) | Executors — the submission algorithm, pool sizing, `CompletableFuture` | 1 |
| [070](Day-070.md) | Latch, semaphore, barrier · **producer-consumer and backpressure** | 1 |
| [071](Day-071.md) | Deadlock, livelock, starvation — reproduce each · B-15 Linux in practice | 1 |
| [072](Day-072.md) | Virtual threads — what changes and **what doesn't** | 1 |
| [073](Day-073.md) | Maven & Gradle — scopes, transitive resolution, BOMs, multi-module | 1 |
| [073A](Day-073A.md) | ➕ **Logging** — SLF4J facade, MDC, and what never to log | 1 |
| [074](Day-074.md) | JUnit 5 — lifecycle, `assertAll`, parameterized · B-16 OS drill (40 Qs) | 1 |
| [075](Day-075.md) | Mockito — the five doubles, captors, **when mocking is a smell** | 1 |
| [076](Day-076.md) | Debugging — thread dumps, heap dumps, **JFR**, the triage order | 1 |
| [076A](Day-076A.md) | ➕ **Classpath & class loading** — the five errors, jar hell | 1 |
| [077](Day-077.md) | 🚪 **Java traps drill** (40 Qs) · Stage 1 exit gate | 1 |
| [078](Day-078.md) | Filesystem, permissions, users, links · D-01 why databases exist | 2 |
| [079](Day-079.md) | Shell — pipes, redirection, exit codes, expansion, quoting | 2 |
| [080](Day-080.md) | `grep` `sed` `awk` `find` `xargs` — a log file into an answer | 2 |
| [081](Day-081.md) | Processes, **signals**, graceful shutdown, systemd · D-02 relational model | 2 |
| [082](Day-082.md) | `ssh`, tunnels, `curl` timing, `journalctl` | 2 |
| [083](Day-083.md) | Bash — `set -euo pipefail`, `trap`, shellcheck | 2 |
| [084](Day-084.md) | ⭐ **The Git object model** — build a commit by hand · D-03 ER modelling | 2 |
| [085](Day-085.md) | Staging, `add -p`, `.gitignore`, commit messages, leaked secrets | 2 |
| [086](Day-086.md) | Branching, fast-forward, three-way merge, **merge vs rebase** | 2 |
| [087](Day-087.md) | Conflicts, **reflog**, reset/revert/restore, **bisect** · D-04 FDs & closure | 2 |
| [088](Day-088.md) | Remotes, trunk-based vs git-flow, **PR discipline** | 2 |
| [089](Day-089.md) | Clean code — naming, function size, side effects, comments | 2 |
| [090](Day-090.md) | SOLID I — SRP by **actor**, Open/Closed | 2 |
| [091](Day-091.md) | SOLID II — **Liskov**, ISP, DIP · D-05 normalization to BCNF | 2 |
| [092](Day-092.md) | DRY, YAGNI, KISS, **composition over inheritance**, Demeter | 2 |
| [093](Day-093.md) | Creational — factory, builder, **why Singleton is usually wrong** | 2 |
| [094](Day-094.md) | Structural — the four wrappers distinguished · D-06 relational algebra | 2 |
| [095](Day-095.md) | Behavioural — strategy, observer, command, template, state | 2 |
| [096](Day-096.md) | **Refactoring** — smells catalogue, safe steps, the strangler | 2 |
| [097](Day-097.md) | Testing philosophy — the pyramid, FIRST · D-07 SQL I, NULLs | 2 |
| [098](Day-098.md) | **TDD** — red/green/refactor on a real rate limiter | 2 |
| [099](Day-099.md) | Debugging as a **methodology** — hypotheses, bisection, traces | 2 |
| [100](Day-100.md) | Code review, both sides · D-08 every join type, join order | 2 |
| [100A](Day-100A.md) | ➕ **Supply chain** — CVEs, SBOMs, licences, **slopsquatting** | 2 |
| [101](Day-101.md) | 🚪 Documentation & **ADRs** · Stage 2 exit gate | 2 |
| [102](Day-102.md) | HTTP for backends — idempotency, caching, `ETag`/`If-Match` | 3 |
| [103](Day-103.md) | REST design — resources, URIs, nesting, HATEOAS reality check | 3 |
| [104](Day-104.md) | Status codes & **RFC 7807** error contracts · D-09 window functions | 3 |
| [105](Day-105.md) | Validation & the **trust boundary** — mass assignment, IDOR | 3 |
| [106](Day-106.md) | Serialization — the four JSON types that corrupt silently | 3 |
| [107](Day-107.md) | **Pagination** — why offset breaks · D-10 CTEs, recursive CTEs | 3 |
| [108](Day-108.md) | Filtering, sorting, search — allow-lists all the way down | 3 |
| [109](Day-109.md) | **Versioning** — what breaks, and how to deprecate | 3 |
| [110](Day-110.md) | OpenAPI, contract-first · ⭐ **D-11 indexes** — B+ trees, covering | 3 |
| [111](Day-111.md) | **AuthN vs AuthZ** — the distinction people fail on | 3 |
| [112](Day-112.md) | ⭐ **Password storage** — argon2, salt, pepper, work factors | 3 |
| [113](Day-113.md) | Sessions, cookie flags, fixation · D-12 query processing | 3 |
| [114](Day-114.md) | ⭐ **JWT internals** — `alg:none`, algorithm confusion, revocation | 3 |
| [115](Day-115.md) | Access & refresh tokens — **rotation and reuse detection** | 3 |
| [115b](Day-115b.md) | ➕ **API keys**, HMAC signing, mTLS, workload identity | 3 |
| [116](Day-116.md) | OAuth 2.0 — the grants, **PKCE**, why implicit died | 3 |
| [117](Day-117.md) | OpenID Connect — ID tokens · **D-13 ACID**, precisely | 3 |
| [118](Day-118.md) | RBAC/ABAC/ReBAC · **multi-tenancy and row-level security** | 3 |
| [119](Day-119.md) | **OWASP API Top 10** — IDOR, SSRF, CSRF, the checklist | 3 |
| [119b](Day-119b.md) | ➕ **SQL injection in depth** — and the general principle | 3 |
| [120](Day-120.md) | Caching patterns · **D-14 isolation levels**, write skew | 3 |
| [121](Day-121.md) | Invalidation, **stampede**, stale-while-revalidate | 3 |
| [122](Day-122.md) | ⭐ **Rate limiting** — four algorithms, distributed | 3 |
| [123](Day-123.md) | **Idempotency & retries** · D-15 MVCC, 2PL, optimistic | 3 |
| [124](Day-124.md) | Background jobs · **the dual-write problem and the outbox** | 3 |
| [125](Day-125.md) | Queues & brokers — ack, DLQ, visibility timeout, Kafka | 3 |
| [126](Day-126.md) | File uploads — **presigned URLs** · D-16 NoSQL, **CAP** | 3 |
| [127](Day-127.md) | Structured logging & **correlation IDs** | 3 |
| [128](Day-128.md) | Metrics, RED/USE · ⭐ **liveness vs readiness** | 3 |
| [129](Day-129.md) | Webhooks — signatures, replay, SSRF when sending | 3 |
| [129A](Day-129A.md) | 🐳 Docker I — containers, images, the commands | 3 |
| [129B](Day-129B.md) | 🐳 Docker II — layers, cache, **the JVM in a container** | 3 |
| [129C](Day-129C.md) | 🐳 Docker III — compose stack · 🚪 **Stage 3 exit gate** | 3 |
| [130](Day-130.md) | **Why Spring exists** — build the problem first | 4 |
| [131](Day-131.md) | DI — constructor vs setter vs **field (never)** | 4 |
| [132](Day-132.md) | The IoC container — bean definitions, **the two-phase startup** | 4 |
| [133](Day-133.md) | The bean lifecycle · **graceful shutdown** | 4 |
| [134](Day-134.md) | Scopes · ⭐ **the singleton-with-state bug** | 4 |
| [135](Day-135.md) | Component scanning · **package-by-feature** | 4 |
| [136](Day-136.md) | `@Qualifier`, `@Primary`, `@Profile` | 4 |
| [137](Day-137.md) | ⭐ **AOP & proxies** — why `@Transactional` fails on self-invocation | 4 |
| [138](Day-138.md) | SpEL & property resolution · **SpEL as an RCE surface** | 4 |
| [139](Day-139.md) | Boot starters · **the defaults you must change** | 4 |
| [140](Day-140.md) | **Auto-configuration internals** — conditions, ordering, debugging | 4 |
| [141](Day-141.md) | Boot project structure — **package by feature**, the main-class rule | 4 |
| [142](Day-142.md) | Configuration & profiles — `@ConfigurationProperties`, secrets | 4 |
| [143](Day-143.md) | **Actuator** — probes, metrics, and the endpoints that leak | 4 |
| [144](Day-144.md) | DevTools, **fat jars**, layered images, container JVM flags | 4 |
| [145](Day-145.md) | ⭐ **Spring MVC architecture** — the DispatcherServlet lifecycle | 4 |
| [146](Day-146.md) | `@RestController` & mappings — Day 103's design, wired | 4 |
| [147](Day-147.md) | ⭐ **DTOs vs entities** — four reasons, each fatal alone | 4 |
| [148](Day-148.md) | **Bean Validation** — and where validation stops, business rules start | 4 |
| [149](Day-149.md) | **Global exception handling** — RFC 7807 and the three gaps | 4 |
| [150](Day-150.md) | `ResponseEntity` & **Jackson** — closing Day 106's four hazards | 4 |
| [151](Day-151.md) | **Filters vs interceptors vs AOP** — choosing the right hook | 4 |
| [152](Day-152.md) | **Pagination** with `Pageable` — and where Spring's default is wrong | 4 |
| [153](Day-153.md) | Versioning & **springdoc** — the code-first tension | 4 |
| [154](Day-154.md) | ⭐ **HTTP clients** — the timeout that isn't set, retries, breakers | 4 |
| [155](Day-155.md) | 🚪 **CORS**, reverse proxies · **Web block exit gate** | 4 |
| [156](Day-156.md) | JDBC & **HikariCP** — the pool is the bottleneck | 4 |
| [157](Day-157.md) | JPA vs Hibernate vs Spring Data — three layers | 4 |
| [158](Day-158.md) | Entity mapping — ids, enums, **`equals` for entities** | 4 |
| [159](Day-159.md) | Relationships & **the owning side** | 4 |
| [160](Day-160.md) | ⭐ **Lazy vs eager, N+1, `LazyInitializationException`** | 4 |
| [161](Day-161.md) | Fetch strategies — join fetch, graphs, **projections** | 4 |
| [162](Day-162.md) | **The persistence context** — dirty checking, flush order | 4 |
| [163](Day-163.md) | Spring Data repositories — and where they stop helping | 4 |
| [163b](Day-163b.md) | ➕ **Injection safety in JPA** — where the ORM does not protect you | 4 |
| [164](Day-164.md) | ⭐ **`@Transactional`** — propagation, rollback, the bugs | 4 |
| [165](Day-165.md) | **Auditing & locking** — optimistic, pessimistic, Envers | 4 |
| [166](Day-166.md) | **Flyway** — and ⭐ **expand/contract** zero-downtime migration | 4 |
| [167](Day-167.md) | The second-level cache — and why you probably shouldn't | 4 |
| [168](Day-168.md) | 🚪 Redis & `@Cacheable` · **Data block exit gate** | 4 |
| [169](Day-169.md) | ⭐ **The Spring Security filter chain** | 4 |
| [170](Day-170.md) | `AuthenticationManager`, `UserDetailsService`, authorities | 4 |
| [171](Day-171.md) | **Password encoding** — and migrating 200k users | 4 |
| [172](Day-172.md) | Stateless APIs, sessions, **CSRF** — when disabling is right | 4 |
| [173](Day-173.md) | ⭐ **JWT authentication** — the filter, rotation, revocation | 4 |
| [174](Day-174.md) | `@PreAuthorize` & method security — **closing IDOR** | 4 |
| [175](Day-175.md) | **OAuth2** — resource server, client, and what not to build | 4 |
| [176](Day-176.md) | 🚪 **Multi-tenancy & RLS** · **Security block exit gate** | 4 |
| [177](Day-177.md) | `@Async` — executors, context propagation, lost exceptions | 4 |
| [178](Day-178.md) | `@Scheduled` & **the distributed scheduling problem** | 4 |
| [179](Day-179.md) | **Application events** — and where they stop being right | 4 |
| [180](Day-180.md) | WebSockets & STOMP — **the three things that break at scale** | 4 |
| [181](Day-181.md) | **Resilience4j** — and ⭐ **the composition order** | 4 |
| [182](Day-182.md) | Structured logging, **MDC and tracing** | 4 |
| [183](Day-183.md) | Testing — slices, MockMvc, **the context cache** | 4 |
| [184](Day-184.md) | ⭐ **Testcontainers** — testing against the real thing | 4 |
| [185](Day-185.md) | **Production readiness** — the 50-item checklist | 4 |
| [185A](Day-185A.md) | Spring **Kafka & RabbitMQ** — losing nothing, duplicating safely | 4 |
| [185B](Day-185B.md) | ➕🚪 **Boot 3 specifics** · **STAGE 4 EXIT GATE** | 4 |
| [186](Day-186.md) | **PostgreSQL architecture** — processes, buffers, **WAL**, checkpoints | 5 |
| [187](Day-187.md) | Data types & **constraints** — the last line of defence | 5 |
| [188](Day-188.md) | Schema design in practice — where normalization stops | 5 |
| [189](Day-189.md) | ⭐ **Indexes I** — the B-tree, and **8 reasons yours is ignored** | 5 |
| [190](Day-190.md) | **Indexes II** — composite order, partial, GIN/GiST/BRIN/trigram | 5 |
| [191](Day-191.md) | ⭐ **`EXPLAIN ANALYZE`** — reading a plan like a diagnosis | 5 |
| [192](Day-192.md) | **Join algorithms** — and why the planner picks wrong | 5 |
| [193](Day-193.md) | 🔧 **Query optimization workshop** — ten queries, against the clock | 5 |
| [194](Day-194.md) | **MVCC, vacuum, bloat** — and ⭐ **transaction ID wraparound** | 5 |
| [195](Day-195.md) | 🔬 **Isolation levels** — reproduce every anomaly, incl. **write skew** | 5 |
| [196](Day-196.md) | **Locks & deadlocks** — ⭐ the FIFO queue outage | 5 |
| [197](Day-197.md) | Connection pooling & **PgBouncer** — what transaction mode breaks | 5 |
| [198](Day-198.md) | **JSONB** — and the discipline that stops it becoming EAV | 5 |
| [199](Day-199.md) | **Full-text search** — and when PostgreSQL is not enough | 5 |
| [200](Day-200.md) | **Partitioning** — and what it is not | 5 |
| [201](Day-201.md) | **Replication, failover, PITR** — ⭐ RPO and RTO | 5 |
| [202](Day-202.md) | 🚪 **`pg_stat_statements`** · **Postgres block gate** | 5 |
| [203](Day-203.md) | **Redis architecture** — the single-threaded event loop | 5 |
| [204](Day-204.md) | **Data structures** — sorted sets, bitmaps, **HyperLogLog** | 5 |
| [205](Day-205.md) | Persistence — **RDB vs AOF**, and what "durable" means here | 5 |
| [206](Day-206.md) | Caching patterns, **key design**, eviction | 5 |
| [207](Day-207.md) | ⭐ **Distributed locks** — why `SETNX` is wrong · **Redlock** | 5 |
| [208](Day-208.md) | **Rate limiting** — the four algorithms in Lua | 5 |
| [209](Day-209.md) | 🚪 **Pub/Sub & Streams** · **Redis block gate** | 5 |
| [210](Day-210.md) | **MongoDB** — the document model, and when it's a mistake | 5 |
| [211](Day-211.md) | ⭐ **Embedding vs referencing** — the only decision that matters | 5 |
| [212](Day-212.md) | Indexes (**ESR**) & the **aggregation pipeline** | 5 |
| [213](Day-213.md) | 🚪🚪 Transactions, sharding · **STAGE 5 EXIT GATE** | 5 |

> **Days 214–247 are Stage 6 — Frontend**, which lives in
> [`03-Web-Developer`](../../03-Web-Developer/). The Java track resumes at Day 248.

| Day | Focus | Stage |
|---|---|---|
| [248](Day-248.md) | 🏗️ **Choosing the flagship** — what makes a project defensible | 7 |
| [249](Day-249.md) | 🏗️ Requirements, the state machine, ⭐ **the hard problem** | 7 |
| [250](Day-250.md) | 🏗️ Domain modelling, the ER diagram, the first migration | 7 |
| [251](Day-251.md) | 🏗️ **The API contract** — OpenAPI before any controller | 7 |
| [252](Day-252.md) | 🚪🏗️ **The design review gate** — five ADRs, defence rehearsed | 7 |
| [253](Day-253.md) | 🏗️ The skeleton — structure, Flyway, CI, ⭐ **deployed** | 7 |
| [254](Day-254.md) | 🏗️ **The domain layer** — invariants, no framework | 7 |
| [255](Day-255.md) | 🏗️ Persistence — projections, ⭐ **query counts asserted** | 7 |
| [256](Day-256.md) | 🏗️ The service layer — transaction boundaries, side effects | 7 |
| [257](Day-257.md) | 🏗️ The web layer — DTOs, validation, the error contract | 7 |
| [258](Day-258.md) | ⭐🏗️ **Authentication** — JWT, rotation, **reuse detection** | 7 |
| [259](Day-259.md) | 🏗️ **Authorization** — ownership, RBAC, **tenant isolation** | 7 |
| [260](Day-260.md) | 🏗️ Pagination, filtering, caching, rate limiting, metrics | 7 |
| [261](Day-261.md) | 🏗️ **Testing** — the eight categories, and a **load test** | 7 |
| [262](Day-262.md) | 🚪🏗️ **The backend review gate** — a 42-row audit | 7 |
| [263](Day-263.md) | 🏗️ **The outbox in anger** — the dual write, actually solved | 7 |
| [264](Day-264.md) | 🏗️ **Background jobs** — idempotent claim beats locking | 7 |
| [265](Day-265.md) | 🏗️ `@Async` — bounded queues, context, ⭐ **what may be lost** | 7 |
| [266](Day-266.md) | 🏗️ **Caching — measured**, and at least one deleted | 7 |
| [267](Day-267.md) | 🏗️ **File uploads** — presigned URLs, ⭐ eight attacks | 7 |
| [268](Day-268.md) | 🏗️ **Real-time** — SSE vs WebSockets, honestly | 7 |
| [269](Day-269.md) | 🏗️ Search & reporting — the read path with different rules | 7 |
| [270](Day-270.md) | 🚪🏗️ **Data & async gate** — ⭐ the failure drill | 7 |

> **Days 271–280 are the flagship frontend**, in
> [`03-Web-Developer`](../../03-Web-Developer/). The Java track resumes at Day 281.

| Day | Focus | Stage |
|---|---|---|
| [281](Day-281.md) | 🏗️ **Coverage & quality gates** — ⭐ mutation testing | 7 |
| [282](Day-282.md) | 🏗️ **The container image** — layered, non-root, sized | 7 |
| [283](Day-283.md) | 🏗️ **The pipeline** — migrations, deploy, ⭐ **tested rollback** | 7 |
| [284](Day-284.md) | 🏗️ **Infrastructure** — ⭐ the failure matrix, a timed restore | 7 |
| [285](Day-285.md) | 🏗️ **Observability** — ⭐ every alert fired, ⭐ a postmortem | 7 |
| [286](Day-286.md) | 🏗️ **Load testing** & ⭐ **breaking it on purpose** | 7 |
| [287](Day-287.md) | 🚪🏗️ **Production readiness gate** — ⭐ **the flagship is complete** | 7 |
| [288](Day-288.md) | 🏗️ **The rebuild** — archaeology, the audit, characterisation tests | 7 |
| [289](Day-289.md) | 🏗️ **Re-deriving the data model** — derive blind, then diff | 7 |
| [290](Day-290.md) | ⭐🏗️ **The risk engine I** — determinism, rules as data | 7 |
| [291](Day-291.md) | 🏗️ **The risk engine II** — backtesting, shadow mode, explainability | 7 |
| [292](Day-292.md) | ⭐🏗️ **RAG I** — chunking, embeddings, ingestion | 7 |
| [293](Day-293.md) | 🏗️ **RAG II** — ⭐ **hybrid search**, reranking, evaluation | 7 |
| [294](Day-294.md) | ⭐🏗️ **RAG III** — ⭐ **prompt injection**, hallucination, cost | 7 |
| [295](Day-295.md) | 🏗️ **The ADR you never wrote** — and the honest critique | 7 |
| [296](Day-296.md) | 🏗️ Finishing — audit fixes, ⭐ **the injection test** | 7 |
| [297](Day-297.md) | 🚪🚪🏗️ **STAGE 7 EXIT GATE** — two defensible projects | 7 |
| [298](Day-298.md) | **Why architecture exists** — coupling, cohesion, **change cost** | 8 |
| [299](Day-299.md) | **Layered architecture** — and ⭐ **the four ways it rots** | 8 |
| [300](Day-300.md) | ⭐ **Clean & hexagonal** — ports, adapters, the dependency rule | 8 |
| [301](Day-301.md) | **The repository pattern** — where `JpaRepository` isn't one | 8 |
| [302](Day-302.md) | **The service layer** — application vs domain logic | 8 |
| [303](Day-303.md) | **DI by hand** — then ⭐ **build the container** | 8 |
| [304](Day-304.md) | **DDD I** — value objects, ⭐ **illegal states unrepresentable** | 8 |
| [305](Day-305.md) | ⭐ **DDD II** — ⭐ **aggregates as the contention boundary**, events | 8 |
| [306](Day-306.md) | ⭐ **The modular monolith** — the default, and when to extract | 8 |
| [307](Day-307.md) | **Refactoring the flagship** — seven steps, measured | 8 |
| [308](Day-308.md) | ⭐ **The LLD method** — 45 minutes, six steps, ⭐ **the clock** | 8 |
| [309](Day-309.md) | **UML that earns its keep** — and ⭐ **the missing arrow** | 8 |
| [310](Day-310.md) | ⭐ **Concurrency in LLD** — the question that ends most rounds | 8 |
| [311](Day-311.md) | ⭐ **Parking Lot** — the method applied end to end | 8 |
| [312](Day-312.md) | **Elevator** — state machine, LOOK, ⭐ **the cost function** | 8 |
| [313](Day-313.md) | **LRU cache** — and ⭐ **why real caches aren't exact** | 8 |
| [314](Day-314.md) | **Rate limiter** — ⭐ **the boundary burst**, token bucket | 8 |
| [315](Day-315.md) | **Splitwise** — ⭐ **the penny problem**, settlement | 8 |
| [316](Day-316.md) | ⭐ **BookMyShow** — ⭐ **holding a seat across a payment** | 8 |
| [317](Day-317.md) | **Vending machine & ATM** — state pattern, ⭐ **the journal** | 8 |
| [318](Day-318.md) | **Logging & notifications** — ⭐ **when the queue is full** | 8 |
| [319](Day-319.md) | **Chess & Snake and Ladder** — ⭐ **legality must be simulated** | 8 |
| [320](Day-320.md) | **Food delivery & cabs** — ⭐ **matching is an offer** | 8 |
| [320A](Day-320A.md) | ➕ **Library & Amazon orders** — ⭐ **the two unifying rules** | 8 |
| [321](Day-321.md) | 🚪🚪 **STAGE 8 EXIT GATE** — two live 45-minute LLD rounds | 8 |
| [322](Day-322.md) | **Scalability** — ⭐ **Little's Law**, the utilisation curve, SLO/SLI | 9 |
| [323](Day-323.md) | ⭐ **Back-of-the-envelope estimation** — the numbers that decide | 9 |
| [324](Day-324.md) | **Vertical vs horizontal** — ⭐ **what "stateless" really means** | 9 |
| [325](Day-325.md) | **Load balancing** — L4 vs L7, ⭐ **the death spiral** | 9 |
| [326](Day-326.md) | ⭐ **Caching at scale** — ⭐ **consistent hashing**, four failure modes | 9 |
| [327](Day-327.md) | **CDN & object storage** — ⭐ **presigned URLs** | 9 |
| [328](Day-328.md) | **Replication** — three topologies, ⭐ **three lag anomalies** | 9 |
| [329](Day-329.md) | ⭐ **Sharding** — the key, hotspots, ⭐ **pre-splitting** | 9 |
| [330](Day-330.md) | ⭐ **CAP done correctly** — and ⭐ **PACELC**, which matters more | 9 |
| [331](Day-331.md) | **Consistency models** — ⭐ **the four session guarantees** | 9 |
| [332](Day-332.md) | **SQL vs NoSQL** — ⭐ **when you must commit to your queries** | 9 |
| [333](Day-333.md) | **Queues & event-driven** — ⭐ **choreography vs orchestration** | 9 |
| [334](Day-334.md) | **Kafka** — the log, partitions, ⭐ **where ordering lives** | 9 |
| [335](Day-335.md) | **Delivery semantics** — ⭐ **why exactly-once is a lie** | 9 |
| [336](Day-336.md) | **Distributed limits & ⭐ backpressure** — ⭐ **deadline propagation** | 9 |
| [337](Day-337.md) | **Search & inverted indexes** — ⭐ **keeping a derived index honest** | 9 |
| [338](Day-338.md) | **Observability at scale** — ⭐ **cardinality, burn rate** | 9 |
| [339](Day-339.md) | **Resilience** — ⭐ **the anatomy of a cascade**, static stability | 9 |
| [340](Day-340.md) | **Microservices vs monolith** — ⭐ **Conway's law**, the distributed monolith | 9 |
| [341](Day-341.md) | **Gateway, discovery, service mesh** — and when you need none | 9 |
| [342](Day-342.md) | ⭐ **Distributed transactions** — 2PC, ⭐ **sagas & compensations** | 9 |
| [343](Day-343.md) | ⭐ **Consensus** — Raft, quorums, ⭐ **why not to build one** | 9 |
| [344](Day-344.md) | ⭐⭐ **The system design interview framework** — the 45-min clock | 9 |
| [345](Day-345.md) | 🏗️ **Design 1 — URL shortener** · ⭐ key generation, 301 vs 302 | 9 |
| [346](Day-346.md) | 🏗️ **Design 2 — Pastebin** · ⭐ where large content lives | 9 |
| [347](Day-347.md) | 🏗️ **Design 3 — rate limiter** · ⭐ local decisions, global truth | 9 |
| [348](Day-348.md) | 🏗️ **Design 4 — distributed KV store** · ⭐ vector clocks, ⭐ **LSM trees** | 9 |
| [349](Day-349.md) | 🏗️ **Design 5 — web crawler** · ⭐ **the frontier**, politeness by structure | 9 |
| [350](Day-350.md) | 🏗️ **Design 6 — notifications** · ⭐ **the broadcast**, provider limits | 9 |
| [351](Day-351.md) | ⭐🏗️ **Design 7 — news feed** · ⭐⭐ **fan-out on write vs read** | 9 |
| [352](Day-352.md) | ⭐🏗️ **Design 8 — chat** · ⭐⭐ **routing to a connection**, ordering | 9 |
| [353](Day-353.md) | 🏗️ **Design 9 — video streaming** · ⭐ **adaptive bitrate**, egress | 9 |
| [354](Day-354.md) | 🏗️ **Design 10 — file sync** · ⭐ **chunking**, conflicts | 9 |
| [355](Day-355.md) | 🏗️ **Design 11 — ride hailing** · ⭐ **location at 250k/s** | 9 |
| [356](Day-356.md) | 🏗️ **Design 12 — ticket booking** · ⭐⭐ **the waiting room** | 9 |
| [357](Day-357.md) | ⭐🏗️ **Design 13 — payments** · ⭐⭐ **the double-entry ledger** | 9 |
| [358](Day-358.md) | 🏗️ **Design 14 — autocomplete** · ⭐ **precompute, then look up** | 9 |
| [358A](Day-358A.md) | ➕🏗️ **Design 15 — job scheduler & metrics** · ⭐ claims, compression | 9 |
| [359](Day-359.md) | 🚪🚪 **STAGE 9 EXIT GATE** — ✅✅ **COMPLETE SDE** | 9 |

**✅ Stage 0 (22/22).** **✅ Stage 1 (64/64).** **✅ Stage 2 (25/25).** **✅ Stage 3 (33/33).**
**✅ Stage 4 (57/57).** **✅ Stage 5 (28/28).** **✅ Stage 7 (40/40 Java-side)** — ✅ **both projects
complete**. The 10 frontend days (271–280) live in
[`03-Web-Developer`](../../03-Web-Developer/).
**✅ Stage 8 (25/25)** — principles, method, **eleven problems** and the gate.
**✅ Stage 9 (39/39)** — 22 fundamentals, the framework, **fifteen designs** and the gate.
**✅✅ COMPLETE SDE — Stages 0–9 finished.** **349 days written**, including all **fifteen added gap
days**. Parallel tracks: **C-01–C-14** ✅, **B-01–B-16** ✅, **D-01–D-16** ✅ — all finished.
**Next batch:** Days 360–379 — **Stage 10, DevOps**: Linux and shell at depth, Docker, CI/CD
pipelines, Kubernetes, infrastructure as code, secrets, and production operations.

---

## 🚪 Stage 1 exit gate

Full checklist, including the on-a-real-JVM items, is on **[Day 077](Day-077.md)**.

- [ ] In-memory key-value store with TTL, concurrent and thread-safe → [053](Day-053.md)
- [ ] Custom `ArrayList` written from scratch → [046](Day-046.md)
- [ ] Custom `HashMap` written from scratch → [048](Day-048.md)
- [ ] **Whiteboard `HashMap` internals** — collisions, treeification, resize → [048](Day-048.md)
- [ ] Explain the JMM and why `volatile` doesn't make `i++` safe → ⭐ [065](Day-065.md), [066](Day-066.md)
- [ ] Live-code a thread-safe bounded blocking queue → [067](Day-067.md), [070](Day-070.md)
- [ ] Diagnose a hung JVM and find a leak in a heap dump → [071](Day-071.md), [076](Day-076.md)
- [ ] Score 35+/40 on the traps drill, cold → [077](Day-077.md)

---

## 🚪 Stage 0 exit gate

Full detail on [Day 022](Day-022.md).

- [ ] Raw TCP server speaking HTTP by hand → [Day 004](Day-004.md) (Java) + [Day 022](Day-022.md) (Python)
- [ ] WebSocket chat across two tabs, server hand-written → [Day 017](Day-017.md)
- [ ] 10-min oral: *"what happens when you type google.com and press Enter?"* → [Day 022](Day-022.md)
- [ ] Explain HTML vs a server to a non-technical person → [Day 012](Day-012.md), [Day 004](Day-004.md)

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

- **DSA in Java** — 45–75 min + a pattern journal entry ([track](../Roadmap/DSA-Parallel-Track.md))
- **🎙️ Articulation drill** — 15 min, recorded
- **Spaced repetition** — 10 min on notes from days 1, 3, 7, 14, 30 ago
