# Q2 — Matrix Chain Multiplication

## 1. What is this question actually asking?

Given a chain of matrices A1, A2, ..., An, find the minimum number of scalar multiplications needed to compute the product A1*A2*...*An. The order in which we parenthesize the matrices affects the number of multiplications but not the result. Matrices are given as an array `dims` where matrix i has dimensions dims[i-1] x dims[i].

For example: A(10x30) * B(30x5) * C(5x60)
- (AB)C: 10*30*5 + 10*5*60 = 1500 + 3000 = 4500
- A(BC): 30*5*60 + 10*30*60 = 9000 + 18000 = 27000
Minimum = 4500 by parenthesizing as (AB)C.

## 2. Pattern

Interval DP — dp[i][j] = minimum cost to multiply matrices i through j. Try all split points k between i and j.

## 3. Understand with a Diagram

```
dims = [10, 30, 5, 60]
Matrix 1: 10x30, Matrix 2: 30x5, Matrix 3: 5x60

dp[i][j] = min cost to multiply matrices i..j (1-indexed)
Base case: dp[i][i] = 0 (single matrix, no multiplication)

Fill diagonally (by chain length l):

l=1: dp[1][1]=dp[2][2]=dp[3][3]=0

l=2: (chain of 2 matrices)
  dp[1][2]: k=1: dims[0]*dims[1]*dims[2] = 10*30*5 = 1500
  dp[2][3]: k=2: dims[1]*dims[2]*dims[3] = 30*5*60 = 9000

l=3: (chain of 3 matrices)
  dp[1][3]: try k=1 and k=2
    k=1: dp[1][1] + dp[2][3] + dims[0]*dims[1]*dims[3]
         = 0 + 9000 + 10*30*60 = 0 + 9000 + 18000 = 27000
    k=2: dp[1][2] + dp[3][3] + dims[0]*dims[2]*dims[3]
         = 1500 + 0 + 10*5*60 = 1500 + 0 + 3000 = 4500
    dp[1][3] = min(27000, 4500) = 4500 ✓

Recurrence:
  dp[i][j] = min over k from i to j-1 of:
    dp[i][k] + dp[k+1][j] + dims[i-1] * dims[k] * dims[j]

Split k means: (Matrices i..k) * (Matrices k+1..j)
Cost of split:  left subproblem + right subproblem + cost of final multiplication
The final multiplication of the two resulting matrices costs: rows(left) * cols(left) * cols(right)
= dims[i-1] * dims[k] * dims[j]

          Matrix Index:    1      2      3
          Dimensions:  10x30  30x5   5x60

j=1  j=2  j=3
i=1:  0   1500  4500   ← diagonal fill direction
i=2:  -    0   9000
i=3:  -    -    0
```

## 4. Brute Force → Better → Optimal

### Brute Force — Recursion without memoization
**Idea:** Try all split points recursively.

```
function mcm(i, j):
    if i == j: return 0
    min_cost = infinity
    for k from i to j-1:
        cost = mcm(i,k) + mcm(k+1,j) + dims[i-1]*dims[k]*dims[j]
        min_cost = min(min_cost, cost)
    return min_cost
```

O(2^n) time — overlapping subproblems cause exponential blowup.

### Better — Top-down with memoization
**Idea:** Cache results in memo[i][j]. O(n^3) time, O(n^2) space.

### Optimal — Bottom-up DP (interval DP)
**Idea:** Fill table for chain length l = 2 to n, then l = 3, ..., n. For each (i,j), try all k.

O(n^3) time, O(n^2) space. Avoids recursion overhead.

## 5. Pseudocode (Optimal)

```
function matrixChainOrder(dims[]):
    n = len(dims) - 1   // number of matrices
    dp = 2D array n x n, initialized to 0
    
    // l = chain length
    for l from 2 to n:
        for i from 1 to n-l+1:
            j = i + l - 1
            dp[i][j] = infinity
            
            for k from i to j-1:
                cost = dp[i][k] + dp[k+1][j] + dims[i-1]*dims[k]*dims[j]
                dp[i][j] = min(dp[i][j], cost)
    
    return dp[1][n]
```

## 6. Python Code

