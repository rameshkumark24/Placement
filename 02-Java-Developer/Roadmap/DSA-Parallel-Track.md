# DSA Parallel Track

Runs **every day** alongside the lesson — 45–75 minutes. It is not part of the day count because it
never stops.

Your roadmap defers this to "your own roadmap" without specifying it. That's a hole: DSA is the round
that gates every other round. This is the pattern set to check your track against, not a replacement
for it.

---

## The pattern journal — the part that actually works

For every problem you solve, write **one line**:

```
LC 1004 Max Consecutive Ones III | Sliding window (variable)
Signal: "longest subarray such that <condition>" + condition is monotonic when shrinking
```

**The signal is the whole point.** Anyone can learn 20 patterns. Interviews are won by mapping an
unseen problem to a pattern in the first 60 seconds, and that mapping is a skill you build only by
writing down *what in the problem statement told you*.

Review the journal every Sunday. After ~100 entries you'll notice you're recognising signals before
you finish reading the problem.

---

## Patterns, in the order worth learning them

### Tier 1 — cover ~60% of interview problems

| Pattern | Signal in the problem |
|---|---|
| **Two pointers** | Sorted array · pair/triplet summing to target · in-place partition |
| **Sliding window (fixed)** | "subarray of size k" |
| **Sliding window (variable)** | "longest/shortest subarray such that…" with a monotonic condition |
| **Hash map counting** | "frequency", "anagram", "seen before", O(n) lookup needed |
| **Prefix sum** | Repeated range-sum queries · subarray sums · "sum equals k" |
| **Binary search on array** | Sorted · O(log n) required |
| **Binary search on answer** | "minimum/maximum value such that a check passes" · answer is monotonic |
| **Sorting as a first step** | Intervals · "group similar" · pairing largest with smallest |

### Tier 2 — the structural ones

| Pattern | Signal |
|---|---|
| **Linked list manipulation** | Reverse, cycle detection (Floyd), merge, find middle |
| **Stack** | Matching pairs · **monotonic stack** for "next greater/smaller element" |
| **Heap / priority queue** | "top k", "k largest", "median of a stream", merge k lists |
| **Tree DFS** | Path problems, subtree properties, "return info from children" |
| **Tree BFS** | Level-order, "shortest path in an unweighted tree", level aggregates |
| **BST properties** | In-order traversal is sorted · search/insert/delete in O(h) |
| **Graph BFS** | Shortest path, unweighted · flood fill · level-by-level spread |
| **Graph DFS** | Connected components, cycle detection, path existence |
| **Topological sort** | "order with prerequisites" · DAG · course scheduling |
| **Union-Find** | Connectivity queries · "number of groups" · Kruskal's |

### Tier 3 — the ones people avoid and then fail on

| Pattern | Signal |
|---|---|
| **Backtracking** | "all permutations/combinations/subsets" · constraint satisfaction · N-Queens |
| **DP 1D** | "number of ways" · "min/max cost to reach" · Fibonacci-shaped recurrence |
| **DP 2D** | Two sequences (LCS, edit distance) · grid paths · knapsack |
| **DP on subsequences** | LIS · subset sum · partition problems |
| **Greedy** | Local optimum provably gives global · interval scheduling · **must be able to argue why greedy is correct** |
| **Bit manipulation** | XOR tricks · subsets via bitmask · "single number" |
| **Matrix traversal** | Spiral, rotate in place, islands |
| **Trie** | Prefix search, autocomplete, word dictionaries |
| **Intervals** | Merge, insert, overlap detection — always sort by start first |

---

## Complexity — say it before you code

State the target complexity out loud **before** writing code. If the interviewer wants better,
you've saved twenty minutes.

| Constraint | Expected complexity |
|---|---|
| n ≤ 10 | Permutations, O(n!) |
| n ≤ 20 | Bitmask subsets, O(2ⁿ) |
| n ≤ 500 | O(n³) |
| n ≤ 5,000 | O(n²) |
| n ≤ 10⁶ | O(n log n) |
| n ≤ 10⁸ | O(n) |
| Huge / streaming | O(log n) or O(1) |

Reading the constraint tells you the pattern family before you've thought about the problem.

---

## Java specifics for DSA

Things that lose people time in a live round:

```java
// Arrays vs collections
int[] a = new int[n];                    Arrays.sort(a);        // primitives, no boxing
Integer[] b = new Integer[n];            Arrays.sort(b, cmp);   // custom comparator needs objects

// The workhorses
Map<Character,Integer> freq = new HashMap<>();
freq.merge(c, 1, Integer::sum);                    // cleaner than getOrDefault + put
freq.computeIfAbsent(k, x -> new ArrayList<>()).add(v);

Deque<Integer> stack = new ArrayDeque<>();         // use this, NOT Stack (legacy, synchronized)
Deque<Integer> queue = new ArrayDeque<>();         // offer/poll — also a queue

PriorityQueue<int[]> pq =
    new PriorityQueue<>((x, y) -> x[1] - y[1]);    // min-heap by second element
                                                   // careful: subtraction overflows.
PriorityQueue<int[]> safe =
    new PriorityQueue<>(Comparator.comparingInt(x -> x[1]));   // prefer this

// String building — never += in a loop (Day 029)
StringBuilder sb = new StringBuilder();
sb.append(c);
sb.reverse();
sb.toString();

// Char arithmetic
int idx = c - 'a';                       // 0..25
char back = (char)('a' + idx);
```

**Traps that cost interviews:**

- `int` overflow in `(lo + hi) / 2` → use `lo + (hi - lo) / 2`
- Comparator by subtraction overflows → use `Comparator.comparingInt`
- `Integer` caching means `==` works to 127 and silently breaks after (Day 028)
- `Arrays.asList()` returns a fixed-size view — `add` throws
- `HashMap` iteration order is not insertion order — `LinkedHashMap` if you need it

---

## Cadence

| | |
|---|---|
| **Daily** | 2–3 problems, 45–75 min, journal every one |
| **Weekly** | Review the journal; re-solve one problem you failed, from scratch |
| **Monthly** | One timed 45-min mock, narrated aloud |

**Narrate aloud from day one.** Silent solving trains a skill that doesn't transfer — the interview
scores your explanation, not just your solution. This is the same principle as the daily
articulation drill.

---

## Where it plugs into the roadmap

| Stage | DSA focus |
|---|---|
| 0 (Days 1–22) | Arrays, strings, two pointers, hash maps — Tier 1 |
| 1 (Days 23–77) | Everything Tier 1 + linked lists, stacks, heaps. Your collections lessons (045–053) make the data structures real. |
| 2–3 (Days 78–129) | Trees, graphs, Tier 2 complete |
| 4–5 (Days 130–213) | DP and backtracking — Tier 3 |
| 8–9 (Days 298–359) | Mixed timed practice; DSA becomes maintenance, LLD/HLD take priority |
| 15 (Days 472–476) | Five timed mocks, scored |
