# DSA Parallel Track — in Python

**45–75 minutes every day, alongside the lesson.** This runs for the whole roadmap, not for a phase.

> **⭐⭐ The goal is not "solve 500 problems". It is to build a pattern index — a mapping from
> *something in the wording* to *a technique* — and to know which mistake you personally make in each
> family.** Five hundred problems solved and forgotten is worth less than eighty with a journal.

---

## The one artefact that matters: the pattern journal

**One line per problem. Three columns. Nothing else.**

| ⭐ Column | ⭐ What goes in it |
|---|---|
| ⭐ **The tell** | what in the *wording* pointed at the technique — "contiguous subarray", "shortest path", "k largest" |
| ⭐ **The technique** | one sentence |
| ⭐⭐ **My bug** | ⭐⭐ **the specific mistake YOU made this time, in your words** |

⭐⭐ **The third column is the only part that is about you**, and re-reading sixty of those lines the
week before a loop is worth more than sixty new problems. Day 476 makes this explicit.

---

## Python-specific: what changes versus Java or C++

| | ⭐ Note |
|---|---|
| ⭐⭐ **You write it faster** | ⭐ genuinely an advantage in a 45-minute round |
| ⭐⭐ **It runs 10–100× slower** | ⭐⭐ a correct O(n log n) can still time out where C++ would not — know your **constant factors**, not just your complexity |
| ⭐⭐ **`list` is not an array** | ⭐⭐ it is an array of pointers; `list.pop(0)` is **O(n)** — use `collections.deque` |
| ⭐⭐ **Recursion limit is 1000** | ⭐⭐ `sys.setrecursionlimit`, or convert to iteration — a deep DFS on 10⁵ nodes **will** blow the stack |
| ⭐ **String concatenation in a loop is O(n²)** | ⭐ build a list and `"".join(...)` |
| ⭐ **`heapq` is a MIN-heap only** | ⭐⭐ for a max-heap, push negated values — say this out loud in the round |
| ⭐ **Integers are arbitrary precision** | ⭐ no overflow — which removes a whole class of problem, and you should mention that you noticed |
| ⭐ **Tuples are hashable, lists are not** | ⭐ so a visited set holds tuples |
| ⭐ `functools.lru_cache` | ⭐⭐ turns a recursive brute force into memoised DP **in one line** — and say what it is doing |

```python
# ⭐ The eight imports that cover almost every interview problem.
from collections import deque, defaultdict, Counter
from functools import lru_cache, cmp_to_key
import heapq, bisect, math, itertools
```

⚠️ ⭐⭐ **Practise in a plain editor with no autocomplete.** Most interview platforms have none, and
discovering that you cannot remember `heapq.heappushpop` under a clock costs minutes you needed for
testing (Day 472).

---

## The pattern families, in the order to learn them

| # | Family | ⭐ The tell |
|---|---|---|
| 1 | **Hashing** | "have I seen this", "count of", "pair summing to" |
| 2 | **Two pointers** | ⭐ a **sorted** array, or "in place, O(1) space" |
| 3 | **Sliding window** | ⭐⭐ "contiguous subarray/substring" + longest/shortest/at most k |
| 4 | **Prefix sums** | ⭐ "sum of a range", "subarrays summing to k" |
| 5 | **Binary search** | sorted, or ⭐⭐ **"minimise the maximum"** — search on the answer |
| 6 | **Stack** | ⭐ nesting, matching, "next greater element" |
| 7 | **Linked list** | in-place reversal, cycle detection, fast/slow pointers |
| 8 | **Trees** | ⭐ traversal choice; ⭐⭐ in-order on a BST gives sorted order |
| 9 | **Graphs — BFS/DFS** | ⭐⭐ **"shortest" + unweighted ⇒ BFS, always** |
| 10 | **Topological sort** | dependencies, ordering, "is it possible" |
| 11 | **Heaps** | ⭐⭐ "k largest" ⇒ a **min**-heap of size k |
| 12 | **Intervals** | ⭐ merge ⇒ sort by **start**; max non-overlapping ⇒ sort by **end** |
| 13 | **Greedy** | ⭐ prove it, or find the counterexample |
| 14 | **Dynamic programming** | ⭐⭐ choices that interact; write the **state sentence** first |
| 15 | **Backtracking** | permutations, combinations, "all possible" |
| 16 | **Union-find** | connectivity, grouping, cycle detection in undirected graphs |
| 17 | **Tries** | prefix queries, autocomplete |
| 18 | **Bit manipulation** | ⭐ subsets, XOR tricks, "single number" |

---

## The weekly rhythm

| Day | What |
|---|---|
| Mon–Fri | ⭐ 2–3 problems in one family. **Journal every one.** |
| Sat | ⭐⭐ **Re-solve two you got wrong**, from scratch, no notes |
| Sun | ⭐⭐ **One timed 45-minute mock, narrated aloud and recorded** (Day 472's protocol) |

⭐⭐ **The Saturday re-solve is the highest-value hour of the week.** A problem you got wrong and then
read the solution to is not learned; a problem you got wrong and then solved unaided a week later is.

---

## The rules

1. ⭐⭐ **Narrate out loud, always** — even alone. Silent practice trains a skill you cannot use in a
   round (Day 466).
2. ⭐⭐ **State the approach and its complexity before writing any code.** Every time. It is the habit
   that most often decides a real round.
3. ⭐ **Twenty-five minutes stuck ⇒ read only the *hint*, not the solution.** Then close it and solve.
4. ⭐⭐ **If you read a full solution, re-solve from scratch within 48 hours** or it did not happen.
5. ⭐ **Test your own code by hand before running it.** The habit is worth more than the bugs it finds.
6. ⭐ Volume is not the metric. **The journal is the metric.**
