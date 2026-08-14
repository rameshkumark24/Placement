# Day 185B ➕ 🚪

**L-185B** · Spring Boot 3 specifics — Jakarta, Micrometer, GraalVM · **Stage 4 exit gate**

**Time:** 3+ hrs · **Mode:** NEW · **Added gap day · Stage 4 exit gate**

> Two jobs. First: the migration knowledge that comes up constantly in interviews and in real work,
> because a very large amount of Java code is still on Spring Boot 2 and someone has to move it.
> Second: **the Stage 4 exit gate** — 57 days of Spring, assembled into one build and one
> conversation.

---

# Part 1 — ⭐ The Jakarta namespace migration

**Boot 3 requires Java 17+ and Jakarta EE 9+.** Oracle's transfer of Java EE to the Eclipse
Foundation did not include the `javax` trademark, so **every package was renamed**:

```java
import javax.persistence.Entity;          // ❌ Boot 2
import jakarta.persistence.Entity;        // ⭐ Boot 3
```

| Old | New |
|---|---|
| `javax.persistence.*` | `jakarta.persistence.*` |
| `javax.servlet.*` | `jakarta.servlet.*` |
| `javax.validation.*` | `jakarta.validation.*` |
| `javax.annotation.PostConstruct` | `jakarta.annotation.PostConstruct` |
| `javax.transaction.*` | `jakarta.transaction.*` |
| ⭐ `javax.sql.DataSource`, `javax.crypto.*` | ⭐ **unchanged — these are Java SE, not EE** |

**⭐ The last row is the one people get wrong**, mass-replacing `javax` → `jakarta` and breaking
`DataSource` and every crypto import. **Only Java *EE* packages moved.**

## ⭐ Why it is not a find-and-replace

**A library compiled against `javax` cannot run on `jakarta` — the class it imports does not exist.**
So:

> **Every dependency touching servlets, JPA, validation or annotations must have a Jakarta-compatible
> version, or the migration stops.**

That is the real blocker, and it is where migrations stall: an unmaintained library with no Jakarta
release. The options are a fork, a replacement, or the Eclipse Transformer to bytecode-rewrite the
jar — all of which are project decisions, not mechanical steps.

**The tooling that does the mechanical 90%:**

```bash
mvn org.openrewrite.maven:rewrite-maven-plugin:run \
  -Drewrite.activeRecipes=org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_0
```

**OpenRewrite handles the imports, the property renames and many API changes.** Budget the saved time
for the dependency audit instead — that is where the work actually is.

## The other breaking changes worth knowing

| Change | Impact |
|---|---|
| ⭐ **Trailing slashes no longer match** | `/api/orders/` 404s (Day 146) — a silent client break |
| ⭐ `spring-boot-starter-validation` not transitive | annotations present, nothing validates (Day 148) |
| ⭐ `@MockBean` → `@MockitoBean` | deprecated in 3.4 (Day 183) |
| Auto-configuration file moved | `META-INF/spring/...AutoConfiguration.imports` (Day 140) |
| `WebSecurityConfigurerAdapter` removed | component-based config only (Day 169) |
| ⭐ `antMatchers` → `requestMatchers` | Day 169 |
| ⭐ Spring Cloud Sleuth → **Micrometer Tracing** | Day 182 |
| Hibernate 5 → 6 | ⭐ some HQL and mapping behaviour changed |
| `ProblemDetail` built in | Day 149 — delete your custom error class |
| Jackson `WRITE_DATES_AS_TIMESTAMPS` defaults shifted | verify Day 150's contract |

**⭐ Test the trailing-slash change specifically.** It breaks only the subset of clients with that
habit, so it passes every test you have and fails for real users.

## The migration order

```
1. Boot 2.7 first          ⭐ deprecation warnings are your to-do list
2. Java 17                 (Boot 2.7 supports it) — de-risk one variable at a time
3. Dependency audit        ⭐ THE step that determines feasibility
4. OpenRewrite             the mechanical 90%
5. Fix what remains        security config, Hibernate 6, tracing
6. Test hard               ⭐ trailing slashes, validation, serialization, security rules
```

**⭐ Step 1 matters more than it sounds:** on 2.7 every removed API is already a deprecation warning,
so the compiler enumerates most of your work before you change anything else.

---

# Part 2 — Micrometer and GraalVM, briefly and honestly

## Micrometer Observation — one API for three signals

```java
Observation.createNotStarted("order.place", registry)
    .lowCardinalityKeyValue("channel", channel)     // ⭐ becomes a metric tag — bounded
    .highCardinalityKeyValue("orderId", id)          // ⭐ trace attribute only — NOT a metric tag
    .observe(() -> service.place(req));
```

**⭐ The cardinality split is the design insight.** One instrumentation produces a metric (low
cardinality only — Day 143's rule enforced by the API) *and* a trace span (where high cardinality is
fine and useful). Previously you instrumented twice and kept them consistent by hand.

