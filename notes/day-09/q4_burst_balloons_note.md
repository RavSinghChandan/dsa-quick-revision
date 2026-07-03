# Q4 — Burst Balloons (2-min card) — LC 312

**Burst all balloons to maximize coins. Coins[i] = nums[i-1]*nums[i]*nums[i+1]. Boundaries = 1.**

**Pattern:** Interval DP — think of k as LAST balloon burst in interval (i,j), not first

```
nums=[3,1,5,8] → arr=[1,3,1,5,8,1]

dp[i][j] = max coins bursting everything strictly between i and j

Key recurrence: think k is LAST burst in (i,j):
  dp[i][j] = max(dp[i][k] + arr[i]*arr[k]*arr[j] + dp[k][j])  for k in (i+1..j-1)

When k is last, i and j are boundaries → coins = arr[i]*arr[k]*arr[j] (clean!)

dp[0][5] = 167  ← Answer
```

**The trick:** "Last burst, not first. Add boundary 1s. dp[i][j] = max over k: left + arr[i]*arr[k]*arr[j] + right. Fill by interval length."

```python
arr = [1] + nums + [1]
n = len(arr)
dp = [[0]*n for _ in range(n)]
for l in range(2, n):
    for i in range(n - l):
        j = i + l
        for k in range(i+1, j):
            dp[i][j] = max(dp[i][j], dp[i][k] + arr[i]*arr[k]*arr[j] + dp[k][j])
return dp[0][n-1]
```

**Complexity:** Time O(n^3) | Space O(n^2)

**Same pattern solves:** Matrix Chain Multiplication, Strange Printer (LC 664), Remove Boxes (LC 546), Merge Stones (LC 1000), Zuma Game (LC 488), Cut Stick (LC 1547), Triangulation (LC 1039).
