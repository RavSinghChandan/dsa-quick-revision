# Day 3 — Sliding Window & Backtracking · Q5 · Combination Sum II

---

## What is this question actually asking?

Given `candidates` (may have duplicates) and a `target`, find **all unique combinations** that sum to target. **Each number used at most once.**

```
candidates = [10,1,2,7,6,1,5], target = 8
→ [[1,1,6],[1,2,5],[1,7],[2,6]]
```

> Classic **backtracking** with the twist of **skipping duplicates** so combinations aren't repeated. Each element used once (unlike Combination Sum I).

---

## Pattern

```
PATTERN: Backtracking (DFS choose/skip) + sort + duplicate-skip
```

Whenever you see: *"all unique combinations/subsets summing to target, each used once"* → **sort**, DFS from an index, subtract as you pick, and **skip equal siblings at the same depth** to avoid duplicate combos.

---

## Understand with a diagram

```
sort: [1,1,2,5,6,7,10]   target 8

pick index by index; at each level, choose candidates[i] then recurse from i+1.
Skip duplicates: if i > start and c[i]==c[i-1] → continue (same value already tried at this level)

1 → 1 → 6 ✓        (indices 0,1,4)
1 →     7 ✓
1 → 2 → 5 ✓
2 →     6 ✓
(the SECOND leading 1 at the top level is skipped to avoid dup)
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all subsets — O(2^n) then filter
Generate every subset, keep those summing to target, dedup. Wasteful.

### 🟢 Optimal — backtracking with pruning — O(2^n) worst but pruned
Sort → DFS from an index, prune when the running sum exceeds target (sorted → `break`), skip duplicate siblings. Each element used once by recursing from `i+1`.

---

## Pseudocode (Optimal)

```
function combinationSum2(candidates, target):
    sort candidates
    result = []
    dfs(start=0, remaining=target, path=[])
    return result

function dfs(start, remaining, path):
    if remaining == 0: add copy of path; return
    for i in start..n-1:
        if i > start and candidates[i] == candidates[i-1]: continue   ← skip dup
        if candidates[i] > remaining: break                          ← prune (sorted)
        path.push(candidates[i])
        dfs(i+1, remaining - candidates[i], path)   ← i+1: each used once
        path.pop()
```

---

## Python Code

```python
def combination_sum2(candidates: list[int], target: int) -> list[list[int]]:
    candidates.sort()
    result = []

    def dfs(start: int, remaining: int, path: list[int]):
        if remaining == 0:
            result.append(path[:])          # found a valid combo
            return
        for i in range(start, len(candidates)):
            if i > start and candidates[i] == candidates[i - 1]:
                continue                     # skip duplicate at this level
            if candidates[i] > remaining:
                break                        # sorted → no further candidate fits
            path.append(candidates[i])
            dfs(i + 1, remaining - candidates[i], path)   # i+1: use once
            path.pop()

    dfs(0, target, [])
    return result


# Test
print(combination_sum2([10,1,2,7,6,1,5], 8))
# [[1, 1, 6], [1, 2, 5], [1, 7], [2, 6]]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Backtracking | O(2^n) worst (pruned in practice) | O(n) recursion + output |

---

## Edge Cases to remember

- **Sort first** — required for both duplicate-skipping and the `break` prune.
- **`i > start` (not `i > 0`)** — skip only duplicates *at the same depth*; picking the same value deeper is allowed (e.g. `[1,1,6]`).
- **Recurse from `i+1`** — each element used at most once (Combination Sum I uses `i` to allow reuse).
- **`path[:]` copy** — append a copy, or all results share one mutating list.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Combination Sum I (LC 39) | Reuse allowed → recurse from `i` |
| 2 | Subsets II (LC 90) | Same dup-skip |
| 3 | Permutations II (LC 47) | Dup-skip with used[] |
| 4 | Palindrome Partitioning (LC 131) | DFS partitions |
| 5 | Letter combinations of phone | DFS choices |
| 6 | Combination Sum III (LC 216) | Fixed count k |
| 7 | Word Break II | DFS + memo |
| 8 | N-Queen (Q6) | Backtracking |

---

## The ONE trick to remember

```
"SORT, DFS FROM i+1, SKIP EQUAL SIBLINGS (i>start && c[i]==c[i-1])."
```

Sorting groups duplicates so you can skip repeated values at the same recursion depth. Recurse from `i+1` so each element is used once. Prune with `break` once a candidate exceeds the remaining target.

> Memory hook: "Same value, same level → already tried it; move on."

---