Sleuth is gone; this replaced it (Day 182).

## GraalVM native images — the honest position

| | JVM | Native |
|---|---|---|
| Startup | 2–4 s | ⭐ **~50 ms** |
| Memory | 200–500 MB | ⭐ 50–100 MB |
| Peak throughput | ⭐ **higher** (JIT) | lower |
| Build time | 30 s | ⭐ **5–10 min** |
| Reflection, dynamic proxies | free | ⭐ **needs hints or registration** |
| Agents, JFR, some profiling | ✅ | ⭐ limited |

**⭐ The trade in one sentence:** you exchange peak throughput, build speed and runtime dynamism for
startup time and memory.

**Worth it:** serverless, CLIs, scale-to-zero, very high pod density — anywhere **cold start is on
the request path**.

**Not worth it:** a long-running service that starts once and runs for a week. A three-second startup
amortised over seven days is nothing, and you have paid ten-minute builds and a reflection
configuration problem to save it.

**⭐ And Spring AOT helps the JVM case too** — `process-aot` shifts condition evaluation and bean
definition work to build time, giving 15–30% faster startup with none of native's constraints.
**Day 144's ranking stands: CDS and AOT are the pragmatic wins; native is a specific architectural
choice.**

---

# Part 3 — 🚪🚪 Stage 4 exit gate (Days 130–185B)

## The build — a complete service, from nothing, no reference material

**A multi-tenant order service with:**

| # | Requirement | Days |
|---|---|---|
| 1 | Package-by-feature; validated `@ConfigurationProperties`; profiles for values only | 141, 142 |
| 2 | REST API: 201+`Location`, ETag/`If-Match`→412, 204, RFC 7807 everywhere | 146, 149, 150 |
| 3 | Request/response DTOs; **no entity ever leaves the service** | 147 |
| 4 | Bean Validation at the boundary; business rules in the service | 148 |
| 5 | Keyset pagination over a **projection**; allow-listed sort with a unique tie-break | 152, 161 |
| 6 | JPA: `SEQUENCE` ids, `@Version`, `LAZY` everywhere, `default_batch_fetch_size` | 158, 159, 160 |
| 7 | ⭐ **A test asserting query count** on the list endpoint | 160 |
| 8 | `@Transactional` in the service only, `readOnly` on reads, `rollbackFor = Exception` | 164 |
| 9 | Flyway, including one **expand/contract rename across five steps** | 166 |
| 10 | Security: correctly ordered chains, argon2 + upgrade-on-login, JWT with **all five validations**, refresh rotation with family revocation | 169–173 |
| 11 | `@PreAuthorize` with a guard bean; collection auth as a **query predicate** | 174 |
| 12 | ⭐ **Multi-tenancy with PostgreSQL RLS**; tenant from the token; context cleared in `finally` | 176 |
| 13 | Outbound `RestClient` with **four timeouts**, jittered retry, circuit breaker, bulkhead | 154, 181 |
| 14 | **Outbox** + a `ShedLock`-guarded poller publishing to Kafka | 124, 178, 185A |
| 15 | ⭐ **Idempotent consumer** with a processed-events table in the same transaction | 185A |
| 16 | Actuator on a separate port, liveness/readiness split, startup probe, `build-info` | 143 |
| 17 | Structured JSON logs, MDC, tracing, alerts | 182 |
| 18 | Redis `@Cacheable` with TTL, JSON serialization, **`CacheErrorHandler`** | 168 |
| 19 | Tests: unit, slices, **Testcontainers** with real PostgreSQL, Kafka, WireMock | 183, 184 |
| 20 | Layered Docker image, `MaxRAMPercentage`, `ExitOnOutOfMemoryError`, graceful shutdown | 144, 185 |

## ⭐ The 30 questions

1. Give the two-phase container startup and the method that creates every proxy.
2. Why does self-invocation break `@Transactional`, from the mechanism?
3. Explain the singleton-with-state bug with a concrete interleaving.
4. How does auto-configuration discover candidates, and why does ordering matter?
5. Walk the DispatcherServlet chain, naming every box.
6. Why is a `@RequestBody`-less parameter both a bug and a vulnerability?
7. Name the three places `@RestControllerAdvice` cannot reach.
8. Give the four independent reasons not to expose an entity.
9. Give Day 106's four corrupting JSON types and the fix for each.
10. Filter, interceptor or AOP — give the deciding question.
11. Why must every paginated sort end on a unique column?
12. Name the four HTTP client timeouts and the one that hides pool exhaustion.
13. Why is CORS not a security feature?
14. Why do small connection pools outperform large ones?
15. Define N+1 with numbers; give three reasons eager is not the fix.
16. Why can't you `join fetch` two collections or fetch-join with `Pageable`?
17. What is dirty checking, and why is it dangerous?
18. Give three things a bulk JPQL update does not do.
19. Give the four ways `@Transactional` is silently ignored.
20. Why does `REQUIRES_NEW` risk a pool deadlock?
21. Walk the five deploys of a zero-downtime rename and say what the wait is for.
22. How does `FilterChainProxy` choose a chain, and what bug follows?
23. Where do 401 and 403 come from, and why can't advice format them?
24. Give the five JWT parse validations and the attack each prevents.
25. Why is URL security insufficient?
26. Why is RLS qualitatively different from a Hibernate `@Filter`?
27. What is wrong with the default async executor?
28. Give the Resilience4j composition order and justify the outermost.
29. Why is your test suite slow, and what fixes it?
30. Name three production behaviours H2 cannot verify.

