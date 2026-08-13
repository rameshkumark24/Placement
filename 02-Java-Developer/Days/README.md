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

**✅ Stage 0 complete (22/22).** **Stage 1: 34 of 64 written** — JVM/memory, language-core, OOP and
collections blocks done.
**Next batch:** Days 054–060 — generics, type erasure, wildcards and PECS, the added reflection day
(056A), lambdas and functional interfaces, and streams.

---

## 🚪 Stage 1 exit gate — trackable now

- [ ] In-memory key-value store with TTL, concurrent and thread-safe → [053](Day-053.md)
- [ ] Custom `ArrayList` written from scratch → [046](Day-046.md)
- [ ] Custom `HashMap` written from scratch → [048](Day-048.md)
- [ ] **Whiteboard `HashMap` internals** — collisions, treeification, resize → [048](Day-048.md)
- [ ] Explain the JMM and why `volatile` doesn't make `i++` safe → Days 065–066
- [ ] Live-code a thread-safe bounded blocking queue → [043](Day-043.md)

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
