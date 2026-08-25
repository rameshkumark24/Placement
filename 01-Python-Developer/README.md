# 01 — Python Developer

| File | Contents |
|---|---|
| ⭐ [**Roadmap/**](Roadmap/README.md) | **The day-by-day plan.** 488 days, Stage 0 → Stage 15, mirroring the [Java track](../02-Java-Developer/Roadmap/README.md) stage for stage |
| ⭐ [**Days/**](Days/README.md) | **The written lessons.** One file per day — ✅✅ **complete: all 488 days, 448 files** |
| [Python-Vibe-Coding-Cheatsheet.md](Python-Vibe-Coding-Cheatsheet.md) | Reference guide. Stack, project layout, Python-specific AI-code traps, API safety, security, testing, delivery |

> Universal build/security/API rules live in [`03-Web-Developer`](../03-Web-Developer/) phases
> 04–08 — they apply to any backend and are not repeated here.
> ⭐⭐ **GenAI / LLM engineering lives here** — Stage 13/14, [Days 414–461](Days/).
> SQL notes: [`06-Common/SQL`](../06-Common/SQL/).

## The plan in one table

| Days | Stage | Milestone |
|---|---|---|
| 1–22 | Ground Zero | Can explain servers, HTTP, WebSockets |
| 23–77 | **Python Mastery** | Interview-grade Python — the data model, the GIL, asyncio |
| 78–101 | Professional Engineering | Git, Linux, clean code, pytest |
| 102–129 | Backend, framework-free | Can build an API with no framework |
| 130–185 | **FastAPI & Django** | Backend interview-ready |
| 186–213 | Databases | Can fix a slow query live |
| 214–247 | Frontend → [`03-Web-Developer`](../03-Web-Developer/) | 🌐 Build the UI for your own API |
| 248–297 | Projects | Two defensible projects (271–280 🌐) |
| 298–321 | LLD | LLD rounds cleared |
| 322–359 | System Design | ✅ **COMPLETE SDE** |
| 360–413 | DevOps · AWS · Distributed Systems | Ships own work, in the cloud, at senior depth |
| 414–461 | ⭐⭐ **AI Engineering** — the model, prompting & evals, retrieval, agents, safety, production | ✅ **AI Engineer** |
| 462–488 | Interview Conversion | Offers |

Full detail, day by day: [**Roadmap/README.md**](Roadmap/README.md) · every day: [**Days/README.md**](Days/README.md)

> ⭐⭐ **Days 414–461 are also Stages 13 & 14 of the [Java track](../02-Java-Developer/)** — AI
> engineering is written once, here, and Java developers are routed in by
> [the crossing](../02-Java-Developer/Roadmap/Stage-13-14-Bridge.md).

## Default stack

Python 3.12 · uv · FastAPI · Pydantic v2 · SQLAlchemy 2.0 (async) · Alembic · Postgres ·
Celery + Redis · pytest · Ruff · mypy · Sentry

**Second framework:** Django + DRF (Stage 4B) — Python backend roles split roughly evenly between the
two, and knowing only one halves your market.

## The three that cause the most damage

1. **A blocking call inside an `async def` route** → the whole event loop stalls, every request waits.
2. **N+1 queries from lazy relationship access** → 101 queries where one would do.
3. **Returning an ORM object instead of a `response_model`** → `password_hash` in the JSON response.

All three: [Python-specific traps](Python-Vibe-Coding-Cheatsheet.md#3-python-specific-traps-in-ai-generated-code)
— and all three get a full day in the roadmap (Days 072, 4A, and Stage 3 respectively).

## The one rule

> **No AI-generated code during lessons or practice.** AI for explanation — always.
> AI writing your code — never.
