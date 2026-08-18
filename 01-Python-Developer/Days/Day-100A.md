# Day 100A

**L-100A** · ➕ **Profiling** — `cProfile`, `line_profiler`, `memray`, and ⭐⭐ the optimisation ladder

**Time:** 2–3 hrs · **Mode:** NEW · ➕ **an addition to the roadmap**

> ⭐⭐ **The sentence to own: you are wrong about where the time goes — everyone is, consistently and
> in the same direction — so the first act of any optimisation is a measurement, and the second is
> checking whether the thing you measured is even worth speeding up.**
>
> ⭐ Day 073 told you *why* Python is slow. **Today is finding out where your program actually is
> slow**, which is almost never where it feels slow.

---

# Part 1 — ⭐⭐ Before you profile: is it worth it?

```
⭐⭐ AMDAHL'S LAW, IN THE ONLY FORM YOU NEED IT: THE MAXIMUM SPEEDUP IS CAPPED BY
⭐   THE FRACTION YOU ARE OPTIMISING.
⭐   ⇒ a function that is 5 % of runtime, made INFINITELY FAST, gives you 5 %.
⭐   ⇒ ⭐⭐ SO THE FIRST QUESTION IS NEVER "HOW DO I MAKE THIS FASTER" BUT "WHAT
⭐        FRACTION OF THE TIME IS THIS?" ⭐ Two hours saving 4 % is two hours lost.
⭐
⭐ ⭐⭐ AND THE SECOND QUESTION, WHICH IS THE ONE ENGINEERS SKIP: WHAT IS THE
⭐   TARGET? "Faster" is not a goal. ⭐ "p95 under 200 ms" is a goal, it tells
⭐   you when to STOP, and it stops you optimising something already fast enough.
```

```python
# ⭐ TIMING, DONE CORRECTLY (Day 076's clocks):
from time import perf_counter
start = perf_counter(); do_work(); elapsed = perf_counter() - start   # ⭐ never time.time()

import timeit
timeit.repeat("f(data)", globals=globals(), number=100, repeat=5)     # ⭐⭐ repeat, take the MIN
```

```
⭐⭐ WHY THE MINIMUM AND NOT THE MEAN, FOR A MICROBENCHMARK: noise is one-sided.
⭐   Another process, a GC pause (Day 027) or a cache miss can only make a run
⭐   SLOWER, never faster. ⇒ ⭐ the minimum is the closest thing to "the work
⭐   itself". ⚠️ ⭐⭐ THE OPPOSITE HOLDS FOR A SERVICE: there you care about p95
⭐   and p99, because the tail is what users experience (Day 080).
```

