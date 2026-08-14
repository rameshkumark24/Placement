# Day 129C 🐳 🚪

**L-129C** · Docker primer III — `docker compose`, a Postgres + Redis stack
**Closes Stage 3**

**Time:** 2 hrs · **Mode:** NEW · **Docker primer, 3 of 3**

> The practical payoff: **one file, one command, and a complete development environment.** The stack
> you build today is what you will use for Stages 4 through 7.
>
> The day closes with the Stage 3 exit gate.

---

# Part 1 — Why compose

```bash
# Without compose — every developer, every time, in the right order
docker network create appnet
docker volume create pgdata
docker run -d --name db --network appnet -v pgdata:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=... -p 5432:5432 postgres:16
docker run -d --name cache --network appnet -p 6379:6379 redis:7
docker run -d --name api --network appnet -p 8080:8080 -e DB_URL=... myapp
```

```bash
# With compose
docker compose up -d
```

**Compose is a declarative description of a multi-container environment**: services, networks,
volumes, dependencies and configuration, in one version-controlled file. That last point is the
important one — the environment becomes part of the repository, reviewed like code, and a new joiner
is productive in one command rather than a page of README instructions (Day 101).

---

# Part 2 — The stack

`compose.yaml` in your project root:

```yaml
services:

  db:
    image: postgres:16.3-alpine              # ⭐ pinned, not :latest (Day 129A)
    environment:
      POSTGRES_DB: appdb
      POSTGRES_USER: app
      POSTGRES_PASSWORD: ${DB_PASSWORD:?DB_PASSWORD is required}   # ⭐ fail fast (Day 079)
    ports:
      - "127.0.0.1:5432:5432"                # ⭐ localhost only (Day 129B)
    volumes:
      - pgdata:/var/lib/postgresql/data      # named volume — survives `down`
      - ./db/init:/docker-entrypoint-initdb.d:ro   # runs ONCE, on an empty volume
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app -d appdb"]
      interval: 5s
      timeout: 3s
      retries: 10
      start_period: 10s
    restart: unless-stopped

  cache:
    image: redis:7.2-alpine
    command: ["redis-server", "--appendonly", "yes", "--maxmemory", "256mb",
              "--maxmemory-policy", "allkeys-lru"]        # ⭐ Day 121
    ports:
      - "127.0.0.1:6379:6379"
    volumes:
      - redisdata:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      retries: 10

  api:
    build:
      context: .
      dockerfile: Dockerfile                 # Day 129B
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/appdb   # ⭐ by SERVICE NAME
      SPRING_DATASOURCE_USERNAME: app
      SPRING_DATASOURCE_PASSWORD: ${DB_PASSWORD}
      SPRING_DATA_REDIS_HOST: cache
      SPRING_PROFILES_ACTIVE: dev
    ports:
      - "8080:8080"
    depends_on:
      db:
        condition: service_healthy           # ⭐ WAIT for the healthcheck, not just for start
      cache:
        condition: service_healthy
    restart: unless-stopped

volumes:
  pgdata:
  redisdata:
```

## The details that matter

**⭐ `depends_on` with `condition: service_healthy`.** Plain `depends_on` waits only for the
container to *start*, not for the database to accept connections — so your application starts,
fails to connect, and crashes. This condition form is the fix, and it is why the healthchecks exist.

**Even so, your application must survive a database that is not ready.** Compose ordering does not
exist in Kubernetes, and a database can restart at any time. **Retry the connection with backoff at
startup** (Day 123) — depending on start order is a development convenience, not a design.

**Service names are hostnames.** `jdbc:postgresql://db:5432/appdb` works because compose creates a
network with DNS (Day 129A). `localhost` would point at the API container itself.

**Bind ports to `127.0.0.1`.** Otherwise your development database is reachable from the network
(Day 129B).

**`${DB_PASSWORD:?...}`** fails immediately with a clear message if the variable is missing — the
shell idiom from Day 079, and far better than starting with an empty password.

**`./db/init` runs only on an empty data volume.** If you change an init script and nothing happens,
that is why: `docker compose down -v` (which deletes the volume) is what re-runs it.

## Compose commands

