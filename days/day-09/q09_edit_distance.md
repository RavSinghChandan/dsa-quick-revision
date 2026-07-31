# Day 9 — Dynamic Programming · Q9 · Edit Distance

---

## What is this question actually asking?

Minimum number of operations (**insert, delete, replace**) to convert string `s1` into `s2`.

```
"horse" → "ros"      → 3   (replace h→r, delete o, delete e ... =3)
"intention" → "execution" → 5
```

> The famous Levenshtein distance. Same 2-D grid as LCS but each cell chooses the cheapest of three edit operations.

---

## Pattern

```
PATTERN: 2-D DP on two strings — match → diagonal, else 1 + min(3 ops)
```

Whenever you see: *"min operations to transform one string into another / spell-check / diff"* → `dp[i][j]` = edits for prefixes i, j. Match → carry the diagonal; mismatch → `1 + min(insert, delete, replace)`.

---

## Understand with a diagram

```
       ""  r  o  s
   ""   0  1  2  3
   h    1  1  2  3
   o    2  2  1  2
   r    3  2  2  2
   s    4  3  3  2
   e    5  4  4  3   ← answer

match  s1[i]==s2[j] → dp[i][j] = dp[i-1][j-1]                 (no cost)
else → dp[i][j] = 1 + min( dp[i-1][j],    ← delete from s1
                           dp[i][j-1],    ← insert into s1
                           dp[i-1][j-1] ) ← replace
Base: dp[i][0]=i (delete all), dp[0][j]=j (insert all)
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — recursion — O(3^n)
Try every op at each mismatch.

### 🟢 Optimal — 2-D DP — O(n·m)
Fill the grid; O(min(n,m)) space with rolling rows.

---

## Pseudocode (Optimal)

```
function edit_distance(s1, s2):
    dp = (n+1) x (m+1)
    for i: dp[i][0] = i          ← delete all
    for j: dp[0][j] = j          ← insert all
    for i in 1..n:
        for j in 1..m:
            if s1[i-1] == s2[j-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = 1 + min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])
    return dp[n][m]
```

---

## Python Code

```python
def edit_distance(s1: str, s2: str) -> int:
    n, m = len(s1), len(s2)
    dp = [[0] * (m + 1) for _ in range(n + 1)]
    for i in range(n + 1):
        dp[i][0] = i               # delete all i chars
    for j in range(m + 1):
        dp[0][j] = j               # insert all j chars
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if s1[i - 1] == s2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]         # no operation
            else:
                dp[i][j] = 1 + min(
                    dp[i - 1][j],       # delete
                    dp[i][j - 1],       # insert
                    dp[i - 1][j - 1],   # replace
                )
    return dp[n][m]


# Test
print(edit_distance("horse", "ros"))            # 3
print(edit_distance("intention", "execution"))  # 5
print(edit_distance("", "abc"))                 # 3
```

---

## Complexity

| | Time | Space |
|---|---|---|
| 2-D DP | O(n·m) | O(n·m) (O(min) rolling) |

---

## Edge Cases to remember

- **Base cases** — `dp[i][0]=i` (delete all), `dp[0][j]=j` (insert all). Forgetting these breaks everything.
- **Three ops on mismatch** — delete (up), insert (left), replace (diagonal); take the min + 1.
- **Match costs 0** — carry the diagonal unchanged.
- **Weighted edits** — assign different costs per op if the problem varies (LC 712 weights by ASCII).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Longest Common Subsequence (Q8) | Same grid |
| 2 | One Edit Distance (LC 161) | Single-op check |
| 3 | Delete Ops for Two Strings (LC 583) | insert/delete only |
| 4 | Min ASCII Delete Sum (LC 712) | Weighted |
| 5 | Regular Expression Matching (LC 10) | 2-D DP with wildcards |
| 6 | Wildcard Matching (LC 44) | 2-D DP |
| 7 | Distinct Subsequences (LC 115) | Count DP |
| 8 | Spell checker / autocorrect | Levenshtein |

---

## The ONE trick to remember

```
"MATCH → CARRY DIAGONAL; MISMATCH → 1 + min(delete↑, insert←, replace↖)."
```

Same grid as LCS. When characters match, no edit is needed (take the diagonal). On a mismatch, pick the cheapest of delete, insert, or replace and add 1. Base rows/columns are "insert/delete everything."

> Memory hook: "Same letter, free. Different, pay one for the cheapest of insert/delete/replace."

---
