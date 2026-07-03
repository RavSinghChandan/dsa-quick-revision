# Q4 — Coin Change (2-min card) — LC 322

**Minimum coins to make amount (unlimited coin uses). Return -1 if impossible.**

**Pattern:** Unbounded Knapsack DP — dp[0]=0, dp[rest]=inf; LEFT-TO-RIGHT (reuse allowed)

```
coins=[1,2,5]  amount=11

dp=[0,∞,∞,∞,∞,∞,∞,∞,∞,∞,∞,∞]
i=1: coin1→dp[1]=1
i=2: coin2→dp[2]=1
i=5: coin5→dp[5]=1
i=6: coin5→dp[1]+1=2
i=10: coin5→dp[5]+1=2
i=11: coin5→dp[6]+1=3

dp[11]=3 → coins: 5+5+1 ✓
```

**The trick:** "Left-to-right update (unlike 0/1 knapsack) — allows same coin to be reused."

```python
dp = [float('inf')]*(amount+1); dp[0]=0
for i in range(1, amount+1):
    for coin in coins:
        if coin<=i and dp[i-coin]!=float('inf'):
            dp[i] = min(dp[i], dp[i-coin]+1)
return dp[amount] if dp[amount]!=float('inf') else -1
```

**Complexity:** Time O(S*n) | Space O(S)  where S=amount

**Same pattern solves:** Coin Change II - count ways (LC 518), Perfect Squares (LC 279), Min Cost Tickets (LC 983), Combination Sum IV (LC 377), Integer Break (LC 343), 0/1 Knapsack (right-to-left variant), Unbounded Knapsack, Ribbon Cut.
