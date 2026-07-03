# Q3 — Subset Sum Problem (2-min card)

**Does any subset of the array sum to the target?**

**Pattern:** 0/1 Knapsack DP — boolean dp[w] = can we make sum w; right-to-left update

```
arr=[3,4,5,2]  target=9

dp=[T,F,F,F,F,F,F,F,F,F]  (dp[0]=True)

num=3: dp[3]=dp[0]=T → dp=[T,F,F,T,...]
num=4: dp[4]=dp[0]=T, dp[7]=dp[3]=T → dp=[T,F,F,T,T,F,F,T,F,F]
num=5: dp[9]=dp[4]=T ← FOUND! dp[8]=dp[3]=T, dp[5]=dp[0]=T

dp[9]=True ✓ (subset {4,5}=9)
```

**The trick:** "dp[0]=True; for each num, right-to-left: dp[w] |= dp[w-num]."

```python
dp = [False]*(target+1); dp[0] = True
for num in arr:
    for w in range(target, num-1, -1):
        dp[w] = dp[w] or dp[w-num]
return dp[target]
```

**Complexity:** Time O(n * target) | Space O(target)

**Same pattern solves:** Partition Equal Subset (LC 416), 0/1 Knapsack, Target Sum (LC 494), Last Stone II (LC 1049), Count Subsets Diff, Coin Change II (LC 518), Combination Sum IV (LC 377), Perfect Squares (LC 279).
