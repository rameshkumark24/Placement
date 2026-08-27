# 🐍 Python Developer — Vibe-Coding Cheatsheet

Backend services and APIs in Python, built with an AI agent.

> The universal rules (agent discipline, API loops, security, review gates) live in
> [`03-Web-Developer`](../03-Web-Developer/) phases 04–08 — they apply to any backend.
> This file covers Python specifics only.
> ⭐⭐ **GenAI / LLM engineering is Stage 13/14 of this track** — [Days 414–461](Days/).

---

## 1. Stack

| Need | Default | Alternative | Notes |
|---|---|---|---|
| Framework | **FastAPI** | Django (batteries), Flask (tiny) | Async, typed, auto OpenAPI docs |
| Python | 3.12+ | | |
| Package manager | **uv** | Poetry, pip-tools | 10–100× faster; agents handle it fine |
| Validation | **Pydantic v2** | | Same models validate input and document the API |
| ORM | **SQLAlchemy 2.0** (async) | SQLModel, Django ORM, Tortoise | |
| Migrations | **Alembic** | Django migrations | |
| DB | Postgres (Supabase/Neon) | | |
| Auth | `fastapi-users` / Supabase Auth | | Never hand-roll |
| Background jobs | **Celery + Redis** | ARQ, Dramatiq, RQ | |
| Cache / rate limit | Redis | | |
| Testing | **pytest** + `httpx` | | |
| Lint + format | **Ruff** | | Replaces black, isort, flake8 |
| Types | **mypy** or pyright | | |
| Errors | Sentry | | |
| Deploy | Railway / Fly.io / Render | AWS ECS | |
| Containers | Docker (multi-stage) | | See [Docker notes](../06-Common/Cloud-DevOps/Docker/Docker-Kubernetes-MLOps.md) |

---

## 2. Project layout

Tell the agent this structure in `CLAUDE.md` or it will invent a new one every session.

```
app/
  main.py              # FastAPI app factory only
  config.py            # pydantic-settings, all env vars typed
  api/
    deps.py            # shared dependencies (get_db, get_current_user)
    v1/
      routes/          # one module per resource
  models/              # SQLAlchemy models
  schemas/             # Pydantic request/response models
  services/            # business logic — NOT in routes
  repositories/        # DB access — NOT in services
  core/
    security.py
    exceptions.py
tests/
alembic/
```

**The rule agents break most:** business logic ends up in the route handler. Routes should parse,
call a service, and return. Nothing else.

---

## 3. Python-specific traps in AI-generated code

### Mutable default arguments

```python
# 💀 The list is created ONCE and shared across every call
def add_item(item, items=[]):
    items.append(item)
    return items

# ✅
def add_item(item, items: list | None = None):
    items = items or []
```

### Sync calls inside async routes

```python
# 💀 Blocks the entire event loop — every other request waits
@app.get("/data")
async def get_data():
    r = requests.get("https://api.example.com")   # blocking!
    return r.json()

# ✅ Async client, with a timeout
@app.get("/data")
async def get_data():
    async with httpx.AsyncClient(timeout=10.0) as client:
        r = await client.get("https://api.example.com")
    return r.json()
```

If you must call blocking code, push it off the loop: `await asyncio.to_thread(blocking_fn)`.

### N+1 queries

```python
# 💀 1 query for orders + 1 per order for the user = 101 queries
orders = await session.scalars(select(Order))
for o in orders:
    print(o.user.name)

# ✅ One query
orders = await session.scalars(
    select(Order).options(selectinload(Order.user))
)
```

### Unbounded queries

```python
# 💀 Fine at 20 rows, fatal at 2 million
@app.get("/items")
async def list_items():
    return await session.scalars(select(Item))

# ✅ Server enforces the ceiling, regardless of what the client asks for
@app.get("/items")
async def list_items(limit: int = Query(50, le=100), cursor: str | None = None):
    ...
```

### Other frequent ones

- Catching bare `except:` and swallowing real errors — always catch specific exceptions
- Secrets read with `os.getenv` scattered everywhere instead of one typed `Settings` object
- `datetime.now()` instead of `datetime.now(timezone.utc)` — store UTC always
- Missing `await`, silently returning a coroutine object
- Session leaks — always use a dependency that closes the session

---

## 4. API safety

> Full rules: [03-Web-Developer/03-Frontend.md](../03-Web-Developer/03-Frontend.md)

