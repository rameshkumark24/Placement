# Roadmap Gap Audit

I checked every day of the SDE roadmap against what Java backend interviews and production Java
actually require. **The roadmap is sound** — ordering is correct, Stage 3 before Stage 4 is the
right call, and the Docker primer placement fixes the one real ordering problem.

These are the genuine gaps. Each is inserted as a lettered sub-day so **your Notion day numbers
still line up** (the same convention you already used for 115b, 129A–C, 163b, 185A).

---

## Critical — these get asked and you'd have no answer

### 1. `java.time` — Date & Time API → **Day 042A**

**Completely absent from the roadmap.** This is used in every single backend service you will ever
write, and asked in most Java interviews.

Missing: `LocalDate` / `LocalDateTime` / `Instant` / `ZonedDateTime` / `OffsetDateTime`, `Duration`
vs `Period`, `DateTimeFormatter`, time zones and DST, why `Date` and `Calendar` are broken (mutable,
zero-indexed months, not thread-safe), and why you **store UTC and convert at display**.

You already have "store all timestamps in UTC" as a rule in your vibe-coding checklist. Nothing in
the roadmap teaches you the API that implements it.

### 2. `BigDecimal` and floating point → **Day 042B**

**Absent.** `0.1 + 0.2 != 0.3` is a top-10 interview question, and using `double` for money is a
production bug that shows up as accounting discrepancies months later.

Missing: IEEE 754 basics, why `double` fails for currency, `BigDecimal` construction (`new
BigDecimal("0.1")` vs `new BigDecimal(0.1)` — different values), `equals` vs `compareTo` (`2.0` and
`2.00` are *not* equal but *do* compare equal), rounding modes, scale.

### 3. Reflection & custom annotations → **Day 056A**

**Absent — and it's a prerequisite you're missing.** Day 137 (AOP), Day 140 (auto-configuration
internals) and Day 174 (`@PreAuthorize`) all assume you understand reflection. Without it, Spring
stays magic, which defeats the point of Stage 4.

Missing: `Class` objects, `getDeclaredFields` / `getDeclaredMethods`, `setAccessible`, writing your
own annotation, `@Retention` / `@Target`, reading annotations at runtime, the performance cost, and
why frameworks depend on it.

### 4. `ThreadLocal` → **Day 066A**

**Absent.** Spring's `SecurityContextHolder` and transaction synchronization are both `ThreadLocal`.
You cannot explain how Spring knows "the current user" without it — and that's a common interview
follow-up.

Missing: what it is, `InheritableThreadLocal`, **the memory leak in thread pools** (a value set on a
pooled thread outlives the request and leaks into the next one), and why virtual threads change the
calculus.

---

## Important — real gaps that bite in production or interview

### 5. Java serialization → **Day 063A**

Missing entirely. `Serializable`, `serialVersionUID`, `transient`, and — more importantly — **why
Java serialization is a security hazard** (deserialization of untrusted data is remote code
execution; it's behind a large share of historic Java CVEs), and why JSON via Jackson is what you
actually use.

### 6. Logging with SLF4J / Logback → **Day 073A**

Logging first appears at Day 182, inside Spring. But you need it from your first standalone Java
program, and the *facade* pattern (SLF4J as an interface, Logback as the implementation) is itself a
good design lesson. Also covers: levels, appenders, parameterised logging (`log.info("x={}", x)` vs
string concatenation), MDC, and what must never be logged.

### 7. Classpath & class loading in practice → **Day 076A**

Day 023 teaches the class loader as theory. Missing is the practical half you'll actually hit:
**`ClassNotFoundException` vs `NoClassDefFoundError`** (a classic interview question), classpath vs
modulepath, jar hell, transitive dependency conflicts, shading, and how to debug
`mvn dependency:tree`.

### 8. Immutability & defensive copying → **Day 034A**

Day 034 covers `final` and Day 042 covers records, but the technique between them is missing:
`final` on a `List` field doesn't make the list immutable. Defensive copying on the way in *and* on
the way out, `List.copyOf`, `Collections.unmodifiableList` and its trap (it's a view, not a copy).

### 9. Regex in Java → **Day 063B**

`Pattern` / `Matcher`, groups, named groups, greedy vs lazy, and **catastrophic backtracking
(ReDoS)** — a nested quantifier on user input is a denial-of-service vulnerability, and it's on the
OWASP list you already care about.

---

## Worth adding — smaller, but they close the set

### 10. Dependency & supply chain hygiene → **Day 100A**

Stage 2 covers Git and clean code but not dependency risk. Given you vibe-code, this is
disproportionately relevant: CVE scanning (`mvn dependency-check`, Dependabot), lockfiles,
transitive dependency resolution, licence checking, and slopsquatting (AI inventing package names
that attackers pre-register).

### 11. Spring Boot 3 specifics → **Day 185B**

Stage 4 is thorough but assumes Boot 3 without teaching what changed: the **Jakarta EE namespace
migration** (`javax.*` → `jakarta.*`, which breaks every pre-2023 tutorial you'll find), Micrometer
observability, and a GraalVM native image overview.

### 12. `wait` / `notify` / `notifyAll` → folded into **Day 067**

Implicit in the concurrency stage but never named. It's the classic producer-consumer interview
question and the foundation `BlockingQueue` is built on. Added to the existing Day 067 rather than a
new day.

---

## What I checked and found already correct

- Stage 3 before Stage 4 — correct, and unusual. Most roadmaps teach Spring's opinion of auth and
  caching instead of the concepts. Keep this.
- Docker primer at 129A–C before Testcontainers at Day 184 — correct dependency ordering.
- HashMap internals (048), JMM (065), `@Transactional` self-invocation (137/164), N+1 (160) — the
  four highest-frequency Java interview topics, all present and correctly weighted.
- OS track (B-01…B-16) and networks track (C-01…C-14) interleaved rather than blocked — correct;
  blocked theory stages are where people quit.
- `equals`/`hashCode` (040) before collections (045+) — correct ordering, most roadmaps get this
  backwards.
- Concurrency after collections — correct.

## One thing I'd change

**DSA is deferred to "your own roadmap"** and never specified. For a Java Developer track that's a
hole, because DSA is the round that gates every other round. It doesn't need to live in the day
list, but the pattern set should be written down so you can tell whether your parallel track covers
it. Added as [DSA-Parallel-Track.md](DSA-Parallel-Track.md).

---

## Net effect

| | Original | After additions |
|---|---|---|
| Stage 1 — Java Mastery | Days 023–077 (55 days) | + 9 sub-days |
| Stage 2 — Professional | Days 078–101 (24 days) | + 1 sub-day |
| Stage 4 — Spring Boot | Days 130–185A (57 days) | + 1 sub-day |

**+11 days.** Everything else stands as you wrote it.
