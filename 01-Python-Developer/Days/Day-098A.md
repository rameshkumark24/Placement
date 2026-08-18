# Day 098A

**L-098A** · ➕ 🧪 **Hypothesis** — property-based testing, and ⭐⭐ the shrinking that makes it usable

**Time:** 2–3 hrs · **Mode:** NEW · ➕ **an addition to the roadmap**

> ⭐⭐ **The sentence to own: an example test asserts what happens for the inputs you thought of, and a
> property test asserts what must be true for *all* inputs and then goes looking for a counterexample
> — which means it finds the cases you did not think of, and those are the ones in production.**
>
> ⭐ This is the shortest day in the block and the one with the highest chance of finding a real bug in
> code you already believe works.

---

# Part 1 — ⭐ The shift

```python
# ⭐ EXAMPLE-BASED — tests three inputs you chose:
def test_encode_decode():
    assert decode(encode("hello")) == "hello"
    assert decode(encode("")) == ""
    assert decode(encode("héllo")) == "héllo"

# ⭐⭐ PROPERTY-BASED — states a LAW, and hunts for a violation:
from hypothesis import given, strategies as st

@given(st.text())
def test_encode_decode_roundtrips(s):
    assert decode(encode(s)) == s
```

```
⭐⭐ WHAT ACTUALLY HAPPENS: Hypothesis generates ~100 values per run, biased
⭐   HARD toward the things that break code — empty, whitespace, "0", surrogate
⭐   pairs, combining characters, newlines, very long strings, NUL bytes.
⭐   ⇒ ⭐⭐ IT IS NOT RANDOM FUZZING. The strategies encode a large amount of
⭐        accumulated knowledge about what breaks programs, ⭐ which is why it
⭐        finds a real bug on a first attempt far more often than feels reasonable.
⭐   ⇒ ⭐ and it REMEMBERS failures in .hypothesis/ and replays them first on the
⭐        next run — so a found bug stays found.
```

---

# Part 2 — ⭐⭐ Shrinking — the feature that makes it usable

```
⭐⭐ A FUZZER FINDS:  "fails on '\U000e0041\x85\U0010ffff…' (2,847 characters)"
⭐   ⇒ ⭐ useless. You cannot debug that, and you certainly cannot put it in a
⭐        bug report.
⭐
⭐ ⭐⭐ HYPOTHESIS FINDS THE SAME BUG AND THEN SHRINKS IT:
⭐   Falsifying example: test_encode_decode_roundtrips(s='\x85')
⭐   ⇒ ⭐⭐ ONE CHARACTER. Now it is obvious — that is a Unicode NEXT LINE, and
⭐        something in the encoder is normalising line endings.
⭐   ⇒ ⭐⭐ SHRINKING IS THE WHOLE DIFFERENCE BETWEEN A CURIOSITY AND A TOOL.
⭐        The library repeatedly simplifies the input — shorter strings, smaller
⭐        numbers, fewer list items — while the test still fails, and reports the
⭐        MINIMAL reproducer.
```

```python
from hypothesis import given, example, settings, strategies as st

@given(st.decimals(min_value=0, max_value=10**6, places=2))
@example(Decimal("0.005"))               # ⭐⭐ PIN a case Hypothesis found once
@settings(max_examples=500)              # ⭐ more search for critical code
def test_money_formats_and_parses(amount):
    assert parse_money(format_money(amount)) == amount
```

⭐⭐ **`@example` is how a property test becomes a regression test.** ⭐ When Hypothesis finds a
falsifying case, pin it with `@example` — **now that specific input runs on every future run, in
milliseconds, forever**, regardless of what the generator happens to produce.

---

# Part 3 — ⭐⭐ Finding properties (the actual skill)