```bash
docker compose up -d              # start everything, detached
docker compose up --build         # rebuild images first
docker compose ps
docker compose logs -f api        # follow one service
docker compose exec api sh        # shell in a running service
docker compose exec db psql -U app -d appdb    # ⭐ psql with no client install
docker compose restart api
docker compose stop               # stop, keep containers and volumes
docker compose down               # remove containers and networks — VOLUMES SURVIVE
docker compose down -v            # ⚠️ ALSO delete volumes — your data is gone
docker compose config             # ⭐ show the fully resolved file
docker compose watch              # rebuild/sync on file changes (Compose 2.22+)
```

**`docker compose config` is under-used** — it resolves variables, overrides and profiles, so it
answers "what is actually going to run?" without guessing.

---

# Part 3 — Development ergonomics

## Overrides

```yaml
# compose.override.yaml — applied AUTOMATICALLY on top of compose.yaml
services:
  api:
    build:
      target: dev
    environment:
      SPRING_PROFILES_ACTIVE: dev,debug
      JAVA_TOOL_OPTIONS: "-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005"
    ports:
      - "127.0.0.1:5005:5005"       # ⭐ debugger, bound to localhost ONLY (Day 076)
    volumes:
      - ./src:/app/src              # bind mount for live reload
```

**Keep the base file production-shaped and put development conveniences in the override**, which is
applied automatically and can be `.gitignore`d for personal preferences.

⚠️ **JDWP on `127.0.0.1` only.** Day 076: the debug port is unauthenticated remote code execution.
Binding it to `0.0.0.0` on any shared machine is a critical finding.

## Profiles

```yaml
services:
  mailhog:
    image: mailhog/mailhog
    profiles: ["tools"]           # only starts with --profile tools
```

```bash
docker compose --profile tools up -d
```

Useful for optional extras — a mail catcher, an admin UI, a seed job — that should not start by
default.

## Useful additions to the stack

```yaml
  mailhog:      { image: mailhog/mailhog, ports: ["127.0.0.1:8025:8025"] }   # catches all email
  minio:        { image: minio/minio, command: server /data --console-address ":9001" }  # S3 (Day 126)
  rabbitmq:     { image: rabbitmq:3-management }                             # Day 125
  adminer:      { image: adminer, ports: ["127.0.0.1:8090:8080"] }           # database UI
```

**MailHog is the one to add first**: it accepts SMTP and shows every message in a web UI, so
development never sends real email to a real address — a mistake that is embarrassing exactly once.

## Testcontainers — the same idea for tests

```java
@Testcontainers
class OrderRepositoryTest {
    @Container
    static PostgreSQLContainer<?> db = new PostgreSQLContainer<>("postgres:16.3-alpine");

    @DynamicPropertySource
    static void props(DynamicPropertyRegistry r) {
        r.add("spring.datasource.url", db::getJdbcUrl);
        r.add("spring.datasource.username", db::getUsername);
        r.add("spring.datasource.password", db::getPassword);
    }
}
```

**This is what made Day 097's revision to the test pyramid possible.** A real PostgreSQL, started in
seconds, disposable, isolated per test class — so a repository test exercises your actual SQL instead
of a mock that cannot catch a wrong query.

---

# Part 4 — What compose is not

**Compose is a development and small-deployment tool.** It has no scheduling, no rolling updates, no
self-healing across hosts, no horizontal scaling beyond one machine, no service mesh and no
declarative reconciliation.

```
Local development                 ✅ compose
CI test environments              ✅ compose or Testcontainers
A single small VM                 ⚠️ compose is defensible
Production, multi-host, scaled    ❌ Kubernetes, ECS, Nomad
```

**But the images are the same.** The Dockerfile you wrote yesterday runs unchanged in Kubernetes;
only the orchestration description changes. That portability is the point, and it is why the
container is the unit rather than the compose file.

Stage 10 covers Kubernetes. Everything you learned about healthchecks, signals, resource limits,
non-root users and environment configuration transfers directly.

---

## Common mistakes

