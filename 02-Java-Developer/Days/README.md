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

**✅ Stage 0 complete (22/22).** **✅ Stage 1 complete (64/64).** **✅ Stage 2 complete (25/25).**
**Stage 3: 11 of 33 written** — the API-design block is done and the security block has begun.
**135 days written**, including all **eleven added gap days**.
Parallel tracks: **C-01–C-14** (networks) ✅, **B-01–B-16** (operating systems) ✅,
**D-01–D-11** (databases) in progress.
**Next batch:** Days 113–123 — the rest of the security block: sessions, ⭐ JWT internals, token
rotation, API keys, OAuth 2.0, OIDC, authorization models, the OWASP API Top 10, SQL injection in
depth, then caching and rate limiting.

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
