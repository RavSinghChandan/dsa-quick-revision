# Day 9 — Dynamic Programming · Q8 · Longest Common Subsequence

---

## What is this question actually asking?

Find the length of the **longest subsequence common to two strings** (characters in order, not necessarily contiguous).

```
"abcde", "ace" → 3   ("ace")
"abc", "abc"   → 3
"abc", "def"   → 0
```

> THE two-string DP. A 2-D grid where each cell asks: do these characters match? The template for edit distance, diff tools, and sequence alignment.

---

## Pattern

```
PATTERN: 2-D DP on two strings — match → diagonal+1, else max of left/up
```

Whenever you see: *"longest/shortest common thing between two sequences"* → `dp[i][j]` for prefixes of length i, j. If the last chars match, `dp[i][j] = dp[i-1][j-1] + 1`; else `dp[i][j] = max(dp[i-1][j], dp[i][j-1])`.

---

## Understand with a diagram

```
       ""  a  c  e
   ""   0  0  0  0
   a    0  1  1  1
   b    0  1  1  1
   c    0  1  2  2
   d    0  1  2  2
   e    0  1  2  3   ← answer

match (s1[i]==s2[j]) → dp[i][j] = dp[i-1][j-1] + 1  (diagonal)
mismatch → dp[i][j] = max(dp[i-1][j], dp[i][j-1])
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all subsequences — O(2^n · m)
Generate and compare. Exponential.

### 🟢 Optimal — 2-D DP — O(n·m)
Fill the grid; O(min(n,m)) space with two rolling rows.

---

## Pseudocode (Optimal)

```
function lcs(s1, s2):
    dp = (n+1) x (m+1) grid of 0
    for i in 1..n:
        for j in 1..m:
            if s1[i-1] == s2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    return dp[n][m]
```

---

## Python Code

```python
def lcs(s1: str, s2: str) -> int:
    n, m = len(s1), len(s2)
    dp = [[0] * (m + 1) for _ in range(n + 1)]
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if s1[i - 1] == s2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1      # match → extend diagonal
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    return dp[n][m]


# Test
print(lcs("abcde", "ace"))   # 3
print(lcs("abc", "abc"))     # 3
print(lcs("abc", "def"))     # 0
```

---

## Complexity

| | Time | Space |
|---|---|---|
| 2-D DP | O(n·m) | O(n·m) (O(min) rolling) |

---

## Edge Cases to remember

- **1-indexed dp with a 0 row/column** — `dp[0][*]` and `dp[*][0]` are 0 (empty prefix).
- **Reconstruct the LCS** — walk back from `dp[n][m]`: diagonal on a match, else toward the larger neighbor.
- **LCS vs substring** — substring must be contiguous (different DP); this is subsequence.
- **Rolling rows** → O(min(n,m)) space.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Edit Distance (Q9) | Same grid, 3 ops |
| 2 | Longest Common Substring | Reset on mismatch |
| 3 | Shortest Common Supersequence (LC 1092) | n+m−LCS |
| 4 | Delete Operation for Two Strings (LC 583) | n+m−2·LCS |
| 5 | Distinct Subsequences (LC 115) | Count DP |
| 6 | Longest Palindromic Subsequence (LC 516) | LCS(s, reverse s) |
| 7 | Interleaving String (LC 97) | 2-D DP |
| 8 | Minimum ASCII delete sum (LC 712) | Weighted LCS |

---

## The ONE trick to remember

```
"MATCH → dp[i-1][j-1] + 1; MISMATCH → max(dp[i-1][j], dp[i][j-1])."
```

Build a grid over the two prefixes. Matching last characters extend the diagonal by 1; otherwise take the better of dropping one character from either string. `dp[n][m]` is the LCS length.

> Memory hook: "Same char? take the diagonal and add one. Different? keep the best neighbor."

---
