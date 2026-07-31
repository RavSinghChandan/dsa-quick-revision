# ⚡ Quick Note · Day 9 — DP · Q1 · Frog Jump K
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Min cost to reach the last stair, jumps up to K, cost = |height diff|."

## Pattern
`1-D DP — dp[i] = min over last K states`

## Visual in your head
```
dp[0]=0
dp[i]=min_{j=1..K} dp[i-j] + |h[i]-h[i-j]|
```

## The trick (say it out loud)
> "Each stair's cost = best of the K stairs it could jump from, plus the height diff."

## Code skeleton
```python
for i in range(1,n):
    for j in range(1,K+1):
        if i-j>=0: dp[i]=min(dp[i], dp[i-j]+abs(h[i]-h[i-j]))
```

## Complexity
- Time O(n·K) · Space O(n) (O(K) rolling)

## This trick solves more
Climbing Stairs (70) · Min Cost Stairs (746) · House Robber · Decode Ways (91) · Coin Change
