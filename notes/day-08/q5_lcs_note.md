# Q5 — Longest Common Subsequence (2-min card) — LC 1143

**Length of longest subsequence appearing in both strings (not necessarily consecutive).**

**Pattern:** 2D DP — match→diagonal+1; mismatch→max(up, left)

```
     ""  a  c  e
 ""   0  0  0  0
 a:   0  1  1  1   ← a==a: dp[0][0]+1=1
 b:   0  1  1  1   ← b≠a: max(1,1)=1; b≠c: max(1,1)=1
 c:   0  1  2  2   ← c==c: dp[1][1]+1=2
 d:   0  1  2  2
 e:   0  1  2  3   ← e==e: dp[4][2]+1=3

Answer = dp[5][3] = 3  ("ace")
```

**The trick:** "Match → dp[i-1][j-1]+1; mismatch → max(dp[i-1][j], dp[i][j-1])."

```python
dp = [[0]*(n+1) for _ in range(m+1)]
for i in range(1, m+1):
    for j in range(1, n+1):
        if text1[i-1]==text2[j-1]: dp[i][j]=dp[i-1][j-1]+1
        else: dp[i][j]=max(dp[i-1][j], dp[i][j-1])
return dp[m][n]
```

**Complexity:** Time O(m*n) | Space O(m*n) [or O(n) with two-row]

**Same pattern solves:** Edit Distance (LC 72), Shortest Common Supersequence (LC 1092), Delete for Two Strings (LC 583), Longest Palindromic Subsequence (LC 516), Distinct Subsequences (LC 115), Min Insert/Delete, Longest Common Substring (GFG).
