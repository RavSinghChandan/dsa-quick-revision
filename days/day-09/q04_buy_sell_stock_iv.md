# Day 9 — Dynamic Programming · Q4 · Best Time to Buy and Sell Stock IV

---

## What is this question actually asking?

Maximize profit with **at most K transactions** (a transaction = one buy then one sell; you must sell before buying again).

```
prices=[3,2,6,5,0,3], K=2 → 7   (buy 2 sell 6 = 4, buy 0 sell 3 = 3)
```

> The general stock DP. State = (day, transactions used, holding or not). All the stock problems (I–IV, cooldown, fee) are this template with K fixed.

---

## Pattern

```
PATTERN: DP over (transaction count, holding state)
```

Whenever you see: *"max profit with ≤ K transactions"* → track two values per transaction: `buy[t]` (best profit if currently holding after ≤t buys) and `sell[t]` (best after ≤t completed sells). Update across days.

---

## Understand with a diagram

```
For each price p, for t = 1..K:
    buy[t]  = max(buy[t],  sell[t-1] - p)   ← buy now (uses a transaction slot)
    sell[t] = max(sell[t], buy[t]    + p)   ← sell now (completes transaction t)

buy[t]  = best profit while holding, within t transactions
sell[t] = best profit not holding, within t transactions
answer = sell[K]
```

If K ≥ n//2, it's unlimited transactions → just sum all upward moves.

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all transaction splits — exponential
Try every combination of buy/sell days.

### 🟢 Optimal — DP over K states — O(n·K)
Roll `buy[]`/`sell[]` arrays across days. Special-case unlimited when K is large.

---

## Pseudocode (Optimal)

```
function max_profit(K, prices):
    if K >= n//2: return sum of all positive consecutive diffs   ← unlimited
    buy  = [-inf]*(K+1)
    sell = [0]*(K+1)
    for p in prices:
        for t in 1..K:
            buy[t]  = max(buy[t],  sell[t-1] - p)
            sell[t] = max(sell[t], buy[t]    + p)
    return sell[K]
```

---

## Python Code

```python
def max_profit_k(K: int, prices: list[int]) -> int:
    n = len(prices)
    if n == 0 or K == 0:
        return 0
    if K >= n // 2:                          # unlimited transactions
        return sum(max(0, prices[i] - prices[i - 1]) for i in range(1, n))

    buy = [float('-inf')] * (K + 1)
    sell = [0] * (K + 1)
    for p in prices:
        for t in range(1, K + 1):
            buy[t] = max(buy[t], sell[t - 1] - p)   # buy (start txn t)
            sell[t] = max(sell[t], buy[t] + p)      # sell (finish txn t)
    return sell[K]


# Test
print(max_profit_k(2, [3,2,6,5,0,3]))       # 7
print(max_profit_k(2, [2,4,1]))             # 2
print(max_profit_k(2, [1,2,3,4,5]))         # 4  (unlimited-ish)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| DP | O(n·K) | O(K) |

---

## Edge Cases to remember

- **K ≥ n//2 → unlimited** — otherwise K is a real constraint; the unlimited shortcut avoids a huge K.
- **`buy[t]` init to −∞** — you can't be holding with 0 profit before any buy.
- **Update `buy[t]` before `sell[t]`** within a day so a same-day buy-sell isn't double counted incorrectly (this ordering is the standard "buy then sell same day" convention that still yields correct max).
- **Cooldown / fee variants** — add a state or subtract fee on sell.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Buy/Sell Stock I (LC 121) | K=1 |
| 2 | Buy/Sell Stock II (LC 122) | Unlimited |
| 3 | Buy/Sell Stock III (LC 123) | K=2 |
| 4 | With Cooldown (LC 309) | Extra state |
| 5 | With Transaction Fee (LC 714) | Fee on sell |
| 6 | Stock IV (LC 188) | This problem |
| 7 | Job scheduling profit | DP states |
| 8 | Interval profit selection | DP |

---

## The ONE trick to remember

```
"TRACK buy[t] & sell[t] PER TRANSACTION; buy[t]=max(buy[t], sell[t-1]-p); sell[t]=max(sell[t], buy[t]+p)."
```

Two rolling arrays over K transactions capture "holding" vs "sold" profit. A buy for transaction t builds on the profit after t−1 sells; a sell completes transaction t. Answer is `sell[K]`. Shortcut to unlimited when K is large.

> Memory hook: "For each transaction slot, best-to-hold and best-to-have-sold, rolled across days."

---