```
⭐⭐ "I CANNOT THINK OF A PROPERTY" IS THE ONLY REAL BARRIER. HERE ARE THE SIX
⭐   SHAPES THAT COVER ALMOST EVERYTHING:
⭐
⭐ 1. ⭐⭐ ROUND TRIP — the easiest and highest-yield.
⭐      parse(format(x)) == x · decode(encode(x)) == x · from_json(to_json(x)) == x
⭐      ⇒ ⭐ applies to every serialiser, parser, encoder and migration you write.
⭐
⭐ 2. ⭐⭐ INVARIANTS — what must always be true of the OUTPUT.
⭐      sorted(xs) is ordered · len(sorted(xs)) == len(xs)
⭐      ⇒ ⭐⭐ AND THE ONE PEOPLE FORGET: Counter(sorted(xs)) == Counter(xs) —
⭐        ⭐ without it, `return []` passes both of the first two.
⭐
⭐ 3. ⭐ IDEMPOTENCE — f(f(x)) == f(x).
⭐      normalise, sanitise, dedupe, absolute paths, ⭐ and every PUT endpoint
⭐
⭐ 4. ⭐⭐ ORACLE — compare against a slow, obviously-correct implementation.
⭐      fast_search(xs, k) == (k in xs) · ⭐ your cache == the plain dict
⭐      ⇒ ⭐⭐ the single best technique when optimising: keep the naive version
⭐        as the test oracle.
⭐
⭐ 5. ⭐ ALGEBRAIC — commutativity, associativity, identity.
⭐      merge(a, b) == merge(b, a) · union(x, empty) == x
⭐
⭐ 6. ⭐⭐ METAMORPHIC — how the output must CHANGE when the input changes,
⭐      when you cannot state the output itself.
⭐      ⇒ ⭐ "adding an item never decreases the total" · "a longer window never
⭐        allows fewer requests" (Day 096's rate limiter!) · "sorting then
⭐        filtering == filtering then sorting"
⭐      ⇒ ⭐⭐ this is the shape that unlocks property testing for BUSINESS logic,
⭐        where there is no closed-form answer to assert.
```

```python
# ⭐⭐ A REAL ONE — the property that catches a genuine money bug (Day 041):
@given(st.lists(st.decimals(min_value=0, max_value=1000, places=2), min_size=1))
def test_splitting_a_bill_loses_no_money(amounts):
    total = sum(amounts)
    shares = split_evenly(total, len(amounts))
    assert sum(shares) == total          # ⭐⭐ THE INVARIANT THAT MATTERS
# ⇒ ⭐ this finds the rounding bug where £10 split three ways becomes £9.99.
```

---

# Part 4 — ⭐ Strategies, settings, and where it fits

```python
st.integers(min_value=0)          st.text(min_size=1)       st.booleans()
st.floats(allow_nan=False)        st.decimals(places=2)     st.datetimes()
st.lists(st.integers(), min_size=1, max_size=100)
st.dictionaries(st.text(), st.integers())
st.sampled_from(Status)                                     # ⭐ an enum (Day 058)
st.builds(Order, total=st.decimals(min_value=0), status=st.sampled_from(Status))  # ⭐⭐ objects
st.one_of(st.none(), st.integers())                         # ⭐ Optional[int]

@st.composite                                               # ⭐ dependent values
def order_with_items(draw):
    n = draw(st.integers(1, 5))
    return Order(items=[draw(item_strategy()) for _ in range(n)])
```

```
⚠️ ⭐⭐ THE CI PROBLEM NOBODY WARNS YOU ABOUT: Hypothesis has a per-example
⭐    DEADLINE (200 ms by default) and fails the test if an example exceeds it.
⭐    ⇒ ⭐ a slow, loaded CI runner ⇒ ⭐⭐ FLAKY FAILURES THAT ARE NOTHING TO DO
⭐         WITH YOUR CODE — exactly the thing Day 092 says destroys trust.
⭐    ⇒ ⭐ FIX: @settings(deadline=None) for anything touching I/O, and keep
⭐         property tests on PURE functions where the deadline is irrelevant.
⭐    ⇒ ⭐ also: derandomize=True for reproducible CI runs, and a bigger
⭐         max_examples on a nightly job than on every PR.
```

