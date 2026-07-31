# ⚡ Quick Note · Day 9 — DP · Q9 · Edit Distance
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Min insert/delete/replace to turn s1 into s2."

## Pattern
`2-D DP: match→diagonal, mismatch→1+min(3 ops)`

## Visual in your head
```
dp[i][0]=i, dp[0][j]=j
match → dp[i-1][j-1]
else → 1 + min(delete↑, insert←, replace↖)
```

## The trick (say it out loud)
> "Match carries the diagonal free. Mismatch = 1 + min(delete, insert, replace). Base = insert/delete all."

## Code skeleton
```python
if s1[i-1]==s2[j-1]: dp[i][j]=dp[i-1][j-1]
else: dp[i][j]=1+min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])
```

## Complexity
- Time O(n·m) · Space O(n·m) (O(min) rolling)

## This trick solves more
LCS · One Edit Distance (161) · Delete Ops (583) · Regex Match (10) · Wildcard (44) · Min ASCII (712)
