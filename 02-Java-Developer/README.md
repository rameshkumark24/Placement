# 02 — Java Developer

## 📅 The roadmap — start here

| | |
|---|---|
| **[Roadmap →](Roadmap/README.md)** | Day-by-day path, 341 days to Complete SDE, then Stages 13–15 |
| **[Written lessons →](Days/README.md)** | Full lesson per day — ✅✅ **complete: 416 files, Days 001–413 and 462–488** |
| ⭐⭐ **[Stages 13 & 14 →](Roadmap/Stage-13-14-Bridge.md)** | **The crossing** — Days 414–461 are AI engineering, written in Python. The exact route, and the twelve places a Java instinct is wrong. |
| **[Gap audit →](Roadmap/Gap-Audit.md)** | What was missing from the original roadmap and what I added |
| **[DSA parallel track →](Roadmap/DSA-Parallel-Track.md)** | Patterns, signals, the journal method |

**Today, if you're starting:** [Day 001](Days/Day-001.md)

> **The one rule:** no AI-generated code during lessons or practice. AI for explanation — always.
> AI writing your code — never.

---

## Path at a glance

| Days | Stage | Milestone |
|---|---|---|
| 1–22 | Ground Zero | Can explain servers, HTML, WebSockets |
| 23–77 | Java Mastery | Interview-grade Java |
| 78–101 | Professional Engineering | Git, Linux, clean code, testing |
| 102–129C | Backend Concepts | Can design an API with no framework |
| 130–185B | Spring Boot | Backend interview-ready |
| 186–213 | Databases | Can fix a slow query live |
| 214–247 | Frontend → [`03-Web-Developer`](../03-Web-Developer/) | 🌐 Build the UI for your own API |
| 248–297 | Projects | Two defensible projects (271–280 🌐) |
| 298–321 | Architecture + LLD | LLD rounds cleared |
| 322–359 | System Design | ✅ **Complete SDE** |
| 360–413 | DevOps · AWS · Distributed | Ships own work, senior-track conversations |
| 414–461 | ⭐⭐ **AI Engineering** → [the crossing](Roadmap/Stage-13-14-Bridge.md) | 🐍 ✅ **AI Engineer** — in Python |
| 462–488 | Interview Conversion | Offers |

---

## Reference material

| File | Contents |
|---|---|
| [Java-Vibe-Coding-Cheatsheet.md](Java-Vibe-Coding-Cheatsheet.md) | Building with an AI agent — stack, Spring/JPA traps, API safety, security |
| [Core/Basics.md](Core/Basics.md) · [Core/OOPs.md](Core/OOPs.md) · [Core/HashMap-Internals.md](Core/HashMap-Internals.md) · [Core/Interview-Questions.md](Core/Interview-Questions.md) | Existing interview notes |
| [Spring-Boot/Annotations-Reference.md](Spring-Boot/Annotations-Reference.md) | Saved reference |
| [Why Spring Boot](../06-Common/HR-Interview/Why-Spring-Boot.md) | The HR-round answer |

> Universal build/security/API rules live in [`03-Web-Developer`](../03-Web-Developer/) phases 04–08.

---

## The three Java mistakes that cause the most damage

1. **N+1 queries** from lazy loading in a loop → use `JOIN FETCH` *(Day 160)*
2. **Returning a JPA entity from a controller** → leaks fields, lazy-init exceptions, JSON recursion *(Day 147)*
3. **No timeouts on `RestClient`/`WebClient`** → one slow dependency exhausts the thread pool *(Day 154)*

Detail: [Java-Vibe-Coding-Cheatsheet.md](Java-Vibe-Coding-Cheatsheet.md#3-java-specific-traps-in-ai-generated-code)
