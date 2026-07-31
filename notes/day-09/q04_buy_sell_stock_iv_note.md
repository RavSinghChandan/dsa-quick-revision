# ⚡ Quick Note · Day 9 — DP · Q4 · Buy/Sell Stock IV
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Max profit with at most K transactions."

## Pattern
`DP over (transaction count, holding state)`

## Visual in your head
```
for p in prices, for t=1..K:
  buy[t]=max(buy[t], sell[t-1]-p)
  sell[t]=max(sell[t], buy[t]+p)
answer=sell[K]
K>=n//2 → unlimited (sum positive diffs)
```

## The trick (say it out loud)
> "buy[t] and sell[t] per transaction. buy builds on sell[t-1]-p; sell finishes with buy[t]+p."

## Code skeleton
```python
for p in prices:
    for t in range(1,K+1):
        buy[t]=max(buy[t], sell[t-1]-p)
        sell[t]=max(sell[t], buy[t]+p)
```

## Complexity
- Time O(n·K) · Space O(K)

## This trick solves more
Stock I/II/III (121/122/123) · Cooldown (309) · Fee (714) · Stock IV (188)
