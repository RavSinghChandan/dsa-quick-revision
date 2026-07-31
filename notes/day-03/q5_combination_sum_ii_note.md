# ⚡ Quick Note · Day 3 — SW & Backtracking · Q5 · Combination Sum II
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "All unique combos summing to target; each number used once (dups in input)."

## Pattern
`Backtracking DFS + sort + skip duplicate siblings`

## Visual in your head
```
sort → dfs(start, remaining, path)
skip: i>start and c[i]==c[i-1] → continue
prune: c[i]>remaining → break
recurse from i+1 (used once)
```

## The trick (say it out loud)
> "Sort. DFS from i+1. Skip equal siblings (i>start && c[i]==c[i-1]). Break when candidate > remaining."

## Code skeleton
```python
for i in range(start,n):
    if i>start and c[i]==c[i-1]: continue
    if c[i]>rem: break
    path.append(c[i]); dfs(i+1,rem-c[i],path); path.pop()
```

## Complexity
- Time O(2^n) worst (pruned) · Space O(n)

## This trick solves more
Combination Sum I (39) · Subsets II (90) · Permutations II (47) · Palindrome Partition (131) · N-Queen
