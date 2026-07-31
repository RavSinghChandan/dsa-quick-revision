# Day 9 — Dynamic Programming · Q5 · 0/1 Knapsack

---

## What is this question actually asking?

Given items with `weight[i]` and `value[i]` and a bag capacity `W`, pick a subset (each item **once**) to **maximize value** without exceeding `W`.

```
weights=[1,3,4,5], values=[1,4,5,7], W=7 → 9  (items 3+4: w=3+4=7, v=4+5=9)
```

> THE foundational subset-choice DP. "Include or exclude each item" — the parent of subset-sum, partition, coin change, and more.

---

## Pattern

```
PATTERN: 0/1 DP — for each item, choose include vs exclude
```

Whenever you see: *"pick a subset under a capacity/limit to optimize a total"* → `dp[w]` = best value with capacity `w`. Process items one by one; for each, either skip it or take it (`value + dp[w - weight]`).

---

## Understand with a diagram

```
2-D: dp[i][w] = best using first i items with capacity w
    dp[i][w] = max( dp[i-1][w],                        ← exclude item i
                    value[i] + dp[i-1][w-weight[i]] )  ← include (if fits)

1-D (space optimized): iterate capacity DOWNWARD so each item used once:
    for w from W down to weight[i]:
        dp[w] = max(dp[w], value[i] + dp[w-weight[i]])
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all subsets — O(2^n)
Enumerate every subset, check capacity.

### 🟢 Optimal — DP — O(n·W)
2-D table, or 1-D with a **reverse** capacity loop (so each item is counted once).

---

## Pseudocode (1-D Optimal)

```
function knapsack(weights, values, W):
    dp = [0]*(W+1)
    for i in 0..n-1:
        for w from W down to weights[i]:      ← DOWNWARD → 0/1 (each item once)
            dp[w] = max(dp[w], values[i] + dp[w - weights[i]])
    return dp[W]
```

---

## Python Code

```python
def knapsack_01(weights: list[int], values: list[int], W: int) -> int:
    dp = [0] * (W + 1)
    for i in range(len(weights)):
        for w in range(W, weights[i] - 1, -1):    # DOWNWARD for 0/1
            dp[w] = max(dp[w], values[i] + dp[w - weights[i]])
    return dp[W]


# Test
print(knapsack_01([1,3,4,5], [1,4,5,7], 7))   # 9
print(knapsack_01([2,3,4], [3,4,5], 5))       # 7  (weights 2+3=5 → values 3+4=7)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(2^n) | O(n) |
| DP | O(n·W) | O(W) 1-D |

---

## Edge Cases to remember

- **Iterate capacity DOWNWARD in the 1-D version** — an upward loop would allow reusing an item (that's *unbounded* knapsack). This is THE key distinction.
- **Unbounded knapsack** (item reusable) → loop capacity **upward**.
- **W = 0 or no items** → 0.
- **Pseudo-polynomial** — O(n·W) depends on W's magnitude, not just n.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Subset Sum / Partition Equal (LC 416) | Boolean knapsack |
| 2 | Min Subset Sum Difference (Q6) | Subset-sum reachability |
| 3 | Target Sum (LC 494) | Count subsets |
| 4 | Coin Change (LC 322) | Unbounded (loop up) |
| 5 | Coin Change II (LC 518) | Count unbounded |
| 6 | Last Stone Weight II (LC 1049) | Partition |
| 7 | Ones and Zeroes (LC 474) | 2-D knapsack |
| 8 | Rod cutting | Unbounded |

---

## The ONE trick to remember

```
"INCLUDE vs EXCLUDE EACH ITEM; 1-D LOOP CAPACITY DOWNWARD FOR 0/1."
```

Each item is either taken or not: `dp[w] = max(dp[w], value + dp[w - weight])`. In the 1-D optimization, iterate capacity from high to low so an item isn't reused — the single detail separating 0/1 from unbounded knapsack.

> Memory hook: "Take it or leave it — and count capacity backward so you can't take it twice."

---
