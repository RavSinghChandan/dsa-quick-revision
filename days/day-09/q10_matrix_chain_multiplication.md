# Day 9 — Dynamic Programming · Q10 · Matrix Chain Multiplication

---

## What is this question actually asking?

Given matrix dimensions, find the **minimum number of scalar multiplications** to multiply the chain — by choosing the best **parenthesization**.

```
dims = [40,20,30,10,30]   (matrices 40x20, 20x30, 30x10, 10x30)
→ 26000   (optimal placement of parentheses)
```

> The classic **interval DP**: split the chain at every possible point `k`, solve both halves, add the cost of combining them. The template for palindrome partitioning, burst balloons, and boolean parenthesization.

---

## Pattern

```
PATTERN: Interval DP — dp[i][j] = min over split point k
```

Whenever you see: *"optimal way to combine/parenthesize a sequence"* → `dp[i][j]` = best cost for the sub-chain i..j. Try every split `k` between i and j; combine `dp[i][k] + dp[k+1][j] + costOfMerging`.

---

## Understand with a diagram

```
dp[i][j] = min over k in i..j-1 of:
    dp[i][k] + dp[k+1][j] + dims[i-1]*dims[k]*dims[j]

Matrix i has dimension dims[i-1] x dims[i].
Merging A(i..k) and B(k+1..j) costs rows(A)*cols(A)*cols(B) = dims[i-1]*dims[k]*dims[j].

Fill by increasing chain length (interval size), base dp[i][i]=0.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all parenthesizations — Catalan number (exponential)
Try every way to place parentheses.

### 🟢 Optimal — interval DP — O(n³)
Solve every sub-chain once; combine at each split. O(n²) states × O(n) splits.

---

## Pseudocode (Optimal)

```
function mcm(dims):
    n = len(dims) - 1        ← number of matrices
    dp = n x n grid of 0     (1-indexed matrices 1..n)
    for length in 2..n:
        for i in 1..n-length+1:
            j = i + length - 1
            dp[i][j] = inf
            for k in i..j-1:
                cost = dp[i][k] + dp[k+1][j] + dims[i-1]*dims[k]*dims[j]
                dp[i][j] = min(dp[i][j], cost)
    return dp[1][n]
```

---

## Python Code

```python
def matrix_chain_order(dims: list[int]) -> int:
    n = len(dims) - 1                       # matrices 1..n
    # dp[i][j] = min cost to multiply matrices i..j (1-indexed)
    dp = [[0] * (n + 1) for _ in range(n + 1)]
    for length in range(2, n + 1):          # chain length
        for i in range(1, n - length + 2):
            j = i + length - 1
            dp[i][j] = float('inf')
            for k in range(i, j):           # split point
                cost = (dp[i][k] + dp[k + 1][j]
                        + dims[i - 1] * dims[k] * dims[j])
                dp[i][j] = min(dp[i][j], cost)
    return dp[1][n]


# Test
print(matrix_chain_order([40,20,30,10,30]))   # 26000
print(matrix_chain_order([10,20,30]))          # 6000  (single multiply)
print(matrix_chain_order([1,2,3,4]))           # 18
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Interval DP | O(n³) | O(n²) |

---

## Edge Cases to remember

- **`dims` has n+1 numbers for n matrices** — matrix i is `dims[i-1] x dims[i]`.
- **Fill by increasing interval length** — `dp[i][j]` needs shorter intervals already solved.
- **Cost of merge** = `dims[i-1]*dims[k]*dims[j]` — the rows-of-left × shared × cols-of-right.
- **`dp[i][i]=0`** — a single matrix needs no multiplication.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Burst Balloons (LC 312) | Interval DP, split on last |
| 2 | Palindrome Partitioning II (LC 132) | Interval DP |
| 3 | Boolean Parenthesization | Interval DP + T/F counts |
| 4 | Minimum Cost to Cut a Stick (LC 1547) | Interval DP |
| 5 | Guess Number Higher/Lower II (LC 375) | Interval DP |
| 6 | Remove Boxes (LC 546) | Interval DP |
| 7 | Stone Game variants | Interval DP |
| 8 | Optimal BST | Interval DP |

---

## The ONE trick to remember

```
"INTERVAL DP: dp[i][j] = min over k of dp[i][k] + dp[k+1][j] + mergeCost."
```

Try every place to split the chain. Solve the two halves recursively (via smaller intervals filled first), then add the cost of multiplying the two resulting matrices. Fill by increasing interval length.

> Memory hook: "Try every last cut; best left cost + best right cost + the cost to join them."

---
