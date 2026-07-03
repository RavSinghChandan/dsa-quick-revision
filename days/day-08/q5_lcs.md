# Q5 — Longest Common Subsequence (LeetCode 1143)

## 1. What is this question actually asking?

Given two strings, find the length of their longest common subsequence (LCS). A subsequence is a sequence that appears in the same relative order but not necessarily consecutively. For example, "ace" is a subsequence of "abcde". The LCS of "abcde" and "ace" is "ace" (length 3). Subsequences don't require consecutive characters, unlike substrings.

## 2. Pattern

2D Dynamic Programming — dp[i][j] = LCS length of text1[0..i-1] and text2[0..j-1]

## 3. Understand with a Diagram

```
text1 = "abcde"    text2 = "ace"

dp[i][j] = LCS length of text1[0..i-1] and text2[0..j-1]

       ""  a  c  e
   ""   0  0  0  0
   a:   0  1  1  1
   b:   0  1  1  1
   c:   0  1  2  2
   d:   0  1  2  2
   e:   0  1  2  3  ← Answer = 3 ("ace")

Recurrence:
  If text1[i-1] == text2[j-1]:
    dp[i][j] = dp[i-1][j-1] + 1  // characters match!
  Else:
    dp[i][j] = max(dp[i-1][j], dp[i][j-1])  // skip one char from either

Trace interesting cells:
dp[1][1]: text1[0]='a' == text2[0]='a' → dp[0][0]+1 = 1
dp[3][2]: text1[2]='c' == text2[1]='c' → dp[2][1]+1 = 1+1 = 2
dp[5][3]: text1[4]='e' == text2[2]='e' → dp[4][2]+1 = 2+1 = 3

Match visualization:
a b c d e
|   |   |
a   c   e
Common: a, c, e → length 3
```

## 4. Brute Force → Better → Optimal

### Brute Force — Try all subsequences
**Idea:** Generate all subsequences of text1, check if each is a subsequence of text2, return length of longest match.

O(2^m * n) where m=len(text1), n=len(text2). Exponential.

### Optimal — 2D DP
**Idea:** Build dp table bottom-up. dp[0][j] = dp[i][0] = 0 (empty string has LCS 0 with anything).

```
m = len(text1), n = len(text2)
dp = [[0]*(n+1) for _ in range(m+1)]

for i from 1 to m:
    for j from 1 to n:
        if text1[i-1] == text2[j-1]:
            dp[i][j] = dp[i-1][j-1] + 1
        else:
            dp[i][j] = max(dp[i-1][j], dp[i][j-1])

return dp[m][n]
```

O(m*n) time, O(m*n) space.

### Space Optimization — Two rows
**Idea:** dp[i][j] only depends on dp[i-1][j-1], dp[i-1][j], dp[i][j-1]. Use two rows.

```
prev = [0]*(n+1)
curr = [0]*(n+1)

for i from 1 to m:
    for j from 1 to n:
        if text1[i-1] == text2[j-1]:
            curr[j] = prev[j-1] + 1
        else:
            curr[j] = max(prev[j], curr[j-1])
    prev, curr = curr, [0]*(n+1)

return prev[n]
```

O(m*n) time, O(n) space.

## 5. Pseudocode (Optimal)

```
function longestCommonSubsequence(text1, text2):
    m = len(text1), n = len(text2)
    dp = 2D array (m+1) x (n+1), initialized to 0
    
    for i from 1 to m:
        for j from 1 to n:
            if text1[i-1] == text2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    return dp[m][n]
```

## 6. Python Code

```python
def longestCommonSubsequence(text1, text2):
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    return dp[m][n]


def lcs_string(text1, text2):
    """Reconstruct the actual LCS string."""
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(1, m+1):
        for j in range(1, n+1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    # Backtrack
    result = []
    i, j = m, n
    while i > 0 and j > 0:
        if text1[i-1] == text2[j-1]:
            result.append(text1[i-1])
            i -= 1; j -= 1
        elif dp[i-1][j] > dp[i][j-1]:
            i -= 1
        else:
            j -= 1
    return ''.join(reversed(result))


# Test 1 — LeetCode example 1
print(longestCommonSubsequence("abcde", "ace"))   # 3

# Test 2 — LeetCode example 2
print(longestCommonSubsequence("abc", "abc"))     # 3

# Test 3 — LeetCode example 3
print(longestCommonSubsequence("abc", "def"))     # 0

# Test 4 — one empty string
print(longestCommonSubsequence("", "abc"))        # 0

# Test 5 — actual LCS string
print(lcs_string("abcde", "ace"))   # "ace"
print(lcs_string("ABCBDAB", "BDCAB"))  # "BCAB" or "BDAB" (length 4)
```

## 7. Complexity Table

| Approach          | Time    | Space   | Notes                          |
|-------------------|---------|---------|--------------------------------|
| Brute Force       | O(2^m*n)| O(m)    | All subsequences               |
| 2D DP             | O(m*n)  | O(m*n)  | Full table                     |
| Two-row DP        | O(m*n)  | O(n)    | Space optimized                |

## 8. Edge Cases to Remember

- Either string empty — return 0
- Strings identical — return length of either
- No common characters — return 0
- One character strings — return 1 if equal, 0 if not
- LCS vs LCS length — the table gives length; backtracking gives the actual sequence
- LCS vs Longest Common Substring — substrings must be consecutive; dp recurrence differs (reset to 0 on mismatch)

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Longest Common Substring              | Same DP but reset on mismatch (consecutive)         | GFG        |
| Edit Distance                         | LCS + operations to transform one string            | LC 72      |
| Shortest Common Supersequence         | Length = m + n - LCS                               | LC 1092    |
| Minimum Insertions/Deletions to Match | m + n - 2*LCS                                     | GFG        |
| Delete Operation for Two Strings      | Same: m + n - 2*LCS                               | LC 583     |
| Longest Palindromic Subsequence       | LCS(s, reverse(s))                                 | LC 516     |
| Is Subsequence                        | Check if s is subsequence of t (greedy two-pointer) | LC 392     |
| Distinct Subsequences                 | Count distinct ways s appears in t as subsequence   | LC 115     |

## 10. The ONE Trick to Remember

**"Match → diagonal+1; mismatch → max(up, left) — two cases, that's the whole algorithm."**

When characters match, take the best LCS up to this point (diagonal) and add 1. When they don't match, we either skip the current character of text1 (go up: dp[i-1][j]) or skip the current character of text2 (go left: dp[i][j-1]), whichever is better. The entire algorithm is these two cases inside nested loops.
