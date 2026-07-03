# Q3 — Egg Drop Problem (2-min card) — LC 887

**k eggs, n floors — find minimum worst-case trials to determine critical floor F.**

**Pattern:** Flip question to dp[t][k] = max floors checkable with t trials and k eggs

```
n=10, k=2:

t=1: dp[1][1]=1, dp[1][2]=1
t=2: dp[2][2]=dp[1][1]+1+dp[1][2]=3
t=3: dp[3][2]=dp[2][1]+1+dp[2][2]=2+1+3=6
t=4: dp[4][2]=dp[3][1]+1+dp[3][2]=3+1+6=10 ✓

Answer = 4  (first t where dp[t][k] >= n)
```

**The trick:** "dp[t][k]=dp[t-1][k-1]+1+dp[t-1][k]. Find min t where dp[t][k]>=n. Break→check below, Survive→check above."

```python
dp = [0] * (k + 1)
t = 0
while dp[k] < n:
    t += 1
    new_dp = [0] * (k + 1)
    for j in range(1, k + 1):
        new_dp[j] = dp[j-1] + 1 + dp[j]
    dp = new_dp
return t
```

**Complexity:** Time O(k log n) | Space O(k)

**Classic DP:** dp[floors][eggs], O(kn^2) — try each floor x as drop point, worst=1+max(breaks=dp[x-1][j-1], survives=dp[i-x][j])

**Same pattern solves:** Burst Balloons (LC 312), Strange Printer (LC 664), Frog Jump (LC 403), Jump Game IV (LC 1345).
