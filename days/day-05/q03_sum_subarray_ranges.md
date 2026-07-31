# Day 5 — Stack, Queue & Greedy · Q3 · Sum of Subarray Ranges

---

## What is this question actually asking?

For every subarray, its **range** = (max − min). Sum the ranges over **all** subarrays.

```
[1,2,3] → subarrays: [1]=0,[2]=0,[3]=0,[1,2]=1,[2,3]=1,[1,2,3]=2 → sum = 4
```

> `sum(range) = sum(max) − sum(min)` over all subarrays. Each part is a classic **monotonic-stack contribution** problem — count how many subarrays each element is the max/min of.

---

## Pattern

```
PATTERN: Monotonic stack "contribution" — Σmax − Σmin over all subarrays
```

Whenever you see: *"sum of max/min over all subarrays"* → for each element compute how many subarrays it is the max (or min) of, using previous/next greater-or-smaller boundaries via a monotonic stack. Contribution = `value × leftCount × rightCount`.

---

## Understand with a diagram

```
Element nums[i] is the MIN of subarrays spanning (prevSmaller, i] × [i, nextSmaller):
   left  = i - prevSmallerIndex
   right = nextSmallerIndex - i
   count = left * right
Σmin = Σ nums[i] * left * right   (over all i)

Σmax similarly with prev/next GREATER boundaries.
answer = Σmax − Σmin.
```

Careful strict/non-strict boundaries avoid double counting equal values.

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — every subarray — O(n²)
Track running max/min per start; sum ranges. O(n²) — fine for small n, and a valid interview answer.

### 🟢 Optimal — monotonic stack contribution — O(n)
Compute `Σmax` and `Σmin` each in O(n) with previous/next boundary stacks. Subtract.

---

## Pseudocode (Optimal)

```
function subarray_ranges_sum(nums):
    return sum_of_maxes(nums) - sum_of_mins(nums)

function sum_of_mins(nums):        # each element as the MIN
    prevSmaller[i], nextSmaller[i] via monotonic stacks
    total = 0
    for i:
        left  = i - prevSmaller[i]
        right = nextSmaller[i] - i
        total += nums[i] * left * right
    return total
# sum_of_maxes: same with greater boundaries
```

---

## Python Code (clean O(n²), plus O(n) sketch)

```python
def sum_subarray_ranges_bruteforce(nums: list[int]) -> int:
    total = 0
    n = len(nums)
    for i in range(n):
        mn = mx = nums[i]
        for j in range(i, n):
            mn = min(mn, nums[j])
            mx = max(mx, nums[j])
            total += mx - mn
    return total


def sum_subarray_ranges(nums: list[int]) -> int:
    # O(n): Σmax − Σmin via monotonic-stack contributions
    def sum_of(extreme_is_min: bool) -> int:
        n = len(nums)
        total = 0
        stack = []  # indices, monotonic
        for i in range(n + 1):
            # sentinel at the end forces flush
            while stack and (i == n or
                             (nums[stack[-1]] > nums[i] if extreme_is_min
                              else nums[stack[-1]] < nums[i])):
                mid = stack.pop()
                left = mid - (stack[-1] if stack else -1)
                right = i - mid
                total += nums[mid] * left * right
            stack.append(i)
        return total
    return sum_of(False) - sum_of(True)   # Σmax − Σmin


# Test
print(sum_subarray_ranges_bruteforce([1,2,3]))   # 4
print(sum_subarray_ranges([1,2,3]))              # 4
print(sum_subarray_ranges([1,3,3]))              # 4
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(1) |
| Monotonic stack | O(n) | O(n) |

---

## Edge Cases to remember

- **Σmax − Σmin decomposition** — the key insight; range of a subarray is max − min, so linearity splits the total.
- **Strict vs non-strict boundaries** — for equal values, use `>` on one side and `>=` (or the sentinel/index tie-break) on the other to count each subarray exactly once.
- **Sentinel flush** — iterating to `n` with a forced pop empties the stack cleanly.
- **Overflow** — large arrays → big sums (Python safe; use long elsewhere).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Sum of Subarray Minimums (LC 907) | Just Σmin |
| 2 | Sum of Subarray Ranges (LC 2104) | This problem |
| 3 | Largest Rectangle Histogram (Q5) | Prev/next smaller |
| 4 | Maximal Rectangle (LC 85) | Histogram per row |
| 5 | Number of subarrays where X is max | Contribution count |
| 6 | Trapping Rain Water (Q4) | Boundaries |
| 7 | Subarray with bounded max (LC 795) | atMost trick |
| 8 | Total strength of wizards (LC 2281) | Contribution + prefix |

---

## The ONE trick to remember

```
"Σrange = Σmax − Σmin; EACH ELEMENT'S CONTRIBUTION = value × leftCount × rightCount."
```

Split range into max minus min. For each element, previous/next smaller (or greater) boundaries give how many subarrays it dominates; multiply by the value. Monotonic stacks compute both in O(n).

> Memory hook: "Count how many subarrays each value rules as max, and as min — then subtract."

---
