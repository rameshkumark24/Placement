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

**Stage 0: 20 of 22 written.**
**Next batch:** Days 021–022 (deployment + C-14 networks drill, then the full end-to-end trace) and
the start of Stage 1 — Java Mastery, from Day 023.

---

## Stage 0 exit gate — trackable now

- [ ] Raw TCP server speaking HTTP by hand → [Day 004](Day-004.md)
- [ ] WebSocket chat across two tabs, server hand-written → [Day 017](Day-017.md)
- [ ] 10-min oral: *"what happens when you type google.com and press Enter?"* → assembled on Day 022
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