```
⭐⭐ WHERE IT FITS — AND IT IS A SUPPLEMENT, NOT A REPLACEMENT:
⭐   ⭐ USE IT FOR: parsers, serialisers, encoders, money and rounding, data
⭐     structures, anything with an inverse, anything you optimised (oracle),
⭐     and validation logic.
⭐   ⚠️ ⭐ NOT FOR: "does the endpoint return 200", CRUD glue, or anything whose
⭐     only property would be a restatement of the implementation.
⭐   ⇒ ⭐⭐ KEEP YOUR EXAMPLE TESTS. They document intent readably — a property
⭐        test says "this law holds", ⭐ an example test says "a £100 order for a
⭐        pro customer costs £90", and a reader needs both.
```

⭐ **`hypothesis.stateful.RuleBasedStateMachine`** goes one level further: you declare operations and
invariants, and it generates *sequences* of calls looking for an order that breaks something.
⭐⭐ **That is the tool for a cache, a queue, a state machine or a rate limiter** — and it finds the
"only if you do A then B twice" bugs that no example test would ever contain.

---

## Common mistakes

| Mistake | Correction |
|---|---|
| ⭐ "It is just fuzzing" | ⭐⭐ Strategies are biased toward known-breaking values, and it **shrinks**. |
| Not pinning a found failure | ⭐⭐ `@example(...)` — turn it into a permanent regression test. |
| ⭐ Restating the implementation as the property | ⭐ Then it passes by construction and tests nothing. |
| ⭐⭐ **Only asserting length and order for a sort** | ⭐⭐ `return []` passes both. Assert the multiset. |
| Property tests over I/O | ⭐ Slow, and the deadline makes them flaky. Keep them pure. |
| ⭐ Deadline failures in CI | ⭐ `deadline=None`, or move the test off I/O. |
| Deleting example tests | ⭐⭐ They document intent; properties document laws. Keep both. |
| ⭐ Giving up at "I cannot think of a property" | ⭐⭐ Try the six shapes — round trip and metamorphic cover most code. |
| Huge `max_examples` on every PR | ⭐ Small on PRs, large on a nightly run. |

---

## Interview questions

**Q: What is property-based testing?**
> Instead of asserting what happens for inputs I chose, I state a property that must hold for all
> inputs and let the library search for a counterexample. So rather than three examples of
> encode/decode, I assert that decoding an encoded string always returns the original, and Hypothesis
> generates a hundred values biased toward things that break code — empty strings, combining
> characters, NUL bytes, huge inputs. The value is that it finds cases I didn't think of, and those
> are the ones that reach production.

**Q: What makes Hypothesis more useful than a fuzzer?**
> Shrinking. A fuzzer tells you it failed on a 2,847-character string of exotic Unicode, which you
> can't debug and can't put in a bug report. Hypothesis takes that failure and repeatedly simplifies
> the input while the test still fails, then reports the minimal reproducer — often one character.
> That's the difference between a curiosity and a tool. It also records failures and replays them
> first on the next run, and I can pin one with `@example` so it becomes a permanent regression test
> that runs in milliseconds.

