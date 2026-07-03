# Q2 — Matrix Chain Multiplication (2-min card)

**Min scalar multiplications to compute product of n chained matrices.**

**Pattern:** Interval DP — dp[i][j] = min cost to multiply matrices i..j; try all split points k

```
dims = [10, 30, 5, 60]   (3 matrices: 10x30, 30x5, 5x60)

l=2:
  dp[1][2] = 10*30*5 = 1500
  dp[2][3] = 30*5*60 = 9000

l=3:
  dp[1][3]:
    k=1: dp[1][1]+dp[2][3]+10*30*60 = 0+9000+18000 = 27000
    k=2: dp[1][2]+dp[3][3]+10*5*60  = 1500+0+3000  = 4500
  dp[1][3] = 4500   ← Answer
```

**The trick:** "Try every split k; cost = left_cost + right_cost + dims[i-1]*dims[k]*dims[j]. Fill by chain length, not row/column."

```python
n = len(dims) - 1
dp = [[0]*(n+1) for _ in range(n+1)]
for l in range(2, n+1):           # chain length
    for i in range(1, n-l+2):
        j = i + l - 1
        dp[i][j] = float('inf')
        for k in range(i, j):
            cost = dp[i][k] + dp[k+1][j] + dims[i-1]*dims[k]*dims[j]
            dp[i][j] = min(dp[i][j], cost)
return dp[1][n]
```

**Complexity:** Time O(n^3) | Space O(n^2)

**Same pattern solves:** Burst Balloons (LC 312), Minimum Cost Cut Stick (LC 1547), Strange Printer (LC 664), Boolean Parenthesization (GFG), Optimal BST (Classic), Score Triangulation (LC 1039).
