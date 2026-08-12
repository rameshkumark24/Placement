# 01 — Python Developer

| File | Contents |
|---|---|
| [Python-Vibe-Coding-Cheatsheet.md](Python-Vibe-Coding-Cheatsheet.md) | **Main guide.** Stack, project layout, Python-specific AI-code traps, API safety, security, testing, delivery |

> Universal rules live in [`00-Vibe-Coding-Core`](../00-Vibe-Coding-Core/) and are not repeated here.
> For ML/GenAI work in Python see [`05-ML-Engineer`](../05-ML-Engineer/).
> SQL notes: [`06-Common/SQL`](../06-Common/SQL/).

## Default stack

FastAPI · Python 3.12 · uv · Pydantic v2 · SQLAlchemy 2.0 (async) · Alembic · Postgres ·
Celery + Redis · pytest · Ruff · Sentry

## The three that cause the most damage

1. **A blocking call inside an `async def` route** → the whole event loop stalls, every request waits.
2. **N+1 queries from lazy relationship access** → 101 queries where one would do.
3. **Returning an ORM object instead of a `response_model`** → `password_hash` in the JSON response.

All three: [Python-specific traps](Python-Vibe-Coding-Cheatsheet.md#3-python-specific-traps-in-ai-generated-code)
