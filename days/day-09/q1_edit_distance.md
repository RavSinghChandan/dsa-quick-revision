# Q1 — Edit Distance (LeetCode 72)

## 1. What is this question actually asking?

Given two words, find the minimum number of operations to convert one word to another. The allowed operations are: insert a character, delete a character, or replace a character (each counts as 1 operation). This is known as the Levenshtein distance. For example, converting "horse" to "ros" takes 3 operations.

## 2. Pattern

2D Dynamic Programming — dp[i][j] = min operations to convert word1[0..i-1] to word2[0..j-1]

## 3. Understand with a Diagram

```
word1 = "horse"    word2 = "ros"

dp[i][j] = min edits to convert word1[0..i-1] to word2[0..j-1]

Base cases:
  dp[0][j] = j  // insert j characters (convert "" to word2[0..j-1])
  dp[i][0] = i  // delete i characters (convert word1[0..i-1] to "")

       ""  r  o  s
  "":   0  1  2  3
  h:    1  1  2  3
  o:    2  2  1  2
  r:    3  2  2  2
  s:    4  3  3  2
  e:    5  4  4  3  ← Answer = dp[5][3] = 3

Recurrence:
  If word1[i-1] == word2[j-1]:
    dp[i][j] = dp[i-1][j-1]  // no operation needed
  Else:
    dp[i][j] = 1 + min(
      dp[i-1][j],     // delete word1[i-1]
      dp[i][j-1],     // insert word2[j-1] 
      dp[i-1][j-1]    // replace word1[i-1] with word2[j-1]
    )

Trace dp[2][2] (o, o):
  word1[1]='o' == word2[1]='o' → dp[2][2] = dp[1][1] = 1 ✓

Trace dp[5][3] (e, s):
  word1[4]='e' ≠ word2[2]='s' → 1 + min(dp[4][3], dp[5][2], dp[4][2])
  = 1 + min(2, 4, 2) = 3 ✓

Operations for horse→ros:
  horse → rorse (replace h with r)
  rorse → rose  (delete r)
  rose  → ros   (delete e)
  3 operations ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — Recursion without memoization
**Idea:** Try all operations at each step.

```
function edit(i, j):
    if i == 0: return j  // insert all
    if j == 0: return i  // delete all
    if word1[i-1] == word2[j-1]: return edit(i-1, j-1)
    return 1 + min(edit(i-1,j), edit(i,j-1), edit(i-1,j-1))
```

O(3^(m+n)). Exponential.

### Optimal — 2D DP
**Idea:** Build dp table. Each cell (i,j) represents edit distance between prefixes.

O(m*n) time, O(m*n) space.

### Space Optimization — Single row
**Idea:** dp[i][j] depends only on dp[i-1][j-1], dp[i-1][j], dp[i][j-1]. Use previous row + diagonal variable.

```
prev = list(range(n+1))

for i from 1 to m:
    curr = [i] + [0]*n
    for j from 1 to n:
        if word1[i-1] == word2[j-1]:
            curr[j] = prev[j-1]
        else:
            curr[j] = 1 + min(prev[j], curr[j-1], prev[j-1])
    prev = curr

return prev[n]
```

O(m*n) time, O(n) space.

## 5. Pseudocode (Optimal)

```
function minDistance(word1, word2):
    m = len(word1), n = len(word2)
    dp = 2D array (m+1) x (n+1)
    
    for i in 0..m: dp[i][0] = i
    for j in 0..n: dp[0][j] = j
    
    for i in 1..m:
        for j in 1..n:
            if word1[i-1] == word2[j-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = 1 + min(dp[i-1][j],    // delete
                                    dp[i][j-1],    // insert
                                    dp[i-1][j-1])  // replace
    
    return dp[m][n]
```

## 6. Python Code

```python
def minDistance(word1, word2):
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Base cases
    for i in range(m + 1):
        dp[i][0] = i
    for j in range(n + 1):
        dp[0][j] = j
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i-1] == word2[j-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = 1 + min(
                    dp[i-1][j],     # delete
                    dp[i][j-1],     # insert
                    dp[i-1][j-1]    # replace
                )
    
    return dp[m][n]


# Test 1 — LeetCode example 1
print(minDistance("horse", "ros"))    # 3

# Test 2 — LeetCode example 2
print(minDistance("intention", "execution"))  # 5

# Test 3 — same word
print(minDistance("abc", "abc"))      # 0

# Test 4 — empty string
print(minDistance("", "abc"))         # 3
print(minDistance("abc", ""))         # 3

# Test 5 — single char
print(minDistance("a", "b"))          # 1
```

## 7. Complexity Table

| Approach          | Time    | Space   | Notes                       |
|-------------------|---------|---------|-----------------------------|
| Brute Recursion   | O(3^m+n)| O(m+n)  | Exponential                 |
| 2D DP             | O(m*n)  | O(m*n)  | Full table                  |
| 1D DP             | O(m*n)  | O(n)    | Space optimized             |

## 8. Edge Cases to Remember

- Either string empty — cost = length of the other (all inserts or all deletes)
- Both empty — cost = 0
- Same strings — cost = 0
- One character — min(insert, delete, replace) = 0 if same, 1 if different
- Three operations: delete (dp[i-1][j]), insert (dp[i][j-1]), replace (dp[i-1][j-1])

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| One Edit Distance                     | Check if exactly 1 edit apart                       | LC 161     |
| Delete Operation for Two Strings      | Only delete allowed: m+n-2*LCS                     | LC 583     |
| Minimum ASCII Delete Sum              | Weighted delete: sum of ASCII of deleted chars      | LC 712     |
| Longest Common Subsequence            | Foundation — edit distance builds on LCS            | LC 1143    |
| Distinct Subsequences                 | Count ways to form t from s                         | LC 115     |
| Regular Expression Matching           | DP with special chars                               | LC 10      |
| Wildcard Matching                     | DP with '?' and '*' wildcards                       | LC 44      |
| Shortest Common Supersequence         | LCS-based, m+n-LCS                                 | LC 1092    |

## 10. The ONE Trick to Remember

**"Match → diagonal; mismatch → 1 + min(delete=up, insert=left, replace=diagonal)."**

When characters match, no operation needed — take dp[i-1][j-1]. When they don't, pay 1 and choose the cheapest of three operations: delete word1's current char (come from dp[i-1][j]), insert word2's current char (come from dp[i][j-1]), or replace (come from dp[i-1][j-1]). The base case fills the first row/column with 0,1,2,...,n (cost to build/delete each prefix).
