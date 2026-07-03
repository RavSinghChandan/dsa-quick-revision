# Q1 — Edit Distance (2-min card) — LC 72

**Min operations (insert/delete/replace) to convert word1 to word2.**

**Pattern:** 2D DP — match→diagonal; mismatch→1+min(delete=up, insert=left, replace=diagonal)

```
     ""  r  o  s
"":   0  1  2  3
h:    1  1  2  3
o:    2  2  1  2
r:    3  2  2  2
s:    4  3  3  2
e:    5  4  4  3  ← Answer = 3

horse→ros: replace h→r, delete r, delete e
```

**The trick:** "Match → dp[i-1][j-1]; mismatch → 1 + min(up=delete, left=insert, diagonal=replace)."

```python
dp = [[0]*(n+1) for _ in range(m+1)]
for i in range(m+1): dp[i][0] = i
for j in range(n+1): dp[0][j] = j
for i in range(1, m+1):
    for j in range(1, n+1):
        if word1[i-1]==word2[j-1]: dp[i][j]=dp[i-1][j-1]
        else: dp[i][j]=1+min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])
return dp[m][n]
```

**Complexity:** Time O(m*n) | Space O(m*n) [O(n) with 1D rolling array]

**Same pattern solves:** One Edit Distance (LC 161), Delete for Two Strings (LC 583), Min ASCII Delete Sum (LC 712), Shortest Common Supersequence (LC 1092), Distinct Subsequences (LC 115), Wildcard Matching (LC 44), Regex Matching (LC 10).
