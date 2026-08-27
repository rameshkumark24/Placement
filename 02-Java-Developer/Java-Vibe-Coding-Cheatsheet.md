# ☕ Java Developer — Vibe-Coding Cheatsheet

Spring Boot services built with an AI agent.

> ⭐ **This file is self-contained.** Everything you need to build a Spring service with an agent
> is here — no other folder is a prerequisite.
> Interview material lives in [`Core/`](Core/).
>
> ⭐ [`03-Web-Developer`](../03-Web-Developer/) is a **separate domain**, not a continuation of this
> one: a vibe-coding library for Next.js/Supabase web products. Useful if you build a web app.
> Not required for anything here, and its stack is not this stack.

---

## 0. Working with the agent

> ⭐⭐ **The compiler catches what the agent got wrong about Java. Nothing catches what it got
> wrong about your domain.** A service that compiles, passes tests and returns the wrong user's
> data is the normal failure, not the rare one.

**Plan mode first — for these, always:**

| Trigger | Why |
|---|---|
| A new entity or a schema change | ⭐ A migration is hard to reverse once data is in it |
| Anything touching auth, roles or ownership | ⭐⭐ The blast radius is every user |
| A new external dependency or third-party call | Timeouts, retries and failure modes are design, not detail |
| Anything with money | `BigDecimal`, idempotency, and the audit trail are decided up front |
| Refactoring across more than ~3 files | The agent will confidently half-finish it |

```
⭐ READ THE PLAN FOR WHAT IT DOES NOT SAY:
   · which endpoint enforces ownership, and in the query or after?
   · what happens when the third-party call times out?
   · is the migration reversible, and what runs during the deploy gap?
```

**Before you accept the code:**

- [ ] ⭐⭐ **Every package it added actually exists.** Models invent plausible Maven coordinates;
      attackers register the invented ones. Check `mvnrepository.com` before `./gradlew build`.
- [ ] ⭐ **The authorization test is the one you write yourself.** User B gets 403/404 on user
      A's row. The agent will not write this test unless told, and it is the #1 real leak.
- [ ] ⭐ It did not quietly change `ddl-auto`, loosen a `@PreAuthorize`, or disable CSRF to make
      a test pass
- [ ] `./gradlew check` passes — and you ran it, not the agent reporting that it did

**The second-model cross-check** — before merging anything that touches auth, money or data:

```
Review this diff for: (1) authorization gaps — can user B reach user A's data,
(2) unbounded queries or missing pagination, (3) missing timeouts on external calls,
(4) transaction boundaries that are wrong or missing, (5) anything that logs PII.
If you find nothing, say so — do not invent findings.
```

---

## 1. Stack

| Need | Default | Notes |
|---|---|---|
| Java | **21 LTS** | Records, pattern matching, virtual threads |
| Framework | **Spring Boot 3.x** | |
| Build | **Gradle (Kotlin DSL)** or Maven | Pick one and tell the agent |
| Persistence | Spring Data JPA / Hibernate | jOOQ if the SQL is complex |
| Migrations | **Flyway** | Never `ddl-auto: update` in production |
| DB | Postgres | |
| Security | **Spring Security** + JWT | Never hand-roll auth |
| Validation | Jakarta Bean Validation | `@Valid` on every request body |
| Mapping | MapStruct | Entity ↔ DTO, compile-time |
| Testing | JUnit 5 + Testcontainers + RestAssured | |
| Docs | springdoc-openapi | |
| Observability | Actuator + Micrometer + Sentry | |
| Deploy | Docker → Railway / Fly.io / ECS | |

---

## 2. Layering — the rule agents break

```
Controller  →  Service  →  Repository  →  Entity
    ↕ DTO         ↕ domain      ↕ JPA
```

- **Controllers** parse, validate, delegate, and map to a response DTO. No logic.
- **Services** hold business logic and own `@Transactional` boundaries.
- **Repositories** do data access only.
- **Entities never leave the service layer.** Returning a JPA entity from a controller leaks
  fields, triggers lazy-loading exceptions, and creates infinite JSON recursion on bidirectional
  relationships.

```java
// 💀 Leaks passwordHash, and serialising `orders` lazily explodes
@GetMapping("/{id}")
public User get(@PathVariable Long id) { return repo.findById(id).orElseThrow(); }

// ✅ Explicit DTO
@GetMapping("/{id}")
public UserResponse get(@PathVariable Long id, @AuthenticationPrincipal Jwt jwt) {
    return userService.getForOwner(id, jwt.getSubject());
}
```

---

## 3. Java-specific traps in AI-generated code

### N+1 queries — the single most common one

```java
// 💀 1 query for orders, then 1 per order for the customer
List<Order> orders = orderRepository.findAll();
orders.forEach(o -> log.info(o.getCustomer().getName()));

// ✅ One query
@Query("SELECT o FROM Order o JOIN FETCH o.customer")
List<Order> findAllWithCustomer();
```

Turn on `spring.jpa.properties.hibernate.generate_statistics=true` in dev and watch the query count.

### `@Transactional` that does nothing

```java
// 💀 Self-invocation bypasses the proxy — no transaction is started
public void outer() { this.inner(); }
@Transactional public void inner() { ... }
```

Also: `@Transactional` on a private method is silently ignored. And a `@Transactional` method that
makes an HTTP call holds a DB connection open for the duration of that call — don't.

### Unbounded queries

```java
// 💀 findAll() on a table that grows
List<Item> all = itemRepository.findAll();

// ✅ Pageable, with a server-enforced ceiling
Page<Item> page = itemRepository.findAll(PageRequest.of(p, Math.min(size, 100)));
```

