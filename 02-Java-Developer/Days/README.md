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

**✅ Stage 0 complete (22/22).** **Stage 1: 13 of 64 written** — JVM/memory and language-core blocks done.
**Next batch:** Days 035–042B — classes and constructors, encapsulation, inheritance, polymorphism,
abstract vs interface, `equals`/`hashCode`, `Comparable`, records and sealed classes, plus the two
added days `java.time` (042A) and `BigDecimal` (042B).

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
