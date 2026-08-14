# Day 063B ➕

**L-063B** · Regex in Java — `Pattern`/`Matcher`, groups, greedy vs lazy · **catastrophic
backtracking (ReDoS)**

**Time:** 2 hrs · **Mode:** NEW · **Added day** — not in the original roadmap

> **Why this day was added.** Regex appears in the roadmap only as an assumed skill — validation in
> Stage 4, log parsing in Stage 6, `String.split` everywhere. But the one thing that makes regex
> *dangerous* is never covered: Java's engine is backtracking, so a pattern that looks fine can take
> exponential time on a 30-character input. That is a one-line denial of service, it is a real CVE
> class (CWE-1333), and it took Cloudflare's global network down in July 2019. You should be able to
> look at a validation regex and say whether it is safe.

---

# Part 1 — L-063B · The API

## `Pattern` and `Matcher`

```java
Pattern p = Pattern.compile("(\\w+)@(\\w+)\\.com");   // compile ONCE
Matcher m = p.matcher("write to bob@acme.com now");   // cheap, per input

if (m.find()) {
    System.out.println(m.group());   // bob@acme.com   — group 0 = whole match
    System.out.println(m.group(1));  // bob
    System.out.println(m.group(2));  // acme
    System.out.println(m.start() + ".." + m.end());
}
```

**`Pattern` is immutable and thread-safe; `Matcher` is stateful and is not.** So the correct usage
in a service is a `static final Pattern` field and a fresh `Matcher` per call. A `Matcher` as a
shared field is a data race, and it is a bug you will meet in real code.

```java
private static final Pattern EMAIL = Pattern.compile("...");   // ✅ shared
private Matcher matcher;                                       // 💀 never
```

`String.matches`, `String.split` and `String.replaceAll` all **compile the pattern on every call**.
That is fine once; in a loop over a million log lines it is the dominant cost. Hoist it.

Note also that `String.split` special-cases a single-character non-regex-meta pattern and is fast,
but `split("|")` or `split(".")` will surprise you — those are regex metacharacters. `split("\\.")`
is what you meant.

## match vs find, and the four methods

| Method | Anchoring |
|---|---|
| `matches()` | the **entire** input must match |
| `lookingAt()` | must match at the start, need not reach the end |
| `find()` | anywhere; repeated calls walk forward |

This is a frequent bug: `Pattern.compile("\\d+").matcher("abc123").matches()` is `false`, while
`.find()` is `true`. And `String.matches` is whole-string, unlike most other languages' `match`.

## Groups

```java
// Numbered
Matcher m = Pattern.compile("(\\d{4})-(\\d{2})-(\\d{2})").matcher("2026-08-13");

// Named — always prefer these
Pattern p = Pattern.compile("(?<year>\\d{4})-(?<month>\\d{2})-(?<day>\\d{2})");
Matcher n = p.matcher("2026-08-13");
if (n.matches()) System.out.println(n.group("year"));

// Non-capturing: group for alternation without paying for capture
Pattern.compile("(?:https?|ftp)://\\S+");
```

Group numbers are assigned by **opening-parenthesis order**, left to right, which is why inserting a
group in the middle silently renumbers everything after it. Named groups do not have that problem.
Use `(?:...)` whenever you only need grouping, not capture — it is both clearer and slightly faster.

## Quantifiers — greedy, lazy, possessive

This is where the day turns.

```java
String html = "<b>bold</b> and <i>italic</i>";

Pattern.compile("<.+>").matcher(html)    // GREEDY:  <b>bold</b> and <i>italic</i>
Pattern.compile("<.+?>").matcher(html)   // LAZY:    <b>
Pattern.compile("<.++>").matcher(html)   // POSSESSIVE: no match at all
```

| Form | Behaviour |
|---|---|
| `X*` `X+` `X?` `X{n,m}` | **Greedy** — take as much as possible, then give back on failure |
| `X*?` `X+?` `X??` | **Lazy** — take as little as possible, then take more on failure |
| `X*+` `X++` `X?+` | **Possessive** — take as much as possible and **never give back** |

Greedy `<.+>` matches to the last `>` in the string, then backtracks one character at a time until
the rest of the pattern can match. That "give back and retry" is **backtracking**, and it is both
the source of regex's expressive power and the source of the vulnerability.

Possessive quantifiers refuse to give back, which is what makes them a defence — more on that below.

---

# Part 2 — Catastrophic backtracking

## Two engine families

There are two ways to implement regex:

**Finite-automaton engines** (RE2, Go's `regexp`, Rust's `regex`) convert the pattern into a state
machine and run the input through it once. **Guaranteed O(n·m)**. The price: no backreferences, no
lookbehind.

**Backtracking engines** (Java, .NET, Python, JavaScript, PCRE) explore possibilities depth-first
and undo on failure. This buys backreferences and lookaround. The price: **worst case is
exponential in the input length**.

