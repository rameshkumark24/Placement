# Day 034A ➕

**Immutability & defensive copying** — how to build an object nobody can corrupt

**Time:** 2–3 hrs · **Mode:** NEW

> **This is an added day.** The original roadmap covers `final` (Day 034) and records (Day 042) but
> never the technique between them. Without it, "make it immutable" is advice you can't actually
> execute — and getting it wrong produces a class that *looks* immutable and isn't.
>
> See [Gap-Audit.md](../Roadmap/Gap-Audit.md#8-immutability--defensive-copying--day-034a).

---

# Immutability, properly

## The five requirements

Yesterday established that `final` is not immutability. Here is what actually is:

```
   1. Make the class final                      — no subclass can add mutability
   2. Make all fields private and final
   3. No setters, and no method that mutates state
   4. Defensively copy mutable objects ON THE WAY IN   (constructor)
   5. Defensively copy mutable objects ON THE WAY OUT  (getters)
```

**Requirements 4 and 5 are the ones people miss**, and missing either one makes the whole thing
worthless.

## The broken version

```java
public final class Period {
    private final Date start;
    private final Date end;

    public Period(Date start, Date end) {
        if (start.after(end)) throw new IllegalArgumentException();
        this.start = start;                 // 💀 storing the caller's object
        this.end = end;
    }

    public Date getStart() { return start; }   // 💀 handing out the internal object
    public Date getEnd()   { return end;   }
}
```

**Final class. Private final fields. No setters.** And it is completely broken — twice.

### Attack 1 — through the constructor

```java
Date start = new Date();
Date end = new Date();
Period p = new Period(start, end);

end.setTime(0);          // 💥 the caller still holds a reference
                         //    p's invariant (start before end) is now violated
```

The validation passed, then the underlying object changed. **A time-of-check-to-time-of-use bug** —
structurally identical to the argument for `String` immutability on Day 029.

### Attack 2 — through the getter

```java
Period p = new Period(start, end);
p.getEnd().setTime(0);   // 💥 mutating the object's internals from outside
```

The getter handed out a live reference to internal state.

## The fixed version

```java
public final class Period {
    private final Date start;
    private final Date end;

    public Period(Date start, Date end) {
        this.start = new Date(start.getTime());     // ✅ copy IN, BEFORE validating
        this.end   = new Date(end.getTime());
        if (this.start.after(this.end))             // ✅ validate the COPIES
            throw new IllegalArgumentException("start after end");
    }

    public Date getStart() { return new Date(start.getTime()); }   // ✅ copy OUT
    public Date getEnd()   { return new Date(end.getTime());   }
}
```

### The ordering detail that catches people

**Copy first, then validate.** Not the other way round.

```java
// 💀 SUBTLY BROKEN — a TOCTOU window
if (start.after(end)) throw new IllegalArgumentException();   // check the ORIGINAL
this.start = new Date(start.getTime());                        // copy afterwards
```

Between the check and the copy, another thread can mutate the caller's `Date`. The window is tiny —
which makes the bug rare, intermittent and horrible to reproduce.

**Always: copy, then validate the copy.** Nobody else has a reference to the copy, so it cannot
change underneath you.

## Collections

```java
public final class Team {
    private final List<String> members;

    public Team(List<String> members) {
        this.members = List.copyOf(members);          // ✅ immutable copy (Java 10+)
    }

    public List<String> getMembers() {
        return members;                                // ✅ safe — it's already immutable
    }
}
```

### The trap: `unmodifiableList` is a **view**, not a copy

```java
List<String> source = new ArrayList<>(List.of("a", "b"));
List<String> wrapped = Collections.unmodifiableList(source);

wrapped.add("c");     // ✅ throws — good
source.add("c");      // 💥 succeeds — and `wrapped` now shows THREE elements
```

**`unmodifiableList` wraps; it doesn't copy.** Whoever holds the original can still mutate it, and
the "unmodifiable" view reflects the change.

| Method | Copies? | Rejects nulls? | Notes |
|---|---|---|---|
| `Collections.unmodifiableList(l)` | ❌ **view** | No | Backing list can still change |
| `new ArrayList<>(l)` | ✅ copy | No | But the copy is mutable |
| `List.copyOf(l)` | ✅ copy | **Yes — throws NPE** | **The right default** (Java 10+) |
| `List.of(...)` | ✅ | Yes | For literals |

**Use `List.copyOf` / `Set.copyOf` / `Map.copyOf`.** They copy *and* return an immutable result — the
correct behaviour in one call.

## Shallow vs deep — the limit you must state

```java
public final class Department {
    private final List<Employee> staff;      // Employee is MUTABLE

    public Department(List<Employee> staff) {
        this.staff = List.copyOf(staff);     // copies the LIST...
    }
    public List<Employee> getStaff() { return staff; }
}

Department d = new Department(list);
d.getStaff().get(0).setSalary(999999);       // 💥 the ELEMENTS are still mutable
```

**`List.copyOf` copies the list structure, not the elements.** The list can't be added to, but every
element inside it can be mutated.

**Real immutability is transitive.** Options:

1. **Make the element type immutable too** — best, and usually a record (Day 042)
2. **Deep-copy the elements** — expensive, and needs a correct copy constructor
3. **Return copies from the getter** — expensive per call
4. **Document the limit honestly** — sometimes the right engineering answer

```java
// ✅ Option 1 — the element type is itself immutable
public record Employee(String name, BigDecimal salary) { }
```

**Being able to say "this is shallowly immutable, and here's why that's sufficient for this use"** is
a stronger answer than pretending the problem doesn't exist.

## Records make most of this automatic — but not all

```java
public record Period(LocalDate start, LocalDate end) {
    public Period {                                    // compact constructor
        if (start.isAfter(end)) throw new IllegalArgumentException();
    }
}
```

Records give you: final class, private final fields, no setters, plus `equals`, `hashCode` and
`toString`. Day 042.

**But records do NOT defensively copy.** This is important and frequently missed:

```java
public record Team(String name, List<String> members) { }

List<String> mutable = new ArrayList<>(List.of("a"));
Team t = new Team("X", mutable);
mutable.add("b");                        // 💥 t.members() now has two elements
t.members().add("c");                    // 💥 also works — it's still an ArrayList
```

**Fix it in the compact constructor:**

```java
public record Team(String name, List<String> members) {
    public Team {
        members = List.copyOf(members);        // ✅ reassign the parameter — this is the idiom
    }
}
```

**In a compact constructor you assign to the *parameter*, and the canonical constructor assigns it
to the field afterwards.** That's the correct pattern, and it's a genuinely good detail to know.

## `java.time` removes the problem

```java
// 💀 Date and Calendar are MUTABLE — hence all the copying above
Date d = new Date();
d.setTime(0);

// ✅ java.time types are immutable
LocalDate date = LocalDate.now();
date.plusDays(1);          // returns a NEW LocalDate; `date` is unchanged
```

**With `java.time`, defensive copying of dates is unnecessary.** No copying, no TOCTOU window, safe
to share across threads.

That's [Day 042A](Day-042A.md) — and it's the single best reason to never use `Date` or `Calendar`
in new code.

## When immutability is the wrong choice

Be honest about the costs:

| Cost | When it bites |
|---|---|
| Allocation per "change" | Hot loops, large objects modified frequently |
| Defensive copies | Large collections copied on every construction |
| Awkward for genuinely stateful things | Connection pools, caches, counters |

**Immutability is the right default, not a universal rule.** DTOs, value objects, configuration and
domain values should be immutable. A `ConnectionPool` should not.

## Where you already rely on this

- **`String` is immutable** — Day 029's five reasons
- **`java.time` is immutable** — Day 042A
- **Records are shallowly immutable** — Day 042
- **`final` fields get a memory-model guarantee** — safe publication without synchronisation
  (Day 034, Day 065)
- **Immutable objects are inherently thread-safe** — the single biggest reason to prefer them

**That last point is the payoff.** An immutable object can be shared across any number of threads
with zero synchronisation, zero locking, zero reasoning about visibility. Days 065–071 are entirely
about the problems you *avoid* by making things immutable.

---

## Type this yourself

```java
import java.util.*;

public class DefensiveCopy {

    // ---------- BROKEN: looks immutable, isn't ----------
    static final class BrokenPeriod {
        private final Date start, end;
        BrokenPeriod(Date start, Date end) {
            if (start.after(end)) throw new IllegalArgumentException();
            this.start = start;                       // stores the caller's object
            this.end = end;
        }
        Date getEnd() { return end; }                 // hands out internal state
        public String toString() { return start.getTime() + " .. " + end.getTime(); }
    }

    // ---------- FIXED ----------
    static final class SafePeriod {
        private final Date start, end;
        SafePeriod(Date start, Date end) {
            this.start = new Date(start.getTime());   // copy FIRST
            this.end   = new Date(end.getTime());
            if (this.start.after(this.end))           // then validate the COPIES
                throw new IllegalArgumentException();
        }
        Date getEnd() { return new Date(end.getTime()); }   // copy OUT
        public String toString() { return start.getTime() + " .. " + end.getTime(); }
    }

    record LeakyTeam(String name, List<String> members) { }

    record SafeTeam(String name, List<String> members) {
        SafeTeam { members = List.copyOf(members); }        // the compact-constructor idiom
    }

    public static void main(String[] args) {

        // ---- Attack 1: through the constructor ----
        Date s = new Date(1000), e = new Date(2000);
        BrokenPeriod broken = new BrokenPeriod(s, e);
        System.out.println("Before: " + broken);
        e.setTime(0);                                  // caller mutates its own object
        System.out.println("After:  " + broken + "   ← invariant VIOLATED");

        // ---- Attack 2: through the getter ----
        Date s2 = new Date(1000), e2 = new Date(2000);
        BrokenPeriod broken2 = new BrokenPeriod(s2, e2);
        broken2.getEnd().setTime(0);
        System.out.println("Getter attack: " + broken2 + "   ← VIOLATED");

        // ---- Both attacks fail on the fixed version ----
        Date s3 = new Date(1000), e3 = new Date(2000);
        SafePeriod safe = new SafePeriod(s3, e3);
        e3.setTime(0);
        safe.getEnd().setTime(0);
        System.out.println("\nSafePeriod after both attacks: " + safe + "   ← intact");

        // ---- unmodifiableList is a VIEW ----
        List<String> source = new ArrayList<>(List.of("a", "b"));
        List<String> view = Collections.unmodifiableList(source);
        System.out.println("\nview before: " + view);
        source.add("c");                               // mutate the BACKING list
        System.out.println("view after:  " + view + "   ← the view CHANGED");

        List<String> copy = List.copyOf(source);
        source.add("d");
        System.out.println("List.copyOf: " + copy + "   ← unaffected");

        // ---- Records do NOT copy by default ----
        List<String> members = new ArrayList<>(List.of("alice"));
        LeakyTeam leaky = new LeakyTeam("A", members);
        members.add("bob");
        System.out.println("\nLeaky record:  " + leaky + "   ← changed from outside");

        List<String> members2 = new ArrayList<>(List.of("alice"));
        SafeTeam safeTeam = new SafeTeam("B", members2);
        members2.add("bob");
        System.out.println("Safe record:   " + safeTeam + "   ← intact");
        try { safeTeam.members().add("carol"); }
        catch (UnsupportedOperationException ex) {
            System.out.println("               and the returned list rejects mutation");
        }
    }
}
```

**Run it and read every line of output.** Five separate attacks; you'll see three succeed against the
naive versions and fail against the correct ones. **That output is the lesson** — an object with
`final` fields and no setters getting corrupted twice.

---

## Common mistakes

| Mistake | Correction |
|---|---|
| `final` fields + no setters = immutable | Not if a field is a mutable object you stored or returned directly. |
| Validating before copying | TOCTOU window. Copy first, validate the copy. |
| `Collections.unmodifiableList` as a copy | It's a view. The backing list can still change. |
| `new ArrayList<>(source)` and calling it immutable | It's a copy, but a *mutable* one. |
| Assuming records defensively copy | They don't. Use `List.copyOf` in the compact constructor. |
| Thinking `List.copyOf` makes elements immutable | It's shallow. Element mutability is unchanged. |
| Using `Date`/`Calendar` in new code | Mutable, and the reason all this copying exists. Use `java.time`. |
| Making everything immutable reflexively | Wrong for genuinely stateful objects. It's a default, not a law. |

---

## Interview questions

**Q: What makes a class immutable?**
> Make it final so no subclass can add mutability, make all fields private and final, provide no
> mutators, and defensively copy any mutable object both when it's received in the constructor and
> when it's returned from a getter. The two copies are the part people miss, and missing either one
> makes the class mutable in practice.

**Q: Why copy before validating rather than after?**
> Because between validating the caller's object and copying it, another thread could mutate it —
> a time-of-check-to-time-of-use window. Copying first means you validate an object nobody else has a
> reference to, so it can't change underneath you.

**Q: What's wrong with `Collections.unmodifiableList`?**
> It returns a view, not a copy. Callers can't mutate through the view, but anyone holding the backing
> list can still change it, and those changes are visible through the view. `List.copyOf` copies and
> returns a genuinely immutable list.

**Q: Are records immutable?**
> Shallowly. The class is final, fields are private and final, and there are no setters — but records
> perform no defensive copying, so a record holding a `List` shares that list with whoever passed it
> in. The fix is `List.copyOf` in the compact constructor, assigning to the parameter.

**Q: Why is immutability valuable for concurrency?**
> An immutable object can't be in an inconsistent state, so it needs no synchronisation and can be
> shared freely across threads. The memory model also guarantees that `final` fields are visible and
> fully initialised to any thread that obtains the reference after construction, which non-final
> fields don't guarantee.

**Q: When would you not make something immutable?**
> When the object is genuinely stateful — a connection pool, a cache, a counter — or when the cost of
> allocating a new instance per change is prohibitive in a hot path. Immutability is the right
> default for values and DTOs, not a universal rule.

---

## Mini task

1. Run `DefensiveCopy`. **Record which attacks succeed against which version.**
2. Take a class in one of your projects with a collection field. Write an attack that corrupts it,
   then fix it.
3. Write a record holding a `Map` and make it properly immutable via the compact constructor.
4. Rewrite `SafePeriod` using `LocalDate`. Notice how much code disappears.
5. Build a deeply immutable `Department` containing `Employee` records.

---

# 🚪 Exit questions

1. List the five requirements for an immutable class.
2. Why does `final` + private fields + no setters fail to guarantee immutability?
3. Show both attacks on a naive class — constructor and getter.
4. Why must you copy before validating?
5. `unmodifiableList` vs `new ArrayList<>(l)` vs `List.copyOf(l)` — what does each give you?
6. Are records immutable? What exactly don't they do?
7. What is the compact-constructor idiom for defensive copying?
8. What is shallow vs deep immutability, and what are your four options?
9. Why are immutable objects thread-safe with no synchronisation?
10. When is immutability the wrong choice?

## 🎙️ Articulation drill

Two minutes: **"How would you make a class immutable, and what are the two mistakes people make?"**

The five rules, then the two attacks — constructor and getter. Being able to *attack* a naive
implementation is what makes this answer memorable.

---

**Previous:** [Day 034](Day-034.md) · **Next:** Day 035 — classes, constructors, and constructor
chaining *(not yet written — see [Days index](README.md))*

> **Language-core block: Days 028–034A complete.** You can now explain boxing, Strings, operators,
> arrays, parameter passing, `static`, `final`, and how to build something genuinely immutable.
> Next comes OOP proper.
