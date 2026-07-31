# ⚡ Quick Note · Day 9 — DP · Q3 · Min Falling Path Sum
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Min path sum top→bottom, moving down or diagonally."

## Pattern
`Grid DP — cell += min of 3 cells above`

## Visual in your head
```
dp[r][c] += min(dp[r-1][c-1], dp[r-1][c], dp[r-1][c+1])  (respect borders)
answer = min(last row)
```

## The trick (say it out loud)
> "Each cell += min of the three cells above (guard borders). Answer = min of the LAST row."

## Code skeleton
```python
best=dp[r-1][c]
if c>0: best=min(best,dp[r-1][c-1])
if c<n-1: best=min(best,dp[r-1][c+1])
dp[r][c]+=best
```

## Complexity
- Time O(R·C) · Space O(R·C) (O(C) rolling)

## This trick solves more
Triangle (120) · Min Path Sum (64) · Cherry Pickup II (1463) · Dungeon Game (174) · Unique Paths (62)