### Others

- `equals`/`hashCode` on JPA entities using a generated ID — broken before persist
- `Optional` fields on entities, or `Optional` as a method parameter
- Checked exceptions swallowed with an empty catch block
- `@Autowired` on fields instead of constructor injection (untestable)
- `LocalDateTime` where `Instant` is correct — store UTC
- `float`/`double` for money — use `BigDecimal`
- Blocking calls on virtual threads inside `synchronized` blocks (pins the carrier thread)

---

## 4. API safety

```java
// Bounded retry with backoff + jitter (Resilience4j)
@Retry(name = "externalApi")           // maxAttempts: 3, exponential, jittered in config
@CircuitBreaker(name = "externalApi", fallbackMethod = "fallback")
public Quote fetchQuote(String symbol) {
    return restClient.get().uri("/quote/{s}", symbol).retrieve().body(Quote.class);
}

private Quote fallback(String symbol, Throwable t) {
    return cache.getLastKnown(symbol);   // degrade, don't fail
}
```

```yaml
resilience4j:
  retry:
    instances:
      externalApi:
        maxAttempts: 3
        waitDuration: 1s
        enableExponentialBackoff: true
        enableRandomizedWait: true          # jitter
        retryExceptions: [java.io.IOException]
        ignoreExceptions: [com.app.BadRequestException]   # never retry 4xx
```

Checklist:

- [ ] **Connect and read timeouts set on every `RestClient`/`WebClient`** — the defaults are
      effectively infinite, and one slow dependency exhausts your thread pool
- [ ] Retries bounded with backoff and jitter; 4xx never retried
- [ ] Circuit breaker on every third-party call
- [ ] `@Scheduled` jobs: `fixedDelay` (waits for completion) not `fixedRate` (overlaps if slow)
- [ ] `@Scheduled` guarded with a lock (ShedLock) if more than one instance runs
- [ ] Rate limiting via Bucket4j or the gateway, per user and per IP
- [ ] HikariCP pool sized deliberately; connection leak detection on
- [ ] Kafka/JMS consumers have bounded retries and a dead-letter topic
- [ ] `@EventListener` handlers cannot publish an event that re-triggers themselves
- [ ] JPA cascade rules reviewed — `CascadeType.ALL` on a bidirectional mapping causes surprise
      deletes and update storms

---

## 5. Security

- [ ] `@PreAuthorize` on service methods, not just controllers
- [ ] **Ownership checked in the query**, not after loading:
      `findByIdAndOwnerId(id, currentUserId)` — this is the anti-IDOR pattern
- [ ] Method-level security enabled (`@EnableMethodSecurity`)
- [ ] CSRF configured deliberately (disabled only for stateless JWT APIs)
- [ ] CORS restricted to known origins
- [ ] Passwords via `BCryptPasswordEncoder` (strength ≥ 10)
- [ ] JWT: short expiry, refresh rotation, signature verified, `alg=none` rejected
- [ ] Actuator endpoints secured — `/actuator/env` and `/heapdump` leak everything
- [ ] `spring.jpa.hibernate.ddl-auto=validate` in production, never `update` or `create`
- [ ] No secrets in `application.properties` — env vars or a secrets manager
- [ ] Jackson: `@JsonIgnore` on sensitive fields; DTOs preferred over annotations
- [ ] Mass assignment blocked — bind to a request DTO, never to the entity
- [ ] Generic error responses via `@ControllerAdvice`; stack traces never returned
- [ ] `mvn dependency-check` / Dependabot in CI

```java
// ✅ Ownership enforced in the query, not in an if-statement afterwards
Optional<Note> note = noteRepository.findByIdAndOwnerId(id, currentUserId);
return note.orElseThrow(() -> new NotFoundException());
```

---

## 6. Testing

- [ ] Unit tests on services with Mockito
- [ ] `@DataJpaTest` + **Testcontainers** for repositories — H2 hides real Postgres behaviour
- [ ] `@SpringBootTest` + MockMvc/RestAssured for endpoints
- [ ] **An authorization test per resource** — user B gets 403/404 on user A's row
- [ ] WireMock for external HTTP dependencies
- [ ] Flyway migrations tested against a fresh DB in CI
- [ ] JaCoCo on business logic

---

## 7. Delivery

- [ ] Multi-stage Dockerfile with a JRE base and a non-root user
- [ ] `-XX:MaxRAMPercentage=75` — the JVM ignores container limits otherwise
- [ ] Actuator `/health` with readiness and liveness probes
- [ ] Structured JSON logging with a correlation ID; PII redacted
- [ ] Flyway migrations run on deploy, reviewed by eye first
- [ ] Micrometer metrics exported
- [ ] Sentry with release tagging
- [ ] Graceful shutdown enabled

---

## Drop into `CLAUDE.md`

```md
## Java rules
- Java 21, Spring Boot 3, Gradle Kotlin DSL, Postgres, Flyway.
- Layering: Controller -> Service -> Repository. Never return a JPA entity from a controller.
- Constructor injection only. No @Autowired fields.
- Every repository method that reads a user-owned row filters by owner id in the query.
- Every list endpoint is Pageable with a server-enforced max size of 100.
- Every RestClient/WebClient call sets connect and read timeouts.
- Use BigDecimal for money, Instant for timestamps.
- Write Flyway migrations; do not run them.
- ddl-auto stays 'validate'. Never change it.
- Run `./gradlew check` before saying you are done.
```
