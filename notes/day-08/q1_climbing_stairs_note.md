# Q1 — Climbing Stairs (2-min card) — LC 70

**Count distinct ways to reach top of n-step staircase (1 or 2 steps at a time).**

**Pattern:** 1D DP — dp[i] = dp[i-1] + dp[i-2] (it's Fibonacci!)

```
n=5:
step:  1   2   3   4   5
ways:  1   2   3   5   8

ways[3] = ways[2]+ways[1] = 2+1 = 3
ways[4] = ways[3]+ways[2] = 3+2 = 5
ways[5] = ways[4]+ways[3] = 5+3 = 8

Answer = 8
```

**The trick:** "Fibonacci in disguise — ways[i] = ways[i-1] + ways[i-2]."

```python
if n <= 2: return n
prev2, prev1 = 1, 2
for _ in range(3, n+1):
    prev2, prev1 = prev1, prev1+prev2
return prev1
```

**Complexity:** Time O(n) | Space O(1)

**Same pattern solves:** Fibonacci (LC 509), Min Cost Climbing Stairs (LC 746), House Robber (LC 198), Decode Ways (LC 91), N-th Tribonacci (LC 1137), Jump Game (LC 55), Jump Game II (LC 45), k-step generalization.
