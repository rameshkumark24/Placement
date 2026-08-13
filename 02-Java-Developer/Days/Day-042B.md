# Day 042B ➕

**`BigDecimal` and floating point** — why `double` is fatal for money

**Time:** 2–3 hrs · **Mode:** NEW

> **This is an added day, and the second of the two critical gaps.** `0.1 + 0.2 != 0.3` is a top-ten
> Java interview question, and using `double` for money is a production bug that surfaces months later
> as accounting discrepancies nobody can trace.
>
> See [Gap-Audit.md](../Roadmap/Gap-Audit.md#2-bigdecimal-and-floating-point--day-042b).

---

# Floating point, and what to use instead

## Why `0.1 + 0.2 != 0.3`

**Binary floating point cannot represent 0.1 exactly** — for the same reason decimal cannot represent
1/3 exactly.

```
   1/3 in decimal:   0.333333333...  (never terminates)
   1/10 in binary:   0.0001100110011001100...  (never terminates)
```

IEEE 754 stores the **nearest representable value**, so `0.1` is actually about
`0.1000000000000000055511151231257827`. Add three of those and the errors compound.

```java
0.1 + 0.2                 // 0.30000000000000004
0.1 + 0.2 == 0.3          // false
1.03 - 0.42               // 0.6100000000000001
```

**Which values *are* exact?** Only those expressible as a sum of powers of two: `0.5`, `0.25`,
`0.125`, `0.75`. That's why `0.5 + 0.25 == 0.75` works perfectly and misleads you into thinking
floating point is fine.

### The structure of a `double`

```
   ┌─┬───────────┬────────────────────────────────────────────────────┐
   │S│  exponent │                    mantissa                        │
   │1│    11     │                       52                           │
   └─┴───────────┴────────────────────────────────────────────────────┘
```

**~15–17 significant decimal digits.** Past that, precision is simply gone:

```java
double d = 9007199254740993.0;      // 2^53 + 1
System.out.println(d);              // 9.007199254740992E15  — the +1 vanished
```

## Why this is fatal for money

```java
double price = 0.10;
double total = 0;
for (int i = 0; i < 100; i++) total += price;
System.out.println(total);          // 9.99999999999998  — not 10.00
```

**Now imagine that across a million transactions.** The error is small per operation and unbounded in
aggregate. Symptoms:

- Ledgers that don't balance by a few paise
- A payment of ₹0.01 more or less than the invoice
- Totals that differ depending on the order you summed them
- Reconciliation reports with unexplainable residuals

**These bugs are found months later by an accountant, not by a test.** That's what makes them
expensive.

## `BigDecimal`

**Arbitrary-precision signed decimal, stored as an unscaled integer plus a scale.**

```
   BigDecimal("123.45")  →  unscaledValue = 12345,  scale = 2
```

Because it stores decimal digits directly, `0.1` is exact.

### The constructor trap — the first thing to know

```java
new BigDecimal(0.1)         // 0.1000000000000000055511151231257827021181583404541015625
new BigDecimal("0.1")       // 0.1  ← EXACT
BigDecimal.valueOf(0.1)     // 0.1  ← uses Double.toString internally
```

**`new BigDecimal(double)` faithfully captures the double's error.** You've already lost the
precision before `BigDecimal` sees it.

> **Rule: always construct from a `String`, or use `BigDecimal.valueOf`. Never from a `double`
> literal.**

### Immutable — like `String` and `java.time`

```java
BigDecimal a = new BigDecimal("10.00");
a.add(new BigDecimal("5"));         // returns a NEW BigDecimal
System.out.println(a);              // 10.00 — unchanged

a = a.add(new BigDecimal("5"));     // ✅
```

Same pattern as Day 029 and Day 042A. Every operation returns a new instance.

### `equals` vs `compareTo` — the trap from Day 041

```java
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("1.00");

a.equals(b)        // FALSE — equals compares value AND SCALE
a.compareTo(b)     // 0     — compareTo compares numeric value only
```

**`equals` includes the scale.** `1.0` has scale 1; `1.00` has scale 2.

> **Rule: always use `compareTo` for numeric comparison. Never `equals`.**

```java
if (amount.compareTo(BigDecimal.ZERO) > 0) { ... }      // ✅ positive
if (a.compareTo(b) == 0) { ... }                        // ✅ numerically equal
if (a.equals(b)) { ... }                                // 💀 scale-sensitive
```

**And it's why `HashSet` and `TreeSet` disagree** on `BigDecimal` — Day 041's demonstration.

### Division requires a rounding mode

```java
BigDecimal.ONE.divide(new BigDecimal("3"));
// 💥 ArithmeticException: Non-terminating decimal expansion; no exact representable result

BigDecimal.ONE.divide(new BigDecimal("3"), 10, RoundingMode.HALF_UP);   // ✅ 0.3333333333
```

**`BigDecimal` refuses to silently lose precision.** That's a feature — it forces you to state how
much precision you want and how to round.

| Mode | Behaviour |
|---|---|
| `HALF_UP` | 0.5 rounds away from zero — **what people mean by "round"** |
| `HALF_EVEN` | 0.5 rounds to the nearest even — **banker's rounding**, the financial default |
| `DOWN` / `FLOOR` | Truncate |
| `UP` / `CEILING` | Away from zero / toward positive infinity |

**`HALF_EVEN` is the default in most financial systems** because `HALF_UP` has a systematic upward
bias: over many roundings it always favours one direction, and the drift accumulates. `HALF_EVEN`
distributes it. Knowing *why* is a good detail.

### `setScale` — normalising money

```java
BigDecimal amount = new BigDecimal("10.456");
amount.setScale(2, RoundingMode.HALF_EVEN);      // 10.46
```

**Set the scale explicitly at your boundaries** — when reading from a source, before storing, and
before displaying. Two amounts with different scales compare `equals`-unequal and print differently.

### `stripTrailingZeros` and the surprise

```java
new BigDecimal("600.0").stripTrailingZeros().toString();     // "6E+2"  😬
new BigDecimal("600.0").stripTrailingZeros().toPlainString(); // "600"  ✅
```

**`toString()` can produce scientific notation.** Use `toPlainString()` for anything a user sees or
anything you write to a file.

## The alternative: integer minor units

```java
long paise = 12345;        // ₹123.45
long cents = 999;          // $9.99
```

**Store money as an integer count of the smallest unit.** No floating point, no scale, exact
arithmetic, and it's what payment APIs do — **Stripe and Razorpay both take amounts in the smallest
currency unit**, which is why your web domain's
[Phase 00](../../03-Web-Developer/00-Stack-and-Services.md) notes "Stripe uses the smallest unit —
paise for INR".

| | `BigDecimal` | Integer minor units |
|---|---|---|
| Exact | ✅ | ✅ |
| Fast | Slower — object allocation | **Very fast** |
| Division / percentages | Handles it with rounding modes | Awkward — manual rounding |
| Multi-currency scales | Handles naturally | You must track decimal places per currency |
| Risk | Verbose, easy to misuse `equals` | **Overflow**, and unit confusion |

**Use `BigDecimal`** when you need division, percentages, interest or tax calculations.
**Use integer minor units** for simple storage and transfer, especially at API boundaries.

**Best practice is often both:** a `Money` value type wrapping an amount and a currency, storing
minor units internally and exposing `BigDecimal` for arithmetic.

```java
public record Money(long minorUnits, Currency currency) {
    public BigDecimal amount() {
        return BigDecimal.valueOf(minorUnits, currency.getDefaultFractionDigits());
    }
    public Money plus(Money other) {
        if (!currency.equals(other.currency)) throw new IllegalArgumentException("currency mismatch");
        return new Money(Math.addExact(minorUnits, other.minorUnits), currency);   // overflow-checked
    }
}
```

**Never represent money as a bare number.** A `long` doesn't know it's paise, and adding rupees to
dollars compiles fine. A `Money` type makes that a compile error or a runtime guard.

## Database mapping

| Java | Postgres | Note |
|---|---|---|
| `BigDecimal` | `NUMERIC(19,4)` / `DECIMAL` | ✅ exact |
| `long` (minor units) | `BIGINT` | ✅ exact |
| `double` | `DOUBLE PRECISION` | ❌ **never for money** |

**`NUMERIC` in Postgres is exact decimal**, matching `BigDecimal`. `REAL` and `DOUBLE PRECISION` are
IEEE 754 with all the same problems.

Your [architecture checklist](../../03-Web-Developer/03-Architecture-and-Data.md) already says "money
as `numeric`, never `float`". This is why.

## Where `double` is still correct

**Don't over-correct.** `double` is right for:

- Scientific and engineering computation, where inputs are measurements with their own error
- Graphics, geometry, physics
- Statistics and ML, where you're approximating anyway
- Anything where relative error of 10⁻¹⁵ is irrelevant

**`double` is wrong specifically where values are *exact decimal quantities* by definition** — money,
and anything counted rather than measured.

---

## Type this yourself

```java
import java.math.*;
import java.util.*;

public class BigDecimalDemo {
    public static void main(String[] args) {

        // ---- 1. The classic ----
        System.out.println("--- floating point ---");
        System.out.println("   0.1 + 0.2        = " + (0.1 + 0.2));
        System.out.println("   == 0.3           ? " + (0.1 + 0.2 == 0.3));
        System.out.println("   1.03 - 0.42      = " + (1.03 - 0.42));
        System.out.println("   0.5 + 0.25       = " + (0.5 + 0.25) + "   ← exact! powers of two");

        // ---- 2. Accumulating error ----
        double total = 0;
        for (int i = 0; i < 100; i++) total += 0.10;
        System.out.printf("%n   100 × 0.10 as double = %.20f%n", total);

        BigDecimal btotal = BigDecimal.ZERO;
        for (int i = 0; i < 100; i++) btotal = btotal.add(new BigDecimal("0.10"));
        System.out.println("   100 × 0.10 as BigDecimal = " + btotal);

        // ---- 3. The constructor trap ----
        System.out.println("\n--- constructor ---");
        System.out.println("   new BigDecimal(0.1)     = " + new BigDecimal(0.1));
        System.out.println("   new BigDecimal(\"0.1\")   = " + new BigDecimal("0.1"));
        System.out.println("   BigDecimal.valueOf(0.1) = " + BigDecimal.valueOf(0.1));

        // ---- 4. equals vs compareTo ----
        BigDecimal a = new BigDecimal("1.0"), b = new BigDecimal("1.00");
        System.out.println("\n--- equals vs compareTo ---");
        System.out.println("   a.equals(b)    = " + a.equals(b) + "   ← scale differs");
        System.out.println("   a.compareTo(b) = " + a.compareTo(b));
        System.out.println("   HashSet size   = " + new HashSet<>(List.of(a, b)).size());
        System.out.println("   TreeSet size   = " + new TreeSet<>(List.of(a, b)).size());

        // ---- 5. Division needs a rounding mode ----
        System.out.println("\n--- division ---");
        try { BigDecimal.ONE.divide(new BigDecimal("3")); }
        catch (ArithmeticException e) { System.out.println("   1/3 unrounded: " + e.getMessage()); }
        System.out.println("   1/3 HALF_UP(10): "
                + BigDecimal.ONE.divide(new BigDecimal("3"), 10, RoundingMode.HALF_UP));

        // ---- 6. HALF_UP vs HALF_EVEN bias ----
        System.out.println("\n--- rounding bias ---");
        BigDecimal upSum = BigDecimal.ZERO, evenSum = BigDecimal.ZERO;
        for (int i = 0; i < 10; i++) {
            BigDecimal v = new BigDecimal(i + ".5");
            upSum   = upSum.add(v.setScale(0, RoundingMode.HALF_UP));
            evenSum = evenSum.add(v.setScale(0, RoundingMode.HALF_EVEN));
        }
        System.out.println("   sum of 0.5..9.5 HALF_UP   = " + upSum   + "   ← biased upward");
        System.out.println("   sum of 0.5..9.5 HALF_EVEN = " + evenSum + "   ← balanced");

        // ---- 7. toString surprise ----
        System.out.println("\n--- toString ---");
        BigDecimal big = new BigDecimal("600.0").stripTrailingZeros();
        System.out.println("   toString()      = " + big.toString());
        System.out.println("   toPlainString() = " + big.toPlainString());

        // ---- 8. Money as minor units ----
        System.out.println("\n--- minor units ---");
        long paise = 12345;
        System.out.println("   " + paise + " paise = ₹"
                + BigDecimal.valueOf(paise, 2).toPlainString());

        // ---- 9. Precision limit of double ----
        System.out.println("\n--- double precision limit ---");
        double d = 9007199254740993.0;                 // 2^53 + 1
        System.out.printf("   2^53 + 1 as double = %.0f   ← the +1 is GONE%n", d);
    }
}
```

**Four results to record:**

1. **100 × 0.10 as a double is not 10.00.** Print it to 20 decimal places and look at it.
2. **`new BigDecimal(0.1)` shows the double's full error** — it captured the mistake rather than
   fixing it.
3. **`HashSet` size 2, `TreeSet` size 1** for `1.0` and `1.00`. Day 041's inconsistency, again.
4. **`HALF_UP` sums higher than `HALF_EVEN`** over ten values. That's the systematic bias, measured —
   and the reason financial systems use banker's rounding.

---

## Common mistakes

| Mistake | Correction |
|---|---|
| `double` for money | Errors accumulate and ledgers stop balancing. `BigDecimal` or minor units. |
| `new BigDecimal(0.1)` | Captures the double's error. Use a String or `valueOf`. |
| `equals` on `BigDecimal` | Scale-sensitive. Use `compareTo`. |
| Dividing without a rounding mode | Throws on non-terminating results. |
| `HALF_UP` for financial rounding | Systematic upward bias. Use `HALF_EVEN`. |
| `toString()` for display | Can be scientific notation. Use `toPlainString()`. |
| A bare `long` for money | Nothing records the currency or the scale. Wrap it in a type. |
| `double` columns in the database | Use `NUMERIC`/`DECIMAL`. |
| Avoiding `double` everywhere | It's correct for measurements and approximations. |

---

## Interview questions

**Q: Why doesn't `0.1 + 0.2 == 0.3`?**
> Binary floating point can't represent 0.1 or 0.2 exactly — their binary expansions are infinite — so
> each is stored as the nearest representable value and the errors compound. Only fractions that are
> sums of powers of two are exact, which is why `0.5 + 0.25 == 0.75` works and misleads people.

**Q: Why is `double` unsuitable for money?**
> Money is an exact decimal quantity, and `double` can't represent most decimal fractions exactly. The
> error is tiny per operation and unbounded in aggregate, so ledgers stop balancing and reconciliation
> reports show residuals nobody can explain. These bugs are typically found by an accountant months
> later, not by a test.

**Q: What's wrong with `new BigDecimal(0.1)`?**
> The `double` has already lost precision before `BigDecimal` sees it, so the constructor faithfully
> records the error — you get a value with dozens of digits. Construct from a `String`, or use
> `BigDecimal.valueOf`, which goes through `Double.toString`.

**Q: Why use `compareTo` rather than `equals` on `BigDecimal`?**
> `equals` compares both the unscaled value and the scale, so `1.0` and `1.00` are not equal even
> though they're numerically identical. `compareTo` compares numeric value only. It's also why a
> `HashSet` and a `TreeSet` containing both will have different sizes.

**Q: What is banker's rounding and why is it the financial default?**
> `HALF_EVEN` — a value exactly halfway rounds to the nearest even digit. `HALF_UP` always rounds
> halfway values away from zero, which introduces a systematic upward bias that accumulates across
> many operations. `HALF_EVEN` distributes the rounding in both directions so the bias cancels out.

**Q: `BigDecimal` or integer minor units?**
> Minor units — storing paise or cents as a `long` — are exact and fast, and match what payment APIs
> expect, but division and percentages are awkward and you must track decimal places per currency.
> `BigDecimal` handles division and rounding properly at the cost of allocation. Often both: a `Money`
> type storing minor units internally and exposing `BigDecimal` for arithmetic.

**Q: When is `double` still the right choice?**
> Scientific and engineering computation, graphics, statistics — anywhere the inputs are measurements
> with their own error and a relative error of 10⁻¹⁵ is irrelevant. The rule is about exact decimal
> quantities, not about floating point being bad.

---

## Mini task

1. Run `BigDecimalDemo`. Record all four highlighted results.
2. Write a shopping-cart total with three items at ₹19.99 and 18% GST. Do it in `double` and in
   `BigDecimal`. Compare to the paisa.
3. Build the `Money` record with currency checking and overflow-safe addition. Try adding INR to USD.
4. Write a percentage split — divide ₹100 three ways — and make the parts sum back to exactly ₹100.
5. Find a `double` used for a monetary value in one of your projects and convert it.

---

# 🚪 Exit questions

1. Why can't binary floating point represent 0.1? Which values *are* exact?
2. How many significant decimal digits does a `double` give?
3. Why is `double` fatal for money, and why are the bugs found late?
4. What's wrong with `new BigDecimal(0.1)`, and what are the two correct constructions?
5. Why does `equals` differ from `compareTo` on `BigDecimal`?
6. Why does `divide` throw without a rounding mode?
7. `HALF_UP` vs `HALF_EVEN` — which is the financial default and why?
8. Why can `toString()` surprise you?
9. `BigDecimal` vs minor units — when would you choose each?
10. Where is `double` still correct?

## 🎙️ Articulation drill

Two minutes: **"How would you represent money in a Java service, and why not `double`?"**

Explain the representation problem, then the accumulation problem, then your choice — and mention
that payment APIs take minor units, which shows you've integrated one.

---

**Previous:** [Day 042A](Day-042A.md) · **Next:** Day 043 — enums with fields, methods, and as state
machines *(not yet written — see [Days index](README.md))*

> **OOP block complete: Days 035–042B.** You can now explain construction order, encapsulation,
> inheritance and its costs, how dispatch physically works, the `equals`/`hashCode` contract and what
> breaks without it, modern data modelling with records and sealed types, and the two things the
> roadmap had missed entirely — dates and money.
