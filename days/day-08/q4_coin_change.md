# Q4 — Coin Change (LeetCode 322)

## 1. What is this question actually asking?

Given a list of coin denominations and a target amount, find the minimum number of coins needed to make up that amount. You can use each coin denomination any number of times (unlimited supply). If it's impossible to make the amount, return -1.

## 2. Pattern

Unbounded Knapsack DP — dp[amount] = min coins to make that amount; iterate coins for each amount (left to right, allowing reuse)

## 3. Understand with a Diagram

```
coins = [1, 2, 5]    amount = 11

dp[i] = min coins to make amount i
Initialize: dp[0]=0, dp[1..11]=infinity

For amount=1: try each coin:
  coin=1: dp[1] = min(inf, dp[0]+1) = 1
  coin=2: 2>1, skip
  coin=5: 5>1, skip
dp[1] = 1

For amount=2:
  coin=1: dp[2] = min(inf, dp[1]+1) = 2
  coin=2: dp[2] = min(2, dp[0]+1) = 1
  coin=5: skip
dp[2] = 1

For amount=5:
  coin=1: dp[5] = min(inf, dp[4]+1) = 3
  coin=2: dp[5] = min(3, dp[3]+1) = 3 (dp[3]=2, so 3)
  coin=5: dp[5] = min(3, dp[0]+1) = 1
dp[5] = 1

For amount=11:
  coin=1: dp[11] = min(inf, dp[10]+1)
  coin=2: dp[11] = min(..., dp[9]+1)
  coin=5: dp[11] = min(..., dp[6]+1)
  ...needs to compute dp[6..10] first

dp array built step by step:
amount: 0  1  2  3  4  5  6  7  8  9  10  11
coins:  0  1  1  2  2  1  2  2  3  3   2   3

dp[11] = 3 → use coins 5+5+1=11 (3 coins)

Visual decision at dp[11]:
  Best: 5 + dp[6] = 5 + (5+dp[1]) = 5+5+1 = 11 → 3 coins ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — Recursion without memoization
**Idea:** Try all combinations of coins.

```
function minCoins(amount, coins):
    if amount == 0: return 0
    if amount < 0: return -1
    result = infinity
    for coin in coins:
        sub = minCoins(amount - coin, coins)
        if sub != -1:
            result = min(result, sub + 1)
    return result if result != infinity else -1
```

O(S^n) where S=amount, n=number of coins. Exponential.

### Better — Memoization
```
memo = {}
function minCoins(amount):
    if amount in memo: return memo[amount]
    ...
```

O(S*n) time, O(S) space.

### Optimal — Bottom-Up DP
**Idea:** dp[0]=0. For each amount from 1 to S, try each coin. dp[i] = min(dp[i], dp[i-coin]+1) if i >= coin.

```
dp = [infinity] * (amount + 1)
dp[0] = 0

for i from 1 to amount:
    for coin in coins:
        if coin <= i and dp[i - coin] != infinity:
            dp[i] = min(dp[i], dp[i - coin] + 1)

return dp[amount] if dp[amount] != infinity else -1
```

O(S*n) time, O(S) space.

**Why left to right (unlike 0/1 knapsack)?**
Coins can be reused (unbounded). When we compute dp[i], dp[i-coin] may already include this same coin type — that's fine because we're allowed unlimited uses.

**Trace for coins=[1,2,5], amount=11:**
```
dp = [0,∞,∞,∞,∞,∞,∞,∞,∞,∞,∞,∞]
i=1: coin=1 → dp[1]=min(∞,dp[0]+1)=1
i=2: coin=1→dp[2]=2, coin=2→dp[2]=min(2,dp[0]+1)=1
i=3: coin=1→3, coin=2→dp[1]+1=2 → dp[3]=2
i=4: coin=1→3, coin=2→dp[2]+1=2 → dp[4]=2
i=5: coin=1→3, coin=2→3, coin=5→dp[0]+1=1 → dp[5]=1
i=6: coin=5→dp[1]+1=2 → dp[6]=2
i=7: coin=5→dp[2]+1=2 → dp[7]=2
i=8: coin=5→dp[3]+1=3 → dp[8]=3
i=9: coin=5→dp[4]+1=3 → dp[9]=3
i=10: coin=5→dp[5]+1=2 → dp[10]=2
i=11: coin=5→dp[6]+1=3 → dp[11]=3
Answer: 3 ✓
```

## 5. Pseudocode (Optimal)

```
function coinChange(coins[], amount):
    dp = [+inf] * (amount + 1)
    dp[0] = 0
    
    for i from 1 to amount:
        for coin in coins:
            if coin <= i and dp[i - coin] != +inf:
                dp[i] = min(dp[i], dp[i - coin] + 1)
    
    return dp[amount] if dp[amount] != +inf else -1
```

## 6. Python Code

```python
def coinChange(coins, amount):
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    
    for i in range(1, amount + 1):
        for coin in coins:
            if coin <= i and dp[i - coin] != float('inf'):
                dp[i] = min(dp[i], dp[i - coin] + 1)
    
    return dp[amount] if dp[amount] != float('inf') else -1


# Test 1 — LeetCode example 1
print(coinChange([1, 2, 5], 11))    # 3 (5+5+1)

# Test 2 — LeetCode example 2
print(coinChange([2], 3))           # -1 (impossible)

# Test 3 — amount = 0
print(coinChange([1, 2, 5], 0))     # 0

# Test 4 — single large coin
print(coinChange([5], 10))          # 2 (5+5)
print(coinChange([5], 3))           # -1

# Test 5 — all 1s
print(coinChange([1], 100))         # 100
```

## 7. Complexity Table

| Approach          | Time    | Space | Notes                         |
|-------------------|---------|-------|-------------------------------|
| Brute Recursion   | O(S^n)  | O(S)  | Exponential                   |
| Memoization       | O(S*n)  | O(S)  | Top-down DP                   |
| Bottom-up DP      | O(S*n)  | O(S)  | Optimal                       |

## 8. Edge Cases to Remember

- amount = 0 — return 0 (0 coins needed)
- No solution exists — all dp[amount] paths stay at infinity, return -1
- Coins larger than amount — those coins are never used
- Coin = 1 always in set — always a solution exists (just use 1s)
- Duplicate coins in array — doesn't affect correctness, just redundant computation
- Large amount — O(S*n) can be slow if S is very large (10^9 range would be problematic)

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Coin Change II (count ways)           | Count ways instead of min coins                     | LC 518     |
| Minimum Cost for Tickets              | DP with ticket duration constraints                 | LC 983     |
| Perfect Squares                       | Same DP: squares as "coins," minimize count         | LC 279     |
| Integer Break                         | DP: max product by breaking n                       | LC 343     |
| Combination Sum IV                    | Count ordered arrangements summing to target        | LC 377     |
| 0/1 Knapsack                          | Each coin used at most once (right-to-left)         | —          |
| Minimum Ribbon Cut                    | Same as coin change (cut lengths = coins)           | —          |
| Unbounded Knapsack                    | General form of coin change                         | —          |

## 10. The ONE Trick to Remember

**"dp[0]=0, dp[rest]=infinity; for each amount, try all coins; left-to-right allows reuse."**

This is unbounded knapsack (coins can be reused). Left-to-right traversal means dp[i-coin] may already include this coin type — that's intentional. Initialize dp[0]=0 (zero coins for zero amount) and dp[1..amount]=infinity (impossible until proven). Answer is dp[amount], return -1 if still infinity.
