# ⚡ Quick Note · Day 9 — DP · Q8 · Longest Common Subsequence
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Longest subsequence common to two strings."

## Pattern
`2-D DP: match→diagonal+1, mismatch→max(left,up)`

## Visual in your head
```
dp[i][j] over prefixes
match s1[i]==s2[j] → dp[i-1][j-1]+1
else → max(dp[i-1][j], dp[i][j-1])
answer dp[n][m]
```

## The trick (say it out loud)
> "Match? diagonal +1. Mismatch? best of dropping one char from either. dp[n][m]."

## Code skeleton
```python
if s1[i-1]==s2[j-1]: dp[i][j]=dp[i-1][j-1]+1
else: dp[i][j]=max(dp[i-1][j], dp[i][j-1])
```

## Complexity
- Time O(n·m) · Space O(n·m) (O(min) rolling)

## This trick solves more
Edit Distance · Longest Common Substring · SCS (1092) · Delete Ops (583) · Palindromic Subseq (516)