Java is a backtracking engine. That is the fact the whole vulnerability rests on.

## The demonstration — type this and watch

```java
import java.util.regex.*;

public class ReDoS {
    public static void main(String[] args) {
        Pattern evil = Pattern.compile("^(a+)+$");

        for (int n = 20; n <= 34; n++) {
            String input = "a".repeat(n) + "!";     // the '!' forces failure
            long t0 = System.nanoTime();
            boolean ok = evil.matcher(input).matches();
            long ms = (System.nanoTime() - t0) / 1_000_000;
            System.out.printf("n=%d  %5d ms  (%b)%n", n, ms, ok);
            if (ms > 20_000) { System.out.println("stopping"); break; }
        }
    }
}
```

Run it. The output roughly doubles every time `n` increases by one:

```
n=20      1 ms
n=24     17 ms
n=28    280 ms
n=30   1120 ms
n=32   4500 ms
n=34  18000 ms
```

**Thirty-four characters. Eighteen seconds. One CPU core, pinned.** Send ten of those concurrently
and a service is down. No payload, no injection, no authentication needed if the endpoint validates
before authenticating.

## Why it explodes

`(a+)+` against `aaaa` asks: *in how many ways can this string be partitioned into groups of one or
more `a`s?* The answer is 2^(n−1). `aaaa` can be `[aaaa]`, `[aaa][a]`, `[aa][aa]`, `[a][aaa]`,
`[aa][a][a]`, and so on. Every partition is a distinct path the engine must try before it can
conclude "no match", and the trailing `!` guarantees every path fails.

```
input "aaa!" against ^(a+)+$

  [aaa]        fail on !
  [aa][a]      fail on !
  [a][aa]      fail on !
  [a][a][a]    fail on !
  ... 2^(n-1) paths, each rejected only at the very end
```

The general shape to recognise:

> **Nested quantifiers over an overlapping character class, followed by something that can fail.**

The overlap is the essential ingredient. `(a+)+` overlaps with itself — an `a` can belong to either
level. `(ab+)+` is far less dangerous because the `a` anchors each repetition.

## Patterns that are actually dangerous

These are not contrived. Every one has appeared in real validation code:

```java
"^(\\w+\\s?)*$"                          // 💀 \w and \s? overlap on trailing input
"^([a-zA-Z0-9]+)*$"                      // 💀 textbook nested quantifier
"^(\\s*|\\t*)+$"                         // 💀 alternation of two overlapping stars
"^([^,]+,)*[^,]+$"                       // 💀 CSV-ish, blows up on a long field
"^(\\d+)*$"                              // 💀
"^([a-z]+)+@([a-z]+)+\\.[a-z]{2,}$"      // 💀 the classic "email validator"
```

That last one is the reason to be suspicious of any regex you copied from a search result to
validate an email address. **Cloudflare's 2019 global outage** was exactly this: a WAF rule
containing `(?:(?:\"|'|\\]|\\}|\\\\|\\d|(?:nan|infinity|true|false|null|undefined|symbol|math)|
\\`|\\-|\\+)+[)]*;?((?:\\s|-|~|!|{}|\\|\\||\\+)*.*(?:.*=.*)))` — the `.*(?:.*=.*)` on the end —
pushed CPU to 100% across their entire network.

## Defences, in order of preference

**1. Do not use a regex.** Email validation is the canonical example: the correct check is "does it
contain exactly one `@`, with something before and after, and does a verification email arrive?"
RFC-5322-complete regexes are famously monstrous and they do not tell you the address exists.

```java
static boolean plausibleEmail(String s) {
    int at = s.indexOf('@');
    return at > 0 && at == s.lastIndexOf('@') && at < s.length() - 1
           && s.length() <= 254 && !s.contains(" ");
}
```

**2. Bound the input before matching.** Exponential growth is only fatal when `n` can grow.

```java
if (input.length() > 256) return false;     // do this FIRST
return PATTERN.matcher(input).matches();
```

This single line converts most theoretical ReDoS into a non-issue, and it is the cheapest defence
available. Apply it to every user-supplied string you match.

**3. Remove the nesting / use possessive quantifiers or atomic groups.**

```java
"^(a+)+$"      →  "^a+$"        // the nesting added nothing
"^(\\w+\\s?)*$" →  "^[\\w\\s]*$" // flatten the class
"^(a+)+$"      →  "^(?>a+)+$"   // atomic group: no backtracking into it
"^([a-z]+)+$"  →  "^([a-z]++)+$"// possessive
```

An **atomic group** `(?>...)` discards the backtracking positions inside it once it has matched.
That is precisely what kills the 2^n path explosion. `++` is the same idea at quantifier level.

**4. Run untrusted patterns with a timeout.** If users supply the regex (a search feature, a rules
engine), you cannot audit it. Java has no built-in regex timeout, so wrap the input in a
`CharSequence` that throws when it has been read too many times:

```java
class TimeoutCharSequence implements CharSequence {
    private final CharSequence inner;
    private final long deadlineNanos;
    private int reads;

