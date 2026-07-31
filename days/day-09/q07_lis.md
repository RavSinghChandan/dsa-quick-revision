# Day 9 — Dynamic Programming · Q7 · Longest Increasing Subsequence

---

## What is this question actually asking?

Find the length of the **longest strictly increasing subsequence** (not necessarily contiguous).

```
[10,9,2,5,3,7,101,18] → 4   (e.g. [2,3,7,101] or [2,5,7,18])
```

> Two must-know solutions: the O(n²) DP, and the elegant **O(n log n) patience-sorting** with binary search.

---

## Pattern

```
PATTERN: DP (O(n²))  OR  Patience Sorting + Binary Search (O(n log n))
```

Whenever you see: *"longest increasing / chain / nesting subsequence"* → O(n²) DP where `dp[i]` = LIS ending at i; or maintain a `tails` array where `tails[k]` = smallest possible tail of an increasing subsequence of length k+1, updated via binary search.

---

## Understand with a diagram

```
O(n log n) patience:
   tails = []   ← tails[k] = smallest tail of an LIS of length k+1
   for x in nums:
       pos = first index in tails with tails[pos] >= x   (bisect_left)
       if pos == len(tails): tails.append(x)     ← extends LIS
       else: tails[pos] = x                      ← improves a tail
   answer = len(tails)

[10,9,2,5,3,7,101,18]
tails evolves: [10]→[9]→[2]→[2,5]→[2,3]→[2,3,7]→[2,3,7,101]→[2,3,7,18]
len = 4
```

`tails` is not the actual subsequence, but its length is the LIS length.

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all subsequences — O(2^n)
Check each for increasing order.

### 🟡 DP — O(n²)
`dp[i] = 1 + max(dp[j])` for `j < i, nums[j] < nums[i]`.

### 🟢 Optimal — patience + binary search — O(n log n)
Maintain `tails`; binary-search the insertion point.

---

## Pseudocode (O(n log n))

```
function lis(nums):
    tails = []
    for x in nums:
        pos = bisect_left(tails, x)      ← strictly increasing
        if pos == len(tails): tails.append(x)
        else: tails[pos] = x
    return len(tails)
```

---

## Python Code

```python
from bisect import bisect_left

def length_of_lis(nums: list[int]) -> int:
    tails = []
    for x in nums:
        pos = bisect_left(tails, x)      # first tail >= x (strict LIS)
        if pos == len(tails):
            tails.append(x)              # x extends the longest so far
        else:
            tails[pos] = x               # x gives a smaller tail
    return len(tails)


# O(n^2) DP version (also returns length)
def length_of_lis_dp(nums):
    if not nums: return 0
    dp = [1] * len(nums)
    for i in range(len(nums)):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    return max(dp)


# Test
print(length_of_lis([10,9,2,5,3,7,101,18]))   # 4
print(length_of_lis([0,1,0,3,2,3]))           # 4
print(length_of_lis_dp([7,7,7,7]))            # 1
```

---

## Complexity

| | Time | Space |
|---|---|---|
| DP | O(n²) | O(n) |
| Patience + BS | O(n log n) | O(n) |

---

## Edge Cases to remember

- **`bisect_left` for STRICTLY increasing**; use `bisect_right` for non-decreasing (allowing equals).
- **`tails` isn't the actual LIS** — only its length is correct; reconstruct with parent pointers if the sequence is needed.
- **Empty array** → 0.
- **All equal** → 1 (strict).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Longest Increasing Subsequence (LC 300) | This |
| 2 | Russian Doll Envelopes (LC 354) | Sort + LIS |
| 3 | Number of LIS (LC 673) | DP + count |
| 4 | Longest Chain of Pairs (LC 646) | Sort + LIS |
| 5 | Max Length of Nesting | LIS |
| 6 | Increasing Triplet (LC 334) | LIS length 3 |
| 7 | Longest Bitonic Subsequence | LIS both ways |
| 8 | Box stacking | Sort + LIS |

---

## The ONE trick to remember

```
"tails[k] = SMALLEST TAIL OF AN LIS OF LENGTH k+1; bisect_left EXTENDS OR IMPROVES."
```

Keep the array of best-possible tails. Each number either extends `tails` (new longest LIS) or replaces the first tail ≥ it (a smaller tail keeps future options open). The length of `tails` is the LIS length. O(n log n).

> Memory hook: "Keep the smallest possible tail for each length — patience-pile the cards."

---
