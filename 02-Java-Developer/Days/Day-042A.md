# Day 042A ➕

**`java.time`** — Date & Time API

**Time:** 3 hrs · **Mode:** NEW

> **This is an added day, and one of the two most serious gaps in the roadmap.** `java.time` appeared
> nowhere in it, yet you use dates in every service you will ever write, and it's asked in most Java
> interviews.
>
> You already have "`Instant` for timestamps, stored UTC, converted at display time" as a rule in
> your [vibe-coding cheatsheet](../Java-Vibe-Coding-Cheatsheet.md). Nothing taught you the
> API that implements it. See [Gap-Audit.md](../Roadmap/Gap-Audit.md#1-javatime--date--time-api--day-042a).

---

# `java.time`

## Why the old API had to be replaced

`Date` and `Calendar` are genuinely broken, and being able to list *why* is a good interview answer:

```java
Date d = new Date(2024, 1, 15);     // year 3924, FEBRUARY 15
```

| Defect | Detail |
|---|---|
| **Mutable** | `date.setTime(0)` — hence all the defensive copying of Day 034A |
| **Not thread-safe** | `SimpleDateFormat` is notoriously unsafe as a shared static field |
| **Zero-indexed months** | January is 0. December is 11. |
| **Year offset from 1900** | `new Date(124, ...)` means 2024 |
| **`Date` isn't a date** | It's an instant — milliseconds since the epoch — despite the name |
| **No concept of "just a date"** | You cannot represent a birthday without a time and zone |
| **Poor arithmetic** | Adding a month requires a `Calendar` and several lines |

**`java.time` (JSR-310, Java 8) fixes all of them.** It's immutable, thread-safe, correctly
domain-modelled, and has a fluent API.

## The core types — choosing the right one

**This is the whole lesson. Most date bugs are choosing the wrong type.**

| Type | Represents | Has a zone? | Use for |
|---|---|---|---|
| **`LocalDate`** | A date. `2026-08-13` | ❌ | Birthdays, due dates, holidays |
| **`LocalTime`** | A time of day. `14:30` | ❌ | Opening hours, alarms |
| **`LocalDateTime`** | Date + time, **no zone** | ❌ | ⚠️ Rarely what you want |
| **`Instant`** | **A point on the timeline**, UTC | Implicitly UTC | **Timestamps. Store this.** |
| **`ZonedDateTime`** | Instant + zone + rules | ✅ | Displaying to a user; scheduling |
| **`OffsetDateTime`** | Instant + fixed offset | Offset only | APIs, DB columns |
| **`Duration`** | Time-based amount — seconds, nanos | — | Elapsed time, timeouts |
| **`Period`** | Date-based amount — years, months, days | — | "3 months from now" |

### The decision rule

```
   Is it a moment in time that happened?          → Instant        (store in UTC)
   Is it a calendar date with no time?            → LocalDate
   Does it need to display in a user's zone?      → ZonedDateTime
   Is it going into a database or an API?         → Instant or OffsetDateTime
   Is it a length of clock time?                  → Duration
   Is it a length of calendar time?               → Period
```

**`LocalDateTime` is the trap.** It looks like the obvious general-purpose type and it has **no
zone**, so it doesn't identify a point in time. `2026-03-30T02:30` is a real value in London and did
not exist — the clocks jumped. **Never use it for timestamps.**

## `Duration` vs `Period` — the distinction that matters

```java
Duration d = Duration.ofHours(24);          // exactly 24 × 60 × 60 seconds
Period   p = Period.ofDays(1);              // "one calendar day"
```

**These are not the same thing**, and the difference appears exactly twice a year:

```java
ZoneId london = ZoneId.of("Europe/London");
ZonedDateTime before = ZonedDateTime.of(2026, 3, 28, 12, 0, 0, 0, london);

before.plus(Duration.ofDays(1));    // 2026-03-29T13:00  ← 24 real hours, clock shifted
before.plus(Period.ofDays(1));      // 2026-03-29T12:00  ← same wall-clock time next day
```

**`Duration` is physics. `Period` is the calendar.** "Same time tomorrow" is a `Period`. "In 24 hours"
is a `Duration`. A subscription renewing "monthly" is a `Period` — months have different lengths.

## Immutability

```java
LocalDate date = LocalDate.of(2026, 8, 13);
date.plusDays(1);                   // returns a NEW LocalDate
System.out.println(date);           // 2026-08-13 — UNCHANGED

date = date.plusDays(1);            // ✅ reassign
```

**Exactly like `String`** (Day 029). Every method returns a new instance.

**The consequences are all good:**
- Thread-safe with no synchronisation
- **No defensive copying needed** — Day 034A's `SafePeriod` collapses to a few lines
- No TOCTOU window between validating a date and using it

## Formatting and parsing

```java
DateTimeFormatter iso = DateTimeFormatter.ISO_LOCAL_DATE;              // 2026-08-13
DateTimeFormatter custom = DateTimeFormatter.ofPattern("dd MMM yyyy"); // 13 Aug 2026

String s = date.format(custom);
LocalDate parsed = LocalDate.parse("13 Aug 2026", custom);
```

**`DateTimeFormatter` is immutable and thread-safe** — so unlike `SimpleDateFormat`, it is safe as a
`static final` field.

```java
// 💀 SimpleDateFormat is NOT thread-safe. This corrupts under concurrency.
private static final SimpleDateFormat FMT = new SimpleDateFormat("yyyy-MM-dd");

// ✅ DateTimeFormatter is
private static final DateTimeFormatter FMT = DateTimeFormatter.ofPattern("yyyy-MM-dd");
```

**That shared-`SimpleDateFormat` bug is a genuine production classic** — it produces silently wrong
dates under load, intermittently. It's worth naming in an interview.

**Locale matters for anything human-facing:**

```java
DateTimeFormatter.ofPattern("dd MMMM yyyy", Locale.forLanguageTag("ta-IN"))
DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM).withLocale(Locale.UK)
```

## Time zones — where the real bugs live

```java
ZoneId kolkata = ZoneId.of("Asia/Kolkata");
ZoneId utc     = ZoneOffset.UTC;

Instant now = Instant.now();                          // a point in time, no zone
ZonedDateTime local = now.atZone(kolkata);            // the same instant, seen from Kolkata
Instant back = local.toInstant();                     // same instant again
```

**Use zone IDs (`Asia/Kolkata`), never fixed offsets (`+05:30`), for anything future-dated.**
Offsets change — governments alter DST rules and even standard offsets. A zone ID carries the rules;
an offset is a snapshot that will eventually be wrong.

### The two DST edge cases

**Spring forward — a time that doesn't exist:**

```java
// London clocks jump 01:00 → 02:00 on 29 March 2026
ZonedDateTime.of(2026, 3, 29, 1, 30, 0, 0, london);   // adjusted forward automatically
```

**Autumn back — a time that happens twice:**

```java
// London clocks go 02:00 → 01:00 on 25 October 2026
ZonedDateTime.of(2026, 10, 25, 1, 30, 0, 0, london);  // ambiguous — picks the EARLIER by default
```

**This is why "run this job at 01:30 every day" is a subtly broken requirement.** Once a year it runs
twice; once a year it doesn't run at all. Schedulers should work in UTC.

## The rule: store UTC, convert at display

```java
// ✅ Store
Instant createdAt = Instant.now();          // → TIMESTAMP WITH TIME ZONE in Postgres

// ✅ Display
createdAt.atZone(userZone)
         .format(DateTimeFormatter.ofLocalizedDateTime(FormatStyle.MEDIUM));
```

**Why:** an `Instant` is unambiguous everywhere. Store a local time and you've lost information you
cannot recover — you don't know which zone it meant, and DST makes some values ambiguous.

**Your [vibe-coding cheatsheet](../Java-Vibe-Coding-Cheatsheet.md) already says "`Instant` for
timestamps" — stored UTC, converted at display, `timestamptz` in Postgres.** This is the Java side
of that rule.

## JDBC and JPA mapping

| Java | Postgres |
|---|---|
| `Instant` / `OffsetDateTime` | `TIMESTAMP WITH TIME ZONE` (`timestamptz`) |
| `LocalDate` | `DATE` |
| `LocalTime` | `TIME` |
| `LocalDateTime` | `TIMESTAMP` — ⚠️ no zone, usually wrong |
| `Duration` | `INTERVAL`, or store as a number of seconds |

```java
@Entity
class Order {
    @Column(name = "created_at")
    private Instant createdAt;              // ✅ no @Temporal needed for java.time
}
```

**Never use `java.util.Date` in an entity.** JPA 2.2+ supports `java.time` natively.

## Testing time — the thing nobody does

```java
// 💀 untestable — depends on the real clock
if (Instant.now().isAfter(deadline)) { ... }

// ✅ inject a Clock
class Service {
    private final Clock clock;
    Service(Clock clock) { this.clock = clock; }

    boolean isOverdue(Instant deadline) { return Instant.now(clock).isAfter(deadline); }
}

// In production
new Service(Clock.systemUTC());

// In a test — time is now deterministic
new Service(Clock.fixed(Instant.parse("2026-08-13T10:00:00Z"), ZoneOffset.UTC));
```

**`Clock` exists precisely for this**, and almost nobody uses it. Injecting a `Clock` makes
time-dependent logic testable without sleeping or mocking statics — **a genuinely strong thing to
mention in an interview**, because it shows you've had to test this kind of code.

---

## Type this yourself

```java
import java.time.*;
import java.time.format.*;
import java.time.temporal.*;
import java.util.*;

public class TimeDemo {
    public static void main(String[] args) {

        // ---- 1. Immutability ----
        LocalDate date = LocalDate.of(2026, 8, 13);
        date.plusDays(1);
        System.out.println("--- immutability ---");
        System.out.println("   after plusDays(1) without reassigning: " + date);

        // ---- 2. Choosing the type ----
        System.out.println("\n--- the core types ---");
        System.out.println("   LocalDate      " + LocalDate.now());
        System.out.println("   LocalTime      " + LocalTime.now());
        System.out.println("   LocalDateTime  " + LocalDateTime.now());
        System.out.println("   Instant        " + Instant.now());
        System.out.println("   ZonedDateTime  " + ZonedDateTime.now(ZoneId.of("Asia/Kolkata")));

        // ---- 3. Duration vs Period across a DST boundary ----
        ZoneId london = ZoneId.of("Europe/London");
        ZonedDateTime before = ZonedDateTime.of(2026, 3, 28, 12, 0, 0, 0, london);
        System.out.println("\n--- Duration vs Period across DST ---");
        System.out.println("   start:                  " + before);
        System.out.println("   + Duration.ofDays(1):   " + before.plus(Duration.ofDays(1)));
        System.out.println("   + Period.ofDays(1):     " + before.plus(Period.ofDays(1)));
        System.out.println("   ↑ 24 real hours vs the same wall-clock time");

        // ---- 4. DST edge cases ----
        System.out.println("\n--- DST edges ---");
        System.out.println("   01:30 on spring-forward: "
                + ZonedDateTime.of(2026, 3, 29, 1, 30, 0, 0, london) + "   ← shifted");
        System.out.println("   01:30 on autumn-back:    "
                + ZonedDateTime.of(2026, 10, 25, 1, 30, 0, 0, london) + "   ← ambiguous, earlier chosen");

        // ---- 5. Store UTC, display local ----
        Instant stored = Instant.parse("2026-08-13T10:30:00Z");
        System.out.println("\n--- one instant, many views ---");
        for (String z : List.of("Asia/Kolkata", "Europe/London", "America/New_York", "Australia/Sydney"))
            System.out.printf("   %-20s %s%n", z,
                    stored.atZone(ZoneId.of(z))
                          .format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm z")));

        // ---- 6. Arithmetic and queries ----
        LocalDate d = LocalDate.of(2026, 8, 13);
        System.out.println("\n--- arithmetic ---");
        System.out.println("   +1 month:        " + d.plusMonths(1));
        System.out.println("   next Monday:     " + d.with(TemporalAdjusters.next(DayOfWeek.MONDAY)));
        System.out.println("   end of month:    " + d.with(TemporalAdjusters.lastDayOfMonth()));
        System.out.println("   is leap year:    " + d.isLeapYear());
        System.out.println("   days to new year:"
                + ChronoUnit.DAYS.between(d, LocalDate.of(2027, 1, 1)));

        // ---- 7. Month-end clamping ----
        System.out.println("\n--- month-end clamping ---");
        System.out.println("   Jan 31 + 1 month = " + LocalDate.of(2026, 1, 31).plusMonths(1));
        System.out.println("   ↑ clamped to the last valid day, NOT Mar 3");

        // ---- 8. Testable time ----
        Clock fixed = Clock.fixed(Instant.parse("2020-01-01T00:00:00Z"), ZoneOffset.UTC);
        System.out.println("\n--- injected Clock ---");
        System.out.println("   Instant.now(fixed) = " + Instant.now(fixed) + "   ← deterministic");

        // ---- 9. The old API, for contrast ----
        System.out.println("\n--- why the old API had to go ---");
        @SuppressWarnings("deprecation")
        Date old = new Date(2026, 8, 13);
        System.out.println("   new Date(2026, 8, 13) = " + old);
        System.out.println("   ↑ wrong year AND wrong month");
    }
}
```

**Three results worth recording:**

1. **`Duration.ofDays(1)` and `Period.ofDays(1)` give different answers** across the DST boundary.
   That's the distinction, demonstrated.
2. **`Jan 31 + 1 month` gives `Feb 28`**, not March 3. `java.time` clamps to the last valid day — good
   behaviour, but you must know it happens.
3. **`new Date(2026, 8, 13)` prints a wildly wrong date.** Year offset plus zero-indexed months.

---

## Common mistakes

| Mistake | Correction |
|---|---|
| `LocalDateTime` for timestamps | It has no zone, so it isn't a point in time. Use `Instant`. |
| Storing local times in the database | Store UTC. Local times are ambiguous across DST. |
| `SimpleDateFormat` as a static field | Not thread-safe — silently corrupts under load. `DateTimeFormatter` is safe. |
| Fixed offsets instead of zone IDs | Offsets change. Zone IDs carry the rules. |
| `Duration` where you meant `Period` | 24 hours ≠ one calendar day across DST. |
| Forgetting to reassign after `plusDays` | Immutable — the method returns a new value. |
| `java.util.Date` in new code | Mutable, badly designed. Use `java.time`. |
| `Instant.now()` inline in business logic | Untestable. Inject a `Clock`. |
| Scheduling in local time | Once a year the job runs twice or not at all. Schedule in UTC. |

---

## Interview questions

**Q: What's wrong with `Date` and `Calendar`?**
> They're mutable and not thread-safe, months are zero-indexed, the year is offset from 1900, and
> `Date` is actually an instant despite its name so you can't represent a plain calendar date.
> `SimpleDateFormat` being mutable and shared is a classic production bug. `java.time` is immutable,
> thread-safe and models the domain properly.

**Q: `LocalDateTime` vs `ZonedDateTime` vs `Instant` — when do you use each?**
> `Instant` is a point on the timeline in UTC — that's what you store and what timestamps should be.
> `ZonedDateTime` is an instant interpreted in a specific zone, for displaying to a user or for
> scheduling. `LocalDateTime` has no zone at all, so it doesn't identify a moment in time and is
> rarely the right choice — a local date-time can be ambiguous or nonexistent across a DST transition.

**Q: `Duration` or `Period`?**
> `Duration` is time-based — an exact number of seconds. `Period` is date-based — years, months and
> days. They differ across DST: adding a `Duration` of 24 hours moves the wall-clock time, while
> adding a `Period` of one day keeps it. "Same time tomorrow" is a `Period`; "in 24 hours" is a
> `Duration`.

**Q: Why store timestamps in UTC?**
> Because an instant in UTC is unambiguous everywhere, whereas a local time loses the zone and can be
> ambiguous — a time during the autumn DST transition occurs twice and one during spring doesn't
> occur at all. You convert to the user's zone only at display.

**Q: How do you test code that depends on the current time?**
> Inject a `java.time.Clock` and call `Instant.now(clock)` rather than `Instant.now()`. In production
> you supply `Clock.systemUTC()`; in tests `Clock.fixed(...)`, so time is deterministic without
> sleeping or mocking static methods.

**Q: Why prefer zone IDs to fixed offsets?**
> A zone ID like `Asia/Kolkata` carries the full set of rules including DST and any historical or
> future changes. A fixed offset is a snapshot that becomes wrong when a government changes the rules,
> which happens more often than people expect.

---

## Mini task

1. Run `TimeDemo` and record the `Duration` vs `Period` results.
2. Rewrite Day 034A's `SafePeriod` using `LocalDate`. Count the lines that disappear.
3. Write a service that checks whether something is overdue, with an injected `Clock`. Test both
   branches with no sleeping.
4. Convert an entity using `java.util.Date` to `Instant`. Check the resulting column type.
5. Write a scheduler that fires at 01:30 local time, and work out what happens on both DST days.

---

# 🚪 Exit questions

1. Name four defects in `Date`/`Calendar`.
2. Give the decision rule for choosing between the seven core types.
3. Why is `LocalDateTime` rarely what you want?
4. `Duration` vs `Period` — show the difference across DST.
5. What are the two DST edge cases and what breaks in each?
6. Why store UTC and convert at display?
7. Why is `DateTimeFormatter` safe as a static field when `SimpleDateFormat` isn't?
8. Why zone IDs rather than fixed offsets?
9. How do you make time-dependent code testable?

## 🎙️ Articulation drill

Two minutes: **"How do you handle dates and times in a service with users in multiple countries?"**

`Instant` in UTC in the database, `ZonedDateTime` for display, zone IDs not offsets, schedule in UTC,
inject a `Clock` for testability. Naming the DST edge cases is what makes this sound like experience.

---

**Previous:** [Day 042](Day-042.md) · **Tomorrow:** [Day 042B](Day-042B.md) — ➕ **added day** —
`BigDecimal` and why `double` is fatal for money
