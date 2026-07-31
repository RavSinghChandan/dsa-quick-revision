# Day 9 — Dynamic Programming · Q6 · Minimum Subset Sum Difference

---

## What is this question actually asking?

Split the array into **two subsets** so that the **absolute difference of their sums is minimized**. Return that difference.

```
[1,6,11,5] → 1   ({1,5,6}=12, {11}=11 → diff 1)
[1,2,7]    → 4   ({1,2}=3, {7}=7 → diff 4)
```

> A **subset-sum** DP: find which sums ≤ total/2 are achievable; the closest one to total/2 minimizes the difference.

---

## Pattern

```
PATTERN: Subset-Sum reachability DP (boolean knapsack) → closest to total/2
```

Whenever you see: *"partition into two groups minimizing difference / closest to half"* → compute all achievable subset sums up to total/2. If one subset sums to `s`, the other is `total − s`, and diff = `total − 2s`. Minimize over reachable `s`.

---

## Understand with a diagram

```
total = sum(nums)
reachable[s] = can some subset sum to s?  (0..total/2)

for each best reachable s (largest ≤ total/2):
    diff = total - 2*s     ← other subset = total - s
    answer = min diff

[1,6,11,5] total=23, half=11
reachable sums include 12? (1+6+5) → s up to 11 or 12; best s=12 → diff=23-24? no.
best s <= 11 that's reachable maximizing s → 11 (just {11}) → diff=23-22=1
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all 2^n subsets — exponential
Enumerate every partition.

### 🟢 Optimal — subset-sum DP — O(n · total)
Boolean DP over sums up to total/2; pick the reachable sum closest to total/2.

---

## Pseudocode (Optimal)

```
function min_diff(nums):
    total = sum(nums)
    reachable = boolean array [0..total//2], reachable[0] = True
    for x in nums:
        for s from total//2 down to x:        ← 0/1: downward
            reachable[s] |= reachable[s-x]
    for s from total//2 down to 0:
        if reachable[s]:
            return total - 2*s                ← first (largest) reachable
```

---

## Python Code

```python
def min_subset_sum_diff(nums: list[int]) -> int:
    total = sum(nums)
    half = total // 2
    reachable = [False] * (half + 1)
    reachable[0] = True
    for x in nums:
        for s in range(half, x - 1, -1):       # 0/1 knapsack style, downward
            if reachable[s - x]:
                reachable[s] = True
    for s in range(half, -1, -1):              # largest reachable ≤ half
        if reachable[s]:
            return total - 2 * s


# Test
print(min_subset_sum_diff([1,6,11,5]))   # 1
print(min_subset_sum_diff([1,2,7]))      # 4
print(min_subset_sum_diff([3,1,4,2,2]))  # 0  (perfectly split into 6/6)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Subset-sum DP | O(n · total) | O(total) |

---

## Edge Cases to remember

- **Only compute sums up to total/2** — the other half is symmetric; the closest to total/2 gives the minimum diff.
- **Downward inner loop** — 0/1 knapsack (each element used once).
- **`diff = total − 2s`** — because one subset is `s`, the other is `total − s`.
- **Perfect split** → answer 0 (when total/2 is reachable).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Partition Equal Subset Sum (LC 416) | reachable[total/2]? |
| 2 | Last Stone Weight II (LC 1049) | This exact idea |
| 3 | Target Sum (LC 494) | Signed subset sum |
| 4 | 0/1 Knapsack (Q5) | Same DP family |
| 5 | Count subsets with given sum | Count version |
| 6 | Minimum difference partition | This |
| 7 | Balanced partition | Subset sum |
| 8 | Tug of war | Subset sum near half |

---

## The ONE trick to remember

```
"REACHABLE SUBSET SUMS ≤ total/2; ANSWER = total − 2 × (LARGEST REACHABLE)."
```

One subset's sum `s` determines the other (`total − s`), so the difference is `total − 2s`. Compute which sums are achievable up to total/2 (boolean knapsack), then take the largest reachable `s`.

> Memory hook: "Get one pile as close to half as possible — the gap is your answer."

---
