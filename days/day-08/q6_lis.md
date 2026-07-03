# Q6 — Longest Increasing Subsequence (LeetCode 300)

## 1. What is this question actually asking?

Given an array of integers, find the length of the longest subsequence where each element is strictly greater than the previous one. A subsequence doesn't need to be consecutive. For example, in [10,9,2,5,3,7,101,18], the LIS is [2,3,7,101] or [2,5,7,101] — both have length 4.

## 2. Pattern

O(n log n) — patience sorting: maintain a "piles" array where we binary search to place each element, keeping the array length = LIS length

## 3. Understand with a Diagram

```
nums = [10, 9, 2, 5, 3, 7, 101, 18]

O(n²) DP approach:
dp[i] = LIS ending at index i

i=0: dp[0]=1 ([10])
i=1: dp[1]=1 ([9])
i=2: dp[2]=1 ([2])
i=3: nums[3]=5 > nums[2]=2 → dp[3]=dp[2]+1=2 ([2,5])
i=4: nums[4]=3 > nums[2]=2 → dp[4]=dp[2]+1=2 ([2,3])
i=5: nums[5]=7 > nums[3]=5 → dp[5]=dp[3]+1=3 ([2,5,7])
     also 7>3: dp[4]+1=3, same
i=6: nums[6]=101 > nums[5]=7 → dp[6]=dp[5]+1=4 ([2,5,7,101])
i=7: nums[7]=18 > nums[5]=7 → dp[7]=dp[5]+1=4, ([2,5,7,18])

dp = [1,1,1,2,2,3,4,4]
Answer: max(dp) = 4

O(n log n) — Patience Sorting:
Maintain array 'tails' where tails[i] = smallest tail of all IS of length i+1.

nums = [10, 9, 2, 5, 3, 7, 101, 18]

Process 10: tails=[], 10 >= all → append. tails=[10]
Process 9:  9 < 10 → replace tails[0]=9. tails=[9]
Process 2:  2 < 9 → replace tails[0]=2. tails=[2]
Process 5:  5 > 2 → append. tails=[2,5]
Process 3:  3 > 2, 3 < 5 → replace tails[1]=3. tails=[2,3]
Process 7:  7 > 3 → append. tails=[2,3,7]
Process 101: 101 > 7 → append. tails=[2,3,7,101]
Process 18: 18 > 7, 18 < 101 → replace tails[3]=18. tails=[2,3,7,18]

len(tails) = 4 = LIS length ✓

Note: tails is NOT the actual LIS! It just tracks the length.
Actual LIS is [2,3,7,101] or [2,5,7,101] etc.
```

## 4. Brute Force → Better → Optimal

### Brute Force — All subsequences
**Idea:** Generate all increasing subsequences, return the longest.

O(2^n). Too slow.

### Better — O(n²) DP
**Idea:** dp[i] = LIS ending at index i. For each i, look at all j < i where nums[j] < nums[i].

```
dp = [1] * n
for i from 1 to n-1:
    for j from 0 to i-1:
        if nums[j] < nums[i]:
            dp[i] = max(dp[i], dp[j] + 1)
return max(dp)
```

O(n²) time, O(n) space.

### Optimal — O(n log n) Patience Sorting
**Idea:** Maintain a `tails` array. For each number, binary search for the first element in `tails` >= current number, replace it. If no such element, append. `len(tails)` = LIS length.

```
import bisect

tails = []
for num in nums:
    pos = bisect_left(tails, num)  // find insertion point
    if pos == len(tails):
        tails.append(num)           // extend
    else:
        tails[pos] = num            // replace (keep smaller tail)
return len(tails)
```

O(n log n) time, O(n) space.

## 5. Pseudocode (Optimal)

```
function lengthOfLIS(nums[]):
    tails = []
    
    for num in nums:
        lo = 0, hi = len(tails)
        
        // Binary search for first tail >= num (strictly increasing)
        while lo < hi:
            mid = (lo + hi) // 2
            if tails[mid] < num: lo = mid + 1
            else: hi = mid
        
        if lo == len(tails): tails.append(num)
        else: tails[lo] = num
    
    return len(tails)
```

## 6. Python Code

```python
from bisect import bisect_left


def lengthOfLIS(nums):
    """O(n log n) patience sorting."""
    tails = []
    
    for num in nums:
        pos = bisect_left(tails, num)  # find first >= num
        if pos == len(tails):
            tails.append(num)   # new longest IS
        else:
            tails[pos] = num    # replace to keep smallest tail
    
    return len(tails)


def lengthOfLIS_dp(nums):
    """O(n²) DP — easier to understand."""
    n = len(nums)
    dp = [1] * n
    
    for i in range(1, n):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    
    return max(dp)


# Test 1 — LeetCode example 1
print(lengthOfLIS([10,9,2,5,3,7,101,18]))   # 4

# Test 2 — LeetCode example 2
print(lengthOfLIS([0,1,0,3,2,3]))           # 4

# Test 3 — decreasing (LIS = 1)
print(lengthOfLIS([7,7,7,7,7]))             # 1 (strictly increasing, all same → LIS=1)

# Test 4 — single element
print(lengthOfLIS([1]))                     # 1

# Test 5 — already sorted
print(lengthOfLIS([1,2,3,4,5]))            # 5

# Verify DP gives same answers
print(lengthOfLIS_dp([10,9,2,5,3,7,101,18]))  # 4
```

## 7. Complexity Table

| Approach          | Time       | Space | Notes                          |
|-------------------|------------|-------|--------------------------------|
| Brute Force       | O(2^n)     | O(n)  | All subsequences               |
| O(n²) DP          | O(n²)      | O(n)  | Simple, interview acceptable   |
| Patience Sorting  | O(n log n) | O(n)  | Optimal                        |

## 8. Edge Cases to Remember

- All equal elements — LIS = 1 (strictly increasing, equal not allowed)
- Strictly decreasing — LIS = 1 (each element alone)
- Single element — return 1
- Already sorted ascending — return n
- Duplicates — bisect_left places duplicate to the left, so duplicate doesn't extend LIS
- Non-strictly increasing (if problem allows ≤) — use bisect_right instead

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Russian Doll Envelopes               | Sort by width, LIS on height                        | LC 354     |
| Longest Bitonic Subsequence           | LIS from left + LIS from right                      | GFG        |
| Number of Longest IS                  | Count LIS using extra dp array                      | LC 673     |
| Longest Increasing Subsequence        | This exact problem                                  | LC 300     |
| Delete Columns to Make Sorted III     | 2D LIS variant                                      | LC 960     |
| Largest Divisible Subset              | LIS variant with divisibility                       | LC 368     |
| Increasing Triplet Subsequence        | Check if LIS ≥ 3 (O(n) two-variable greedy)         | LC 334     |
| Wiggle Subsequence                    | Alternating increase/decrease                       | LC 376     |

## 10. The ONE Trick to Remember

**"Patience sorting: binary search for the first tail >= num; replace it or append — tails length = LIS length."**

The tails array maintains invariant: tails[i] is the smallest possible tail of any increasing subsequence of length i+1. Replacing tails[pos] = num doesn't give us the actual LIS but maintains the correct length. The tails array length always equals the LIS length. Use bisect_left for strictly increasing (bisect_right for non-decreasing).