```python
# Bounded retry with exponential backoff + jitter
import asyncio, random, httpx

async def fetch_with_retry(url: str, max_attempts: int = 3) -> httpx.Response:
    last_exc = None
    for attempt in range(max_attempts):
        try:
            async with httpx.AsyncClient(timeout=10.0) as client:
                r = await client.get(url)
            if 400 <= r.status_code < 500 and r.status_code != 429:
                return r                        # never retry client errors
            if r.is_success:
                return r
            last_exc = httpx.HTTPStatusError("retryable", request=None, response=r)
        except (httpx.TimeoutException, httpx.NetworkError) as e:
            last_exc = e
        await asyncio.sleep(min(2 ** attempt, 10) + random.random() * 0.3)
    raise last_exc
```

Checklist:

- [ ] Timeout on **every** outbound call — httpx defaults to none
- [ ] Retries capped, exponential, jittered; never retry 4xx except 429
- [ ] Rate limiting via `slowapi` or Redis, per user **and** per IP
- [ ] Celery tasks have `max_retries` and a dead-letter path
- [ ] No recursive task that can re-enqueue itself without a depth guard
- [ ] Webhook handlers idempotent (store the event ID, ignore repeats)
- [ ] Connection pool sized deliberately — serverless exhausts Postgres connections fast
- [ ] Pagination enforced server-side on every list endpoint

---

## 5. Security

> Full list: [03-Web-Developer/05-Security.md](../03-Web-Developer/05-Security.md)

Python-specific:

- [ ] All config through one `pydantic-settings` class — never bare `os.environ` reads
- [ ] Passwords hashed with `argon2` or `bcrypt` (via `passlib`)
- [ ] SQLAlchemy parameter binding only — never f-strings in `text()`
- [ ] `pickle` never used on untrusted data (it executes code)
- [ ] `yaml.safe_load`, never `yaml.load`
- [ ] No `eval` / `exec` / `subprocess(shell=True)` on user input
- [ ] Pydantic models used for **response** shapes too — stops accidental leaking of
      `password_hash` when you return an ORM object
- [ ] `pip-audit` in CI; verify every package the agent adds actually exists
- [ ] CORS restricted; `allow_origins=["*"]` never in production
- [ ] Debug mode off in production (`FastAPI(docs_url=None)` if the API is private)

```python
# ✅ Response model prevents field leakage
class UserOut(BaseModel):
    id: int
    email: EmailStr
    # password_hash deliberately absent

@app.get("/me", response_model=UserOut)
async def me(user: User = Depends(get_current_user)):
    return user            # extra fields are stripped, not returned
```

---

## 6. Testing

- [ ] `pytest` + `httpx.AsyncClient` for endpoint tests
- [ ] A test DB per run (testcontainers or a scratch schema), never the dev DB
- [ ] Factories (`factory_boy` / `polyfactory`) instead of hand-built fixtures
- [ ] **An authorization test per resource** — user B must get 403/404 on user A's row
- [ ] External calls mocked (`respx`) — tests must not hit the network
- [ ] `pytest --cov` on business logic; don't chase coverage on boilerplate
- [ ] Ruff + mypy in CI, failing the build

```python
# The test that matters most
async def test_user_cannot_read_another_users_note(client, user_a, user_b):
    note = await create_note(owner=user_a)
    r = await client.get(f"/notes/{note.id}", headers=auth(user_b))
    assert r.status_code in (403, 404)
```

---

## 7. Delivery

- [ ] `uv.lock` committed
- [ ] Multi-stage Dockerfile, non-root user, slim base
- [ ] `.dockerignore` excludes `.env`, `.git`, `__pycache__`, tests
- [ ] `/health` endpoint that actually checks the DB
- [ ] Structured JSON logs with a request ID; PII redacted
- [ ] Alembic migration reviewed by eye before running — agents write destructive ones
- [ ] Migrations reversible and run as part of deploy
- [ ] Gunicorn + Uvicorn workers sized to CPU
- [ ] Sentry with release tagging
- [ ] Spend and error alerts wired

---

## Drop into `CLAUDE.md`

```md
## Python rules
- FastAPI + async SQLAlchemy 2.0 + Pydantic v2. Package manager is uv.
- Business logic lives in services/, DB access in repositories/. Routes only parse and delegate.
- Every endpoint declares a response_model. Never return an ORM object directly.
- Every outbound HTTP call uses httpx with an explicit timeout.
- Every list endpoint is paginated with a server-enforced max of 100.
- Never use bare except. Never use pickle or yaml.load on untrusted input.
- All config via one pydantic-settings Settings class.
- Write Alembic migrations; do not run them.
- Run `ruff check && mypy app && pytest` before saying you are done.
```