    TimeoutCharSequence(CharSequence inner, long millis) {
        this.inner = inner;
        this.deadlineNanos = System.nanoTime() + millis * 1_000_000L;
    }
    @Override public char charAt(int i) {
        if ((++reads & 0xFF) == 0 && System.nanoTime() > deadlineNanos)
            throw new RuntimeException("regex timeout");
        return inner.charAt(i);
    }
    @Override public int length() { return inner.length(); }
    @Override public CharSequence subSequence(int s, int e) {
        return new TimeoutCharSequence(inner.subSequence(s, e), 0);
    }
}

// usage
boolean ok = pattern.matcher(new TimeoutCharSequence(userInput, 1000)).matches();
```

The trick works because the engine calls `charAt` on every backtracking step, so the counter is a
proxy for work done. This is the standard Java workaround and worth knowing by name.

**5. Analyse before you ship.** Test every validation regex against
`"a".repeat(50) + "!"`-style inputs. If it does not return in milliseconds, it is a vulnerability.

---

## Common mistakes

| Mistake | Consequence |
|---|---|
| Compiling inside a loop | The compile dominates the runtime |
| `Matcher` as a shared field | Data race |
| `matches()` when you meant `find()` | Silent false negatives |
| `split(".")` or `split("|")` | Metacharacters; nothing like what you intended |
| Nested quantifiers on overlapping classes | ReDoS |
| No length bound before matching | Turns a slow pattern into an outage |
| Copy-pasted "email regex" | Usually both wrong and unsafe |
| Regex for HTML/JSON parsing | Not a regular language; use a parser |
| Regex as a security filter | Attackers out-encode filters; validate structurally |

---

## Interview questions

**Q: Is `Pattern` thread-safe?**
`Pattern` yes — immutable, share it as a `static final`. `Matcher` no — create one per use.

**Q: Greedy vs lazy vs possessive?**
Greedy takes the most and backtracks; lazy takes the least and expands; possessive takes the most
and refuses to backtrack, which prevents catastrophic behaviour at the cost of some matches.

**Q: What is ReDoS?**
A denial of service caused by a pattern whose backtracking is exponential in input length. Java uses
a backtracking engine, so a nested quantifier over an overlapping class — `(a+)+` — makes a
30-character input take tens of seconds.

**Q: How do you defend against it?**
Bound input length, avoid nested quantifiers, use atomic groups or possessive quantifiers, prefer
non-regex validation, and apply a timeout for user-supplied patterns.

**Q: Why can't you parse HTML with a regex?**
HTML is not a regular language — arbitrary nesting requires a stack, which finite automata do not
have. Backreferences and recursion in some engines blur this, but the practical answer is: use a
parser.

---

## Mini task

1. Run `ReDoS` and record the timing curve. Confirm the doubling.
2. Rewrite `^(a+)+$` as `^(?>a+)+$` and re-run at n=40. Then as `^a+$`.
3. Implement `TimeoutCharSequence` and prove it aborts the evil pattern within a second.
4. Write a log-line parser with named groups extracting timestamp, level and message from
   `2026-08-13T10:22:01Z ERROR payment failed`. Hoist the `Pattern` to a static field.
5. Take three regexes from any codebase or tutorial you have and test each against
   `"a".repeat(40) + "!"`. Report which are safe.

---

# 🚪 Exit questions

1. Which of `Pattern`/`Matcher` is thread-safe, and what is the correct usage shape?
2. Difference between `matches()`, `lookingAt()` and `find()`?
3. Why prefer named groups and `(?:...)`?
4. Explain greedy, lazy and possessive in terms of backtracking.
5. Why is `(a+)+` exponential? What is the count of paths?
6. What is the structural signature of a dangerous pattern?
7. Name the cheapest effective ReDoS defence.
8. What does an atomic group do?
9. Why does Java have no built-in regex timeout, and what is the workaround?
10. Why is an email-validation regex usually the wrong tool?

## 🎙️ Articulation drill

Two minutes: **"What is ReDoS and how would you prevent it in a Java service?"**

Backtracking engine → nested quantifier over overlapping classes → exponential paths on a failing
input → one request pins a core. Then the ladder: bound the length, flatten the pattern, atomic
groups, timeout for user-supplied patterns. Cite the Cloudflare 2019 outage if it fits naturally.

---

**Previous:** [Day 063A](Day-063A.md) · **Next:** [Day 064](Day-064.md) — threads

> Reliability block done. **The concurrency block starts tomorrow and runs to Day 072** — it is the
> hardest block in Stage 1 and the one interviews probe hardest. Day 065, the Java Memory Model, is
> the single most important day in it.