⚠️ ⭐ **Benchmarks that lie, and all four are common:** timing a constant the interpreter folds away;
measuring the *second* run of something now cached; using 100 rows when production has 10 million (the
algorithm's shape only shows up at scale); and benchmarking on a laptop with a different core count
and thermal behaviour from the server.

---

# Part 2 — ⭐⭐ `cProfile` — which function

```bash
python -m cProfile -s cumtime -o prof.out app.py
python -c "import pstats; pstats.Stats('prof.out').sort_stats('tottime').print_stats(20)"
uvx snakeviz prof.out          # ⭐ an interactive flame view in the browser
```

```
   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
     1000    0.021    0.000    8.412    0.008 orders.py:44(place_order)
    24000    0.180    0.000    7.900    0.000 repo.py:12(get_customer)   ⭐⭐ 24,000 calls?
    24000    7.640    0.000    7.640    0.000 {method 'execute' of 'Cursor'}
```

```
⭐⭐ tottime vs cumtime — THE DISTINCTION THE WHOLE REPORT TURNS ON:
⭐   tottime = time in THIS function's own code, EXCLUDING what it called
⭐   cumtime = time in this function AND everything beneath it
⭐   ⇒ ⭐⭐ SORT BY cumtime TO FIND *WHERE* — which subtree owns the time.
⭐   ⇒ ⭐⭐ SORT BY tottime TO FIND *WHAT* — the function actually burning it.
⭐   ⇒ ⭐ `main` always has the highest cumtime and it means nothing.
⭐
⭐ ⭐⭐ AND READ `ncalls` BEFORE EITHER OF THEM. In the output above, 1,000
⭐   orders caused 24,000 customer lookups. ⭐⭐ THAT IS AN N+1 QUERY (Stage 4),
⭐   and no amount of making `get_customer` faster fixes it — ⭐ the answer is to
⭐   call it 1,000 times fewer, which is Part 4's first rung.
⭐   ⇒ ⭐⭐ A SURPRISING CALL COUNT IS THE MOST ACTIONABLE THING IN A PROFILE.
```

⚠️ ⭐ **`cProfile` adds per-call overhead**, so it *over*-reports functions called millions of times
and distorts the picture for tiny hot functions. ⭐⭐ **It is excellent for finding structure (call
counts, which subtree) and unreliable for micro-comparisons** — use `timeit` for those and `py-spy` for
anything running in production.

---

# Part 3 — ⭐ `line_profiler`, `py-spy`, and memory

```python
# ⭐ line_profiler — when cProfile says "this function" and you need "which line"
@profile                                   # ⭐ injected by kernprof; no import
def score(rows): ...
# kernprof -l -v script.py

# ⭐  Line #   Hits    Time  Per Hit  % Time  Line Contents
# ⭐      12   1000   842.0     0.8    1.2    total = 0
# ⭐      13   1000 68300.0    68.3   94.1    key = json.dumps(row, sort_keys=True)  ⭐⭐ ← there
```

```bash
py-spy top --pid 4242                       # ⭐⭐ PRODUCTION-SAFE (Day 082) — no code change
py-spy record -o flame.svg --pid 4242 --duration 60
py-spy record -o flame.svg -- python app.py --idle    # ⭐ --idle shows time spent WAITING
```

⭐⭐ **`py-spy` is the one to reach for first in a real service**, because it samples from outside the
process: no import, no restart, no measurable overhead, and **what you measure is what is actually
happening** rather than what happens under a profiler.

```python
# ⭐⭐ MEMORY — tracemalloc is in the stdlib and needs nothing installed:
import tracemalloc
tracemalloc.start()
snap1 = tracemalloc.take_snapshot()
do_the_suspect_thing()
snap2 = tracemalloc.take_snapshot()
for stat in snap2.compare_to(snap1, "lineno")[:10]:    # ⭐⭐ THE DIFF is the tool
    print(stat)                                         # ⭐ "+42 MB at repo.py:88"
```

```bash
memray run -o out.bin app.py && memray flamegraph out.bin   # ⭐⭐ every allocation, with a tree
memray run --live app.py                                     # ⭐ watch it climb in real time
```

```
⭐⭐ PEAK vs LEAK — TWO DIFFERENT PROBLEMS AND TWO DIFFERENT TOOLS:
⭐   PEAK ⇒ one request loads a 2 GB file into a list ⇒ ⭐ memray's flamegraph
⭐          names the allocation site. ⭐⭐ Fix: stream it (Day 047's generators).
⭐   LEAK ⇒ RSS climbs over hours and never falls ⇒ ⭐⭐ tracemalloc SNAPSHOT
⭐          DIFFS across time. ⭐ Usual causes: an unbounded cache or dict, an
⭐          @lru_cache on a method (Day 051), a closure holding a big object
⭐          (Day 043), or a growing module-level list.
⭐   ⇒ ⚠️ ⭐ AND THE THIRD THING PEOPLE MISTAKE FOR A LEAK: RSS not falling after
⭐        a spike is often just the allocator not returning pages to the OS.
⭐        ⭐⭐ Flat-after-spike is fine; STEADILY RISING is a leak (Day 082).
```

---

# Part 4 — ⭐⭐ The optimisation ladder

```
⭐⭐ CLIMB IN ORDER. EACH RUNG IS WORTH MORE THAN EVERY RUNG BELOW IT COMBINED,
⭐   AND PEOPLE REACH FOR RUNG 4 FIRST.
⭐
⭐ 1. ⭐⭐ DO LESS WORK — ⭐⭐ ALWAYS THE BIGGEST WIN.
⭐      · a better algorithm: O(n²) → O(n log n) (the DSA track, cashing in)
⭐      · ⭐⭐ FIX THE N+1: 24,000 queries → 1. ⭐ Nothing else comes close.
⭐      · add the index (Stage 5) · select fewer columns · ⭐ cache the result
⭐      · ⭐ do not compute what nobody asked for (pagination, laziness)
⭐
⭐ 2. ⭐ DO IT LESS OFTEN — batch, debounce, cache, precompute, move it to a
⭐      background job (Stage 4's Celery). ⭐⭐ Latency the user sees is not the
⭐      same as work the system does.
⭐
⭐ 3. ⭐ DO IT FASTER PER ITEM — ⭐⭐ this is where most people start and it is
⭐      the third rung: hoist work out of the loop, use a set instead of a list
⭐      for membership (Day 036), `join` instead of `+=` (Day 037), a
⭐      comprehension instead of `append` in a loop (Day 050), the right data
⭐      structure (Day 039).
⭐
⭐ 4. ⭐ LEAVE PYTHON — numpy/polars/pyarrow for arrays and dataframes, or C/Rust
⭐      via an extension. ⭐⭐ A genuine 10-100× for numeric work, and a large
⭐      complexity cost. Only after 1-3.
⭐
⭐ 5. ⭐ PARALLELISE — processes for CPU, asyncio/threads for I/O (Days 065-072).
⭐      ⚠️ ⭐⭐ LAST, because it multiplies a constant factor while adding
⭐      coordination, memory and a whole class of bugs. ⭐ Parallelising an
⭐      O(n²) algorithm is buying four cores to stay quadratic.
```

```
⭐⭐ AND THE HONEST BACKEND TRUTH, WHICH IS DAY 073'S CLOSING POSITION RESTATED:
⭐   IN ALMOST EVERY WEB SERVICE, THE TIME IS NOT IN YOUR PYTHON. It is in a
⭐   query, an N+1, a missing index, or a round trip to another service.
⭐   ⇒ ⭐⭐ SO PROFILE THE SYSTEM BEFORE THE CODE: `curl -w` for the latency
⭐        breakdown (Day 019), the slow-query log, then the p95 by endpoint
⭐        (Day 080). ⭐ Reach for cProfile only once you know the time is
⭐        actually inside your process.
⭐   ⇒ ⭐ and measure AFTER the change too. ⭐⭐ An "optimisation" you did not
⭐        re-measure is a refactor with a story attached.
```

---

## Common mistakes

| Mistake | Correction |
|---|---|
| ⭐⭐ **Optimising without measuring** | ⭐⭐ You are wrong about where the time goes. Everyone is. |
| ⭐ Optimising 5% of runtime | ⭐⭐ Amdahl — the ceiling is 5%. Ask the fraction first. |
| No target | ⭐ "Faster" never tells you when to stop. |
| ⭐ `time.time()` for durations | ⭐ `perf_counter` (Day 076). |
| Taking the mean of a microbenchmark | ⭐⭐ Noise is one-sided. Take the **min** — but p95 for a service. |
| ⭐ Benchmarking on 100 rows | ⭐ The algorithm's shape only appears at scale. |
| ⭐⭐ **Ignoring `ncalls`** | ⭐⭐ A surprising call count is the most actionable line in a profile. |
| Confusing `tottime` and `cumtime` | ⭐ `cumtime` finds *where*, `tottime` finds *what*. |
| ⭐ `cProfile` in production | ⭐⭐ Overhead and distortion. `py-spy`, from outside. |
| Treating flat RSS after a spike as a leak | ⭐ The allocator keeps pages. **Steadily rising** is a leak. |
| ⭐⭐ **Parallelising first** | ⭐⭐ A constant factor with coordination costs. Rung five, not one. |
| Profiling code before the system | ⭐ The time is usually in a query or a round trip. |
| Not re-measuring after the change | ⭐⭐ Then it is a refactor with a story attached. |

---

## Interview questions

**Q: An endpoint is slow. What do you do?**
> Measure before touching anything, and measure the system before the code. `curl -w` gives me the
> latency breakdown — DNS, connect, TLS, time-to-first-byte — and if TTFB dominates, the time is mine.
> Then the slow-query log and p95 by endpoint, because in almost every web service the time is in a
> query, an N+1 or a round trip rather than in Python. Only once I know it's inside the process do I
> reach for a profiler, and in production that's `py-spy`, because it samples from outside without a
> restart or measurable overhead. I'd also want a target before starting — "p95 under 200 ms" tells me
> when to stop; "faster" doesn't.

**Q: What's the difference between `tottime` and `cumtime`?**
> `tottime` is time in the function's own code excluding calls it makes; `cumtime` includes everything
> beneath it. So I sort by `cumtime` to find *where* the time lives — which subtree owns it — and by
> `tottime` to find *what* is actually burning it. `main` always has the highest `cumtime` and it means
> nothing. The column I read first, though, is `ncalls`: seeing 24,000 customer lookups for 1,000
> orders tells me it's an N+1, and no amount of making that function faster fixes it.

**Q: How do you find a memory leak?**
> First establish it *is* one: RSS that's flat after a spike is usually the allocator holding pages
> rather than a leak, whereas steadily rising over hours is real. Then `tracemalloc` with snapshot
> diffs — take one, do the suspect work, take another, and compare — which gives you "plus 42 MB at
> this line". `memray` goes further with a flamegraph of allocation sites, and has a live mode. The
> usual causes in Python are an unbounded cache or dict, `lru_cache` on a method, which keeps every
> instance alive, or a closure holding something large.

**Q: How do you decide what to optimise?**
> A ladder, in order. Do less work — a better algorithm, fixing an N+1, adding an index, not computing
> what nobody asked for. Then do it less often — batch, cache, move it to a background job. Then make
> each item faster, which is where most people start and is actually the third rung. Then leave Python
> for numpy or a compiled extension. And parallelise last, because it buys a constant factor while
> adding coordination, memory and a whole class of concurrency bugs — parallelising a quadratic
> algorithm is buying four cores to stay quadratic.

**Q: Why take the minimum of a microbenchmark rather than the mean?**
> Because noise is one-sided: another process, a GC pause or a cache miss can only make a run slower,
> never faster, so the minimum is the closest thing to the cost of the work itself. That's for
> microbenchmarks. For a service it inverts — there I care about p95 and p99, because the tail is what
> users actually experience, and the mean hides exactly the people who are complaining.

---

## Mini task

1. ⭐⭐ Guess which function in one of your scripts is slowest. **Write the guess down.** Then profile
   it. Keep the note.
2. ⭐ Profile with `cProfile`, sort by `cumtime` and then `tottime`. **Say what each told you.**
3. ⭐⭐ Find something in a profile where `ncalls` is surprising. **Explain the number.**
4. ⭐ Open the same profile in `snakeviz`.
5. ⭐⭐ Use `line_profiler` on the hottest function and find the single hot **line.**
6. ⭐⭐ Run `py-spy record` against a running server under load and read the flamegraph. Compare with
   `--idle`.
7. ⭐⭐ Write a loop that grows a module-level list. **Find it with `tracemalloc` snapshot diffs.**
8. ⭐ Run `memray run --live` on something that loads a large file, and watch the peak.
9. ⭐⭐ Convert that to a generator (Day 047) and **compare peak memory.**
10. ⭐⭐ Take an N+1 in your own code. **Count the queries, fix it, count again**, and record both
    numbers.
11. ⭐ `timeit` a microbenchmark; report mean and min over 5 repeats and explain the difference.
12. ⭐⭐ Benchmark the same function on 100 rows and 1,000,000 rows. **Note where the ranking changes.**

---

# 🚪 Exit questions

Answer aloud, no notes.

1. State Amdahl's law in the form that matters, and the first question it implies.
2. Why do you need a target?
3. Min or mean — and when does that invert?
4. Name four ways a benchmark lies.
5. `tottime` vs `cumtime` — and which finds what?
6. Why read `ncalls` first?
7. When is `cProfile` unreliable, and what do you use instead?
8. Why is `py-spy` the production tool?
9. Peak vs leak — different tools, different symptoms.
10. What is mistaken for a leak, and how do you tell?
11. Give the five rungs of the ladder, in order.
12. Why is parallelism last?
13. Where is the time in a typical web service?

## 🎙️ Articulation drill

Record two minutes: **"A page takes four seconds to load. Make it fast."**

⭐ **Refuse to start guessing, and say why in one sentence:** **"the first thing I'd do is find out
where the four seconds are, because everyone is wrong about that and consistently in the same
direction. And I'd fix a target first — p95 under 500 ms, say — because 'faster' never tells you when
to stop."**

⭐⭐ **Then measure the system before the code, which is the part that separates a backend engineer
from someone who reaches for a profiler:** *"`curl -w` gives me the breakdown — DNS, connect, TLS,
time to first byte. If TTFB is 3.8 of the 4 seconds, the time is mine and I keep going. If it's the
transfer, it's payload size and that's a completely different fix. Then the slow-query log, because in
almost every web service the time is in a query, an N+1 or a round trip rather than in Python."*

⭐⭐ **Then the profile, and what you read in it:** *"if it really is in the process, `py-spy` against
the running server, because it samples from outside with no restart and no meaningful overhead. And the
first column I read is call counts, not time — twenty-four thousand customer lookups for a thousand
orders is an N+1, and making that function faster fixes nothing. The answer is to call it a thousand
times fewer."*

⭐ **Close on the ladder and the discipline:** *"then I'd work down in order: do less work, do it less
often, then make each item faster, and only then reach for numpy or parallelism. Parallelising is last
because it buys a constant factor while adding coordination and a class of concurrency bugs — four
cores on a quadratic algorithm is still quadratic. And I'd measure again afterwards, because an
optimisation you didn't re-measure is just a refactor with a story attached."*

---

**Previous:** [Day 100](Day-100.md) · **Tomorrow:** [Day 100B](Day-100B.md) — ➕ **code review, both
sides**: what to look for, and ⭐⭐ how to say it
