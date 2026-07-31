# ⚡ Quick Note · Day 9 — DP · Q6 · Min Subset Sum Diff
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Split into two subsets minimizing |sum difference|."

## Pattern
`Subset-sum reachability DP → closest to total/2`

## Visual in your head
```
reachable sums 0..total/2
one subset s → other total-s → diff = total-2s
answer = total - 2*(largest reachable s)
```

## The trick (say it out loud)
> "Get one subset as close to total/2 as possible. diff = total − 2s. Boolean knapsack, downward loop."

## Code skeleton
```python
for x in nums:
    for s in range(half, x-1, -1):
        if reachable[s-x]: reachable[s]=True
# largest reachable s → total-2s
```

## Complexity
- Time O(n·total) · Space O(total)

## This trick solves more
Partition Equal (416) · Last Stone II (1049) · Target Sum (494) · 0/1 Knapsack · Balanced partition
