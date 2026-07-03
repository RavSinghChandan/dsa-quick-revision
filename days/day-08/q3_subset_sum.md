# Q3 — Subset Sum Problem

## 1. What is this question actually asking?

Given a set of non-negative integers and a target sum, determine if there exists a subset of the array whose elements sum exactly to the target. For example, [3,34,4,12,5,2] with target=9 → True (subset {4,5} sums to 9). This is a decision problem: yes or no.

## 2. Pattern

0/1 Knapsack DP — dp[w] = True if any subset of considered items sums to exactly w

## 3. Understand with a Diagram

```
arr = [3, 4, 5, 2]    target = 9

1D boolean dp array of size target+1:
dp[w] = True if some subset of arr sums to w
Initialize: dp[0] = True (empty subset sums to 0)

Process element 3:
  w from 9 down to 3:
    dp[3] = dp[3] or dp[3-3] = dp[0] = True
  dp: [T, F, F, T, F, F, F, F, F, F]

Process element 4:
  w from 9 down to 4:
    dp[7] = dp[7] or dp[3] = True
    dp[4] = dp[4] or dp[0] = True
  dp: [T, F, F, T, T, F, F, T, F, F]

Process element 5:
  w from 9 down to 5:
    dp[9] = dp[9] or dp[4] = True  ← FOUND!
    dp[8] = dp[8] or dp[3] = True
    dp[5] = dp[5] or dp[0] = True
  dp: [T, F, F, T, T, T, F, T, T, T]

Process element 2:
  (already found dp[9]=True, can skip but complete for correctness)
  dp: [T, F, T, T, T, T, T, T, T, T]

return dp[9] = True ✓

Subset that achieves it: {4, 5} → 4+5=9
```

## 4. Brute Force → Better → Optimal

### Brute Force — Try all 2^n subsets
**Idea:** Generate every subset, check if sum equals target.

```
for each subset S:
    if sum(S) == target: return True
return False
```

O(2^n). Too slow.

### Optimal — 1D Boolean DP
**Idea:** dp[w] = True if any subset of seen elements sums to w. Update from right to left for each element.

```
dp = [False] * (target + 1)
dp[0] = True  // empty subset

for num in arr:
    for w in range(target, num - 1, -1):
        dp[w] = dp[w] or dp[w - num]

return dp[target]
```

O(n * target) time, O(target) space.

**Why right to left?** Same as 0/1 knapsack — prevents using same element twice.

**Trace:**
Already done in diagram above.

## 5. Pseudocode (Optimal)

```
function subsetSum(arr[], target):
    dp = boolean array of size (target+1), all False
    dp[0] = True
    
    for num in arr:
        for w from target down to num:
            dp[w] = dp[w] or dp[w - num]
    
    return dp[target]
```

## 6. Python Code

```python
def subset_sum(arr, target):
    dp = [False] * (target + 1)
    dp[0] = True
    
    for num in arr:
        for w in range(target, num - 1, -1):
            if dp[w - num]:
                dp[w] = True
    
    return dp[target]


def count_subsets_with_sum(arr, target):
    """Count number of subsets summing to target."""
    dp = [0] * (target + 1)
    dp[0] = 1  // one way to get sum 0 (empty subset)
    
    for num in arr:
        for w in range(target, num - 1, -1):
            dp[w] += dp[w - num]
    
    return dp[target]


# Test 1 — standard example
print(subset_sum([3, 4, 5, 2], 9))      # True  (4+5=9)
print(subset_sum([3, 34, 4, 12, 5, 2], 9))   # True (4+5=9)

# Test 2 — no subset
print(subset_sum([3, 34, 4, 12, 5, 2], 30))  # False

# Test 3 — target = 0
print(subset_sum([1, 2, 3], 0))          # True (empty subset)

# Test 4 — single element
print(subset_sum([5], 5))                # True
print(subset_sum([5], 3))                # False

# Test 5 — count subsets
print(count_subsets_with_sum([1, 1, 2, 3], 4))  # 3 (1+3, 1+3, 1+1+2)
```

## 7. Complexity Table

| Approach      | Time          | Space     | Notes                         |
|---------------|---------------|-----------|-------------------------------|
| Brute Force   | O(2^n)        | O(n)      | All subsets                   |
| 2D DP         | O(n * target) | O(n*target)| Full table                   |
| 1D DP         | O(n * target) | O(target) | Space optimized               |

## 8. Edge Cases to Remember

- target = 0 — always True (empty subset sums to 0)
- Single element equals target — True
- All elements larger than target — False (none can contribute)
- Duplicate elements — handled correctly by right-to-left
- Target larger than sum of all elements — False
- Negative numbers — problem usually specifies non-negative; if negatives allowed, DP range needs adjustment

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Partition Equal Subset Sum            | Can we partition into two equal sum subsets?        | LC 416     |
| 0/1 Knapsack                          | Generalization with values                          | —          |
| Target Sum                            | Count ways with +/- signs to reach target           | LC 494     |
| Count Subsets with Given Difference   | Subset sum with difference condition                | —          |
| Last Stone Weight II                  | Minimize |S1 - S2|                                 | LC 1049    |
| Combination Sum IV                    | Count ordered combos (unbounded, order matters)     | LC 377     |
| Coin Change II                        | Count ways to make change (unbounded)               | LC 518     |
| Perfect Squares                       | Min number of perfect squares summing to n          | LC 279     |

## 10. The ONE Trick to Remember

**"Boolean dp[0]=True; for each number, right-to-left update: dp[w] |= dp[w-num]."**

dp[0]=True seeds the DP (empty subset). For each number, we ask: "using this number, can we reach sum w?" Only if we could reach w-num before. Right-to-left ensures each number is used at most once. If you need counts instead of boolean, use dp[w] += dp[w-num] with dp[0]=1.