| Mistake | Consequence |
|---|---|
| Plain `depends_on` without `service_healthy` | The app starts before the database accepts connections |
| Relying on start order at all | No such guarantee in production |
| `localhost` between services | Points at the container itself |
| Publishing ports on `0.0.0.0` | The development database is network-reachable |
| `:latest` images | Different bytes on different machines |
| `down -v` from habit | ⚠️ Data loss |
| Editing init scripts and expecting a re-run | They only run on an empty volume |
| Secrets committed in `compose.yaml` | Credentials in Git (Day 085) |
| Development conveniences in the base file | Debug ports and bind mounts near production config |
| JDWP on `0.0.0.0` | Unauthenticated RCE |
| Compose in production | No scheduling, no rolling updates, no self-healing |

---

## Interview questions

**Q: What does `docker compose` give you?**
A declarative, version-controlled description of a multi-container environment — services, networks,
volumes, dependencies and configuration — so the whole stack starts with one command and the
environment is reviewed like code.

**Q: Why is `depends_on` insufficient on its own?**
It waits for the container to start, not for the service to be ready. Use `condition:
service_healthy` with a healthcheck — and still make the application retry its connections, because
ordering guarantees do not exist in production.

**Q: How do containers find each other in compose?**
By service name, resolved through the network's embedded DNS. `db:5432`, not `localhost:5432`.

**Q: What is the difference between `down` and `down -v`?**
`down` removes containers and networks and keeps volumes; `-v` deletes the volumes too, which
destroys your data.

**Q: Is compose suitable for production?**
For a single small VM, arguably. Not for anything requiring scheduling, rolling updates, self-healing
or multi-host scaling — that is Kubernetes or ECS. The images are identical either way, which is what
makes the transition cheap.

**Q: What is Testcontainers and why does it matter?**
It starts real dependencies in Docker from your test code, disposable and isolated per test. It
changed the economics of integration testing — a repository test against real PostgreSQL catches SQL
errors that a mocked test never can.

---

## Mini task

1. Write the full `compose.yaml` above for a project. `docker compose up -d` and confirm all three
   services are healthy.
2. Connect to Postgres with `docker compose exec db psql` — with no client installed on your host.
3. Remove the `condition: service_healthy` and add a `sleep` to the database's start. Observe the API
   failing, then restore it.
4. Add startup connection retry to your application and confirm it survives even without
   `depends_on`.
5. Add an init SQL script, then change it and observe that nothing happens. Use `down -v` to re-run.
6. Add MailHog, send an email from the application, and read it in the web UI.
7. Add MinIO and implement Day 126's presigned upload against it locally.
8. Write a `compose.override.yaml` with the debugger port and attach your IDE. Confirm it is bound to
   `127.0.0.1` only.
9. Add a `tools` profile with Adminer and confirm it does not start by default.
10. Write a Testcontainers integration test for a repository. Break the SQL deliberately and confirm
    the test catches what a mocked test would not.
11. Run `docker compose config` and read the fully resolved output.

---

# 🚪 Exit questions

1. What does compose add over a sequence of `docker run` commands?
2. Why is plain `depends_on` insufficient, and what must the application do anyway?
3. How do services address each other?
4. Why bind published ports to `127.0.0.1` in development?
5. When do init scripts run, and what forces a re-run?
6. What survives `down`, and what does `-v` destroy?
7. What belongs in `compose.override.yaml` rather than the base file?
8. Why must JDWP be bound to localhost?
9. Where is compose appropriate, and where is it not?
10. What transfers unchanged from compose to Kubernetes?
11. What does Testcontainers change about integration testing?

---

# 🚪 Stage 3 exit gate

> **On paper, no framework: produce the full API contract for a multi-tenant SaaS product. Auth flow,
> permission model, error format, pagination, rate limits, idempotency. Defend every choice.**

Broken into checkable pieces:

### The contract document

- [ ] **Resource model and URL scheme** — nouns, ≤1 level of nesting, a stated convention for
      non-CRUD actions → [103](Day-103.md)
- [ ] **Response shape decided once** — money as a string, ISO-8601 with offset, string ids,
      uppercase enums → [103](Day-103.md), [106](Day-106.md)
- [ ] **Error contract** — RFC 7807, a stable `type` clients branch on, per-field validation codes,
      a documented `404`-versus-`403` policy → [104](Day-104.md)
