# ⚡ Quick Note · Day 9 — DP · Q5 · 0/1 Knapsack
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Pick items (each once) under capacity W to maximize value."

## Pattern
`0/1 DP — include vs exclude each item`

## Visual in your head
```
dp[w] = best value at capacity w
each item: for w from W down to weight[i]:
  dp[w]=max(dp[w], value[i]+dp[w-weight[i]])
```

## The trick (say it out loud)
> "Include or exclude. 1-D loop capacity DOWNWARD for 0/1 (upward = unbounded)."

## Code skeleton
```python
for i in range(n):
    for w in range(W, weights[i]-1, -1):
        dp[w]=max(dp[w], values[i]+dp[w-weights[i]])
```

## Complexity
- Time O(n·W) · Space O(W)

## This trick solves more
Subset Sum / Partition (416) · Target Sum (494) · Coin Change (322, up) · Last Stone II (1049) · Ones&Zeroes (474)