## The articulation drill

**Ten minutes, recorded, no notes: "Walk me through your service."**

Request in, response out. **Name a decision and its trade-off at every layer** — filter chain,
serialization, validation, transaction, persistence, cache, outbound call, message publish. Then
answer three follow-ups you have not prepared, drawn from the thirty above.

**Play it back.** Two markers of readiness: you explain *mechanisms* rather than annotations, and you
say "I chose X and gave up Y" rather than "X is best practice".

---

## Common mistakes

| Mistake | Why it hurts |
|---|---|
| ⭐ Blanket `javax` → `jakarta` replace | breaks `javax.sql` and `javax.crypto` |
| Skipping the dependency audit | migration stalls at an unmaintained library |
| Not going via 2.7 first | you lose the deprecation-warning to-do list |
| ⭐ Not testing trailing slashes | silent 404s for a subset of clients |
| Assuming validation still works | starter no longer transitive |
| High-cardinality keys as metric tags | Observation makes the distinction explicit — use it |
| Native images for a long-running service | build and reflection cost for startup you do not need |
| Ignoring AOT and CDS | the cheap wins, skipped for the expensive one |

## Interview questions

**Q: What is involved in migrating Boot 2 to Boot 3?**
Java 17 and the Jakarta namespace, which is not a find-and-replace: a library compiled against
`javax` cannot run, so the dependency audit determines whether the migration is even feasible.
OpenRewrite handles the mechanical parts. Then the behavioural changes — trailing slashes no longer
match, validation is no longer transitive, the security config style changed, Sleuth became
Micrometer Tracing, and Hibernate 6 changed some HQL behaviour.

**Q: Would you use GraalVM native images?**
Where cold start is on the request path — serverless, CLIs, scale-to-zero. Not for a long-running
service: you pay ten-minute builds, reflection configuration and lower peak throughput to save three
seconds that amortise to nothing over a week of uptime. CDS and Spring AOT give a meaningful fraction
of the startup benefit with none of the constraints.

**Q: What does Micrometer Observation change?**
One instrumentation produces both a metric and a trace span, with the API forcing the cardinality
decision: low-cardinality keys become metric tags, high-cardinality ones stay on the span. Previously
you instrumented twice and kept them consistent manually.

## Mini task

**The exit-gate build is the task.** Additionally:

1. Take a Boot 2.7 sample project and migrate it. Time the OpenRewrite step, then time the dependency
   audit. **Note which dominated.**
2. Mass-replace `javax` → `jakarta` and **watch `javax.sql.DataSource` break.**
3. After migrating, call an endpoint with a trailing slash and confirm the 404.
4. Remove `starter-validation` and confirm nothing validates.
5. Instrument one operation with `Observation` and confirm a metric *and* a span appear from one call.
6. Build a native image. Time the build, the startup, and peak throughput. Compare against JVM with
   CDS. **Write the three numbers down and decide honestly.**

# 🚪 Exit questions

1. Why did the `javax` packages move, and which did not?
2. Why is the migration not mechanical, and what determines feasibility?
3. Give the six migration steps and why 2.7 comes first.
4. Name six Boot 3 behavioural changes.
5. Which one breaks silently for a subset of clients?
6. What does Micrometer Observation unify, and what does its API enforce?
7. Give the native-image trade in one sentence, and both verdicts.
8. Where does Spring AOT sit relative to native and CDS?
9. Answer all 30 gate questions, cold.

## 🎙️ Articulation drill

The **ten-minute Stage 4 drill** above is the drill. Record it. Then record a two-minute version of
"how would you migrate a Boot 2 application to Boot 3?" — and make the dependency audit the centre of
the answer, because that is what actually decides the project.

---

**Previous:** [Day 185A](Day-185A.md) · **Next:** Day 186 — Stage 5 opens *(not yet written — see
[Days index](README.md))*

> **🚪 STAGE 4 COMPLETE — Days 130–185B, 57 days.**
>
> You have built the container, the web layer, the data layer, security, and the operational layer —
> and at every step Spring arrived as an **implementation of something Stage 3 had already built by
> hand**, which is why this stage was a sequence of evaluations rather than a sequence of tutorials.
>
> **The thing to carry forward:** you can now say, of any Spring feature, *what problem it solves,
> what it does mechanically, and what it costs.* That is the difference between a candidate who
> configures Spring and one who can be trusted to design with it.