- [ ] **Pagination** — keyset with opaque cursors; justify where you allow offset → [107](Day-107.md)
- [ ] **Filtering and sorting** — allow-listed fields, sortable columns indexed → [108](Day-108.md)
- [ ] **Versioning and deprecation policy**, including the client requirements paragraph →
      [109](Day-109.md)
- [ ] **An OpenAPI 3.1 spec** that a client can be generated from → [110](Day-110.md)

### Auth and permissions

- [ ] **Authentication flow chosen and defended** — sessions or tokens, with the revocation argument
      → [113](Day-113.md), [114](Day-114.md), [115](Day-115.md)
- [ ] **Password storage** — algorithm, parameters, and the reset flow's ten rules →
      [112](Day-112.md)
- [ ] **Third-party access** — API keys or OAuth, with scoping and rotation → [115b](Day-115b.md),
      [116](Day-116.md)
- [ ] **Permission model** — RBAC with ownership checks; where each check lives →
      [111](Day-111.md), [118](Day-118.md)
- [ ] **Tenant isolation** — how a forgotten `WHERE` clause is prevented from leaking data →
      [118](Day-118.md)

### Reliability and operations

- [ ] **Rate limits** — algorithm, keys, `429` response, fail-open or fail-closed →
      [122](Day-122.md)
- [ ] **Idempotency** — which endpoints, key scope, retention, fingerprinting → [123](Day-123.md)
- [ ] **Caching** — what, where, TTL, invalidation, and the tenant in every key →
      [120](Day-120.md), [121](Day-121.md)
- [ ] **Asynchronous work** — what leaves the request cycle, and the outbox → [124](Day-124.md)
- [ ] **Observability** — correlation ids, structured logs, RED metrics, liveness vs readiness →
      [127](Day-127.md), [128](Day-128.md)

### Security review of your own contract

- [ ] Walk your design against the **OWASP API Top 10** and record the answer for each →
      [119](Day-119.md)
- [ ] Name where **IDOR** could occur and the structural defence you chose → [105](Day-105.md)
- [ ] Name every place **user input reaches a query**, and the allow-list → [119b](Day-119b.md)

### Also demonstrable from this stage

- [ ] An `ETag`/`If-Match` optimistic-concurrency endpoint → [102](Day-102.md)
- [ ] Keyset pagination measured against offset at depth 50,000 → [107](Day-107.md)
- [ ] A composite index designed from a query, verified index-only in `EXPLAIN` → [110](Day-110.md)
- [ ] Write skew reproduced and fixed → [120](Day-120.md)
- [ ] A distributed rate limiter with an atomic Lua script → [122](Day-122.md)
- [ ] Presigned upload with server-generated keys and magic-byte validation → [126](Day-126.md)
- [ ] Webhook signature verification, and a sender with SSRF defences → [129](Day-129.md)
- [ ] **The compose stack running, with a Testcontainers integration test** → [129C](Day-129C.md)

## 🎙️ Articulation drill

Ten minutes, recorded: **present your API contract as if to a team**, and defend three choices you
found hardest. This is the exit gate's real test — the document is the artefact, but the defence is
the skill.

---

**Previous:** [Day 129B](Day-129B.md) · **Next:** Day 130 — Stage 4, Spring & Spring Boot
*(not yet written — see [Days index](README.md))*

> ## ✅ Stage 3 complete — Days 102–129C, 33 days.
>
> You built an entire backend's worth of decisions **without a framework**: HTTP semantics, REST
> design, error contracts, validation, serialization, pagination, versioning, authentication,
> authorization, the OWASP catalogue, caching, rate limiting, idempotency, background work,
> observability, webhooks and containers. The **D-series (D-01–D-16)** ran alongside, from why
> databases exist to indexes, transactions, isolation levels and MVCC.
>
> **Stage 4 is Spring & Spring Boot** — and it will read differently because of this ordering. When
> Spring gives you `@Transactional`, you will already know what a transaction is and what a proxy
> does (Day 056A). When it gives you Spring Security, you will already have built sessions, tokens
> and OAuth by hand. **The framework becomes a set of decisions you can evaluate rather than a set of
> annotations you memorise.** That was the point of doing it in this order.
