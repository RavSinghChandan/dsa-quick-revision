# Q2 — 0/1 Knapsack

## 1. What is this question actually asking?

You have a knapsack with a weight limit W and n items, each with a weight and a value. You can either take an item (1) or leave it (0) — no fractions. Maximize the total value of items you take without exceeding the weight limit. Unlike fractional knapsack, you can't split items, which makes greedy fail and requires DP.

## 2. Pattern

2D Dynamic Programming — dp[i][w] = max value using first i items with weight limit w

## 3. Understand with a Diagram

```
Items:   weight=[1,3,4,5]  value=[1,4,5,7]  W=7  n=4

dp[i][w] = max value using items 0..i-1 with capacity w

Build table (rows=items 0..4, cols=capacity 0..7):

      w=0  1  2  3  4  5  6  7
i=0:   0   0  0  0  0  0  0  0   (no items)
i=1:   0   1  1  1  1  1  1  1   (item1: w=1,v=1)
i=2:   0   1  1  4  5  5  5  5   (item2: w=3,v=4)
i=3:   0   1  1  4  5  6  6  9   (item3: w=4,v=5)
i=4:   0   1  1  4  5  7  8  9   (item4: w=5,v=7)

Answer: dp[4][7] = 9

Recurrence:
  If w < item[i].weight:
    dp[i][w] = dp[i-1][w]  // can't take item i
  Else:
    dp[i][w] = max(dp[i-1][w],  // skip item i
                   dp[i-1][w - item[i].weight] + item[i].value)  // take item i

Trace dp[4][7] (item4: w=5, v=7):
  Can we take item4? 5 <= 7 YES
  Skip: dp[3][7] = 9
  Take: dp[3][7-5] + 7 = dp[3][2] + 7 = 1 + 7 = 8
  max(9, 8) = 9
  (Taking items 2 and 3: value=4+5=9 is better)

Which items are taken? (backtrack)
dp[4][7]=9: same as dp[3][7]=9 → item4 NOT taken
dp[3][7]=9: dp[2][7]=5, dp[2][3]+5=4+5=9 → take item3
dp[2][3]=4: dp[1][3]=1, dp[1][0]+4=0+4=4 → take item2
dp[1][0]=0: capacity=0, nothing → item1 NOT taken
Items taken: item2 (w=3,v=4) + item3 (w=4,v=5) = weight 7, value 9 ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — Try all 2^n subsets
**Idea:** For each subset of items, check if total weight ≤ W, track maximum value.

```
max_val = 0
for each subset S of items:
    if sum(weights in S) <= W:
        max_val = max(max_val, sum(values in S))
return max_val
```

O(2^n * n). Infeasible for n > 20.

### Optimal — 2D DP Table
**Idea:** dp[i][w] = max value considering first i items with weight capacity w. Build table iteratively.

```
dp = [[0]*(W+1) for _ in range(n+1)]

for i from 1 to n:
    for w from 0 to W:
        dp[i][w] = dp[i-1][w]  // don't take item i
        if weights[i-1] <= w:
            dp[i][w] = max(dp[i][w], dp[i-1][w-weights[i-1]] + values[i-1])

return dp[n][W]
```

O(n*W) time, O(n*W) space.

### Space Optimization — 1D DP Array
**Idea:** Only need the previous row. Use a single 1D array, iterate w from right to left.

```
dp = [0] * (W+1)

for i from 0 to n-1:
    for w from W down to weights[i]:
        dp[w] = max(dp[w], dp[w - weights[i]] + values[i])

return dp[W]
```

O(n*W) time, O(W) space. Right-to-left traversal prevents using same item twice.

## 5. Pseudocode (Optimal)

```
function knapsack01(weights[], values[], n, W):
    dp = array of size (W+1), initialized to 0
    
    for i from 0 to n-1:
        for w from W down to weights[i]:
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i])
    
    return dp[W]
```

## 6. Python Code

```python
def knapsack_01(weights, values, W):
    n = len(weights)
    dp = [0] * (W + 1)
    
    for i in range(n):
        # Traverse right to left to avoid using item twice
        for w in range(W, weights[i] - 1, -1):
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i])
    
    return dp[W]


def knapsack_01_2d(weights, values, W):
    """2D version for easier understanding and backtracking."""
    n = len(weights)
    dp = [[0] * (W + 1) for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        for w in range(W + 1):
            dp[i][w] = dp[i-1][w]  # don't take
            if weights[i-1] <= w:
                dp[i][w] = max(dp[i][w], dp[i-1][w - weights[i-1]] + values[i-1])
    
    return dp[n][W]


# Test 1 — from diagram
weights = [1, 3, 4, 5]
values  = [1, 4, 5, 7]
W = 7
print(knapsack_01(weights, values, W))       # 9
print(knapsack_01_2d(weights, values, W))    # 9

# Test 2 — standard example
weights2 = [2, 3, 4, 5]
values2  = [3, 4, 5, 6]
W2 = 5
print(knapsack_01(weights2, values2, W2))    # 7 (items w=2,v=3 + w=3,v=4)

# Test 3 — single item fits
print(knapsack_01([3], [10], 5))   # 10

# Test 4 — single item too heavy
print(knapsack_01([10], [100], 5))  # 0

# Test 5 — all items fit
weights3 = [1, 2, 3]
values3  = [6, 10, 12]
print(knapsack_01(weights3, values3, 6))   # 28 (all items: 1+2+3=6, value=6+10+12=28)
```

## 7. Complexity Table

| Approach      | Time    | Space   | Notes                            |
|---------------|---------|---------|----------------------------------|
| Brute Force   | O(2^n)  | O(n)    | Try all subsets                  |
| 2D DP         | O(n*W)  | O(n*W)  | Full table                       |
| 1D DP         | O(n*W)  | O(W)    | Space optimized                  |

## 8. Edge Cases to Remember

- W = 0 — return 0 (can't take anything)
- All items heavier than W — return 0
- Single item exactly at W — take it, return its value
- All items have same weight — take items with highest values
- Right-to-left in 1D — critical! Left-to-right would allow taking same item multiple times (becomes unbounded knapsack)
- Large W with large n — O(n*W) can be large; watch for time limit

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Subset Sum Problem                    | Knapsack where value=weight, target=W               | LC 416     |
| Partition Equal Subset Sum            | Can we split array into two equal subsets?          | LC 416     |
| Last Stone Weight II                  | Balance two groups (knapsack variant)               | LC 1049    |
| Target Sum                            | Count ways to assign +/- to reach target            | LC 494     |
| Coin Change                           | Unbounded knapsack (can reuse coins)                | LC 322     |
| Fractional Knapsack                   | Greedy works when fractions allowed                 | —          |
| Count Subsets with Sum K              | Count ways, not max value                           | —          |
| Perfect Squares                       | Unbounded knapsack with perfect square coins        | LC 279     |

## 10. The ONE Trick to Remember

**"Each item: loop capacity from W down to item.weight — right-to-left prevents using same item twice."**

The 1D DP array stores maximum values. For each item, updating from right to left means when we update dp[w], dp[w - weights[i]] still holds the value WITHOUT item i (from previous iteration). This correctly implements "use each item at most once." Left-to-right would mean dp[w - weights[i]] might already include item i, allowing multiple uses (unbounded knapsack).