```python
def matrixChainMultiplication(dims):
    """
    dims: array of length n+1 where matrix i has dims[i-1] x dims[i]
    Returns minimum scalar multiplications needed.
    """
    n = len(dims) - 1  # number of matrices
    
    # dp[i][j] = min cost to multiply matrices i..j (1-indexed)
    dp = [[0] * (n + 1) for _ in range(n + 1)]
    
    # l = chain length
    for l in range(2, n + 1):
        for i in range(1, n - l + 2):
            j = i + l - 1
            dp[i][j] = float('inf')
            
            for k in range(i, j):
                cost = (dp[i][k] + dp[k+1][j] + 
                        dims[i-1] * dims[k] * dims[j])
                dp[i][j] = min(dp[i][j], cost)
    
    return dp[1][n]


def matrixChainWithBracket(dims):
    """Also returns the optimal parenthesization."""
    n = len(dims) - 1
    dp = [[0] * (n + 1) for _ in range(n + 1)]
    split = [[0] * (n + 1) for _ in range(n + 1)]
    
    for l in range(2, n + 1):
        for i in range(1, n - l + 2):
            j = i + l - 1
            dp[i][j] = float('inf')
            for k in range(i, j):
                cost = dp[i][k] + dp[k+1][j] + dims[i-1] * dims[k] * dims[j]
                if cost < dp[i][j]:
                    dp[i][j] = cost
                    split[i][j] = k
    
    def print_optimal(i, j):
        if i == j:
            return f"A{i}"
        k = split[i][j]
        return f"({print_optimal(i, k)} * {print_optimal(k+1, j)})"
    
    return dp[1][n], print_optimal(1, n)


# Test 1 — standard example
dims = [10, 30, 5, 60]
print(matrixChainMultiplication(dims))     # 4500

# Test 2 — 4 matrices
dims2 = [40, 20, 30, 10, 30]
print(matrixChainMultiplication(dims2))    # 26000

# Test 3 — single matrix
dims3 = [10, 20]
print(matrixChainMultiplication(dims3))    # 0

# Test 4 — two matrices
dims4 = [10, 30, 5]
print(matrixChainMultiplication(dims4))    # 10*30*5 = 1500

# Test 5 — with parenthesization
cost, bracket = matrixChainWithBracket([10, 30, 5, 60])
print(cost, bracket)   # 4500 ((A1 * A2) * A3)
```

## 7. Complexity Table

| Approach              | Time    | Space   | Notes                       |
|-----------------------|---------|---------|-----------------------------|
| Brute Recursion       | O(2^n)  | O(n)    | Exponential                 |
| Top-down Memo         | O(n^3)  | O(n^2)  | Same asymptotic as bottom-up|
| Bottom-up Interval DP | O(n^3)  | O(n^2)  | Preferred                   |

## 8. Edge Cases to Remember

- Single matrix (n=1) — cost = 0
- Two matrices — only one way to multiply, cost = r1*c1*c2
- Chain length determines order of filling dp table (l from 2 to n)
- Indexing: matrix i has dimensions dims[i-1] x dims[i]; split k means left matrices use dims[i-1] and dims[k]
- Cost of multiplying result of left (dims[i-1] x dims[k]) with result of right (dims[k] x dims[j]) = dims[i-1] * dims[k] * dims[j]

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Burst Balloons                        | Interval DP, similar "try all split points"         | LC 312     |
| Strange Printer                       | Interval DP on characters                           | LC 664     |
| Minimum Cost to Cut a Stick           | Interval DP on cuts                                 | LC 1547    |
| Optimal BST                           | Interval DP on search frequencies                   | Classic    |
| Boolean Parenthesization             | Interval DP on operators                            | GFG        |
| Palindrome Partitioning II            | DP on intervals                                     | LC 132     |
| Zuma Game                             | Interval DP on blocks                               | LC 488     |
| Minimum Score Triangulation           | Interval DP on polygon triangulation                | LC 1039    |

## 10. The ONE Trick to Remember

**"Interval DP: for each chain length l, try every split k; cost = left + right + rows*cols_split*cols."**

The key insight: parenthesizing at k splits the chain into (matrices i..k) and (matrices k+1..j). After multiplying, left becomes dims[i-1] x dims[k] and right becomes dims[k] x dims[j], so the final merge costs dims[i-1] * dims[k] * dims[j]. Fill by chain length (outer loop l=2..n), not by row/column order, because dp[i][j] depends on shorter subproblems.
