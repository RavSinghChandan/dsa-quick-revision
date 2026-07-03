# Q2 — 0/1 Knapsack (2-min card)

**Maximize value of items taken (0 or 1 each) without exceeding weight limit W.**

**Pattern:** 2D DP — dp[i][w] = max value using first i items with capacity w; or 1D with right-to-left

```
weights=[1,3,4,5]  values=[1,4,5,7]  W=7

1D dp (initially all 0):
Item1(w=1,v=1): dp[7..1] updated → dp=[0,1,1,1,1,1,1,1]
Item2(w=3,v=4): dp=[0,1,1,4,5,5,5,5]
Item3(w=4,v=5): dp=[0,1,1,4,5,6,6,9]
Item4(w=5,v=7): dp=[0,1,1,4,5,7,8,9]

dp[7] = 9 ✓  (items 2+3: w=3+4=7, v=4+5=9)
```

**The trick:** "Loop capacity RIGHT TO LEFT — prevents using same item twice (unlike unbounded knapsack)."

```python
dp = [0]*(W+1)
for i in range(n):
    for w in range(W, weights[i]-1, -1):  # right to left!
        dp[w] = max(dp[w], dp[w-weights[i]] + values[i])
return dp[W]
```

**Complexity:** Time O(n*W) | Space O(W)

**Same pattern solves:** Subset Sum (LC 416), Partition Equal Subset (LC 416), Last Stone II (LC 1049), Target Sum (LC 494), Count Subsets Sum K, Coin Change unbounded (LC 322), Perfect Squares (LC 279).