**Q: How do you find a property when the answer isn't obvious?**
> I work through a few shapes. Round trip is the highest-yield and applies to every parser,
> serialiser and encoder. Invariants — what must be true of the output — with the caveat that you need
> enough of them: asserting a sort's output is ordered and the same length still passes for `return
> []`, so you need the multiset too. Idempotence for anything normalising. An oracle — comparing
> against a slow obviously-correct version — which is the best technique when you've optimised
> something. And metamorphic properties for business logic: I may not be able to state the total, but
> I can state that adding an item never decreases it, or that a longer rate-limit window never allows
> fewer requests.

**Q: Where would you not use it?**
> Anywhere the only property would restate the implementation, and anywhere involving I/O. The I/O
> case is practical: Hypothesis has a per-example deadline, so on a loaded CI runner you get flaky
> failures that have nothing to do with your code — which is precisely the thing that destroys trust
> in a suite. So I keep property tests on pure functions, and I keep the example tests alongside them,
> because a property says "this law holds" while an example says "a £100 order for a pro customer
> costs £90", and a reader wants both.

---

## Mini task

1. ⭐⭐ Write a round-trip property for something you already have — JSON serialisation, a URL
   encoder, a date formatter. **Give it thirty seconds to find a bug.**
2. ⭐⭐ Write `sorted` properties: ordered, same length. **Then confirm `return []` passes both**, and
   add the multiset assertion.
3. ⭐⭐ Write the bill-splitting property from Part 3 against a naive `split_evenly`. **Watch it find
   the rounding loss.**
4. ⭐ Take the falsifying example it reports and **pin it with `@example`.**
5. ⭐⭐ Write a metamorphic property for your Day 096 rate limiter: *a longer window never allows fewer
   requests.*
6. ⭐ Use `st.builds` to generate domain objects, and `@st.composite` for one where fields depend on
   each other.
7. ⭐⭐ Write an **oracle** test: a fast implementation compared against the obvious slow one.
8. ⭐ Trigger a deadline failure deliberately (add a `sleep`) and then fix it with `deadline=None`.
9. ⭐⭐ Try `RuleBasedStateMachine` on a small cache or queue with an invariant, and let it search for
   an ordering that breaks it.
10. ⭐ Delete the `.hypothesis/` directory and re-run. **Explain what changed.**

---

# 🚪 Exit questions

Answer aloud, no notes.

1. What is the difference between an example test and a property test?
2. Why is Hypothesis not just fuzzing?
3. What is shrinking, and why does it decide whether the tool is usable?
4. What does `@example` do, and why does it matter?
5. Name the six property shapes.
6. Why are "ordered" and "same length" insufficient for a sort?
7. What is an oracle property, and when is it the best tool?
8. Give a metamorphic property for a rate limiter.
9. Why do property tests over I/O go flaky in CI?
10. Where does property testing not belong?
11. Why keep example tests?
12. What does `RuleBasedStateMachine` add?

## 🎙️ Articulation drill

Record two minutes: **"Have you used property-based testing?"**

⭐ **Define it by contrast, in one sentence:** **"instead of asserting what happens for the three
inputs I thought of, I state a law that should hold for all inputs and let the library hunt for a
counterexample. So rather than three encode/decode examples, I assert that decoding an encoded string
always gives back the original."**

⭐⭐ **Then name the feature that makes it practical, because this is the part that separates having
used it from having heard of it:** *"the thing that makes it usable rather than annoying is shrinking.
A plain fuzzer tells you it failed on a three-thousand-character string of exotic Unicode, which you
can't debug. Hypothesis takes that failure and simplifies it while it still fails, and reports the
minimal case — often a single character. At that point the bug is usually obvious. And you pin it with
`@example` so it becomes a permanent regression test."*

⭐⭐ **Then show you can actually find properties, which is the real skill:** *"the barrier is always
'I can't think of a property', so I work through shapes. Round trip covers every parser and
serialiser. Invariants — though you need enough of them, because asserting a sort is ordered and the
same length still passes for `return []`. An oracle, comparing an optimised version against the naive
one, which is the best technique I know when optimising. And metamorphic properties for business
logic: I might not be able to state the total, but I can state that adding an item never decreases it."*

⭐ **Close with the limits, honestly:** *"I keep it to pure functions — parsers, money, data
structures — partly because that's where the properties are and partly because the per-example deadline
makes I/O-touching property tests flaky on a loaded CI runner. And I keep the example tests. A property
says a law holds; an example says a hundred-pound order for a pro customer costs ninety. A reader needs
both."*

---

**Previous:** [Day 098](Day-098.md) · **Tomorrow:** Day 099 *(not yet written — see the
[Days index](README.md))* — **Ruff, mypy and pre-commit**: ⭐⭐ the automated reviewer, so humans
review design
