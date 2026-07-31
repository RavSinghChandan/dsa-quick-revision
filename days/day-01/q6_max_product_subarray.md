# Day 1 — Arrays · Q6 · Maximum Product Subarray

---

## What is this question actually asking?

Find the **largest product** of any contiguous subarray.

```
[2, 3, -2, 4] → 6   (subarray [2,3])
[-2, 0, -1]   → 0
[-2, 3, -4]   → 24  (whole array: -2*3*-4)
```

> Kadane's tricky cousin. The twist: **negatives** — two negatives make a positive, so the *minimum* product matters too.

---

## Pattern

```
PATTERN: Kadane variant — track BOTH running max and running min
```

Whenever you see: *"max product subarray"* → a big negative × current min can become the new max. So carry both `max_so_far` and `min_so_far`, and swap them when you hit a negative.

---

## Understand with a diagram

```
[-2, 3, -4]
x=-2: max=-2, min=-2
x=3 : candidates {3, 3*-2=-6, 3*-2=-6} → max=3, min=-6
x=-4: negative! big value comes from min*x:
      candidates {-4, 3*-4=-12, -6*-4=24} → max=24, min=-12
answer = 24

Rule: at a negative x, max and min flip roles → track both.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all subarrays — O(n²)
Multiply every `[i..j]`.

### 🟢 Optimal — dual Kadane — O(n), O(1)
Track `cur_max` and `cur_min`. At each x compute both from `{x, cur_max*x, cur_min*x}`. Update global best.

---

## Pseudocode (Optimal)

```
function max_product(nums):
    best = cur_max = cur_min = nums[0]
    for x in nums[1:]:
        if x < 0: swap(cur_max, cur_min)      ← negative flips roles
        cur_max = max(x, cur_max * x)
        cur_min = min(x, cur_min * x)
        best = max(best, cur_max)
    return best
```

---

## Python Code

```python
def max_product(nums: list[int]) -> int:
    best = cur_max = cur_min = nums[0]
    for x in nums[1:]:
        if x < 0:
            cur_max, cur_min = cur_min, cur_max   # negative flips max/min
        cur_max = max(x, cur_max * x)
        cur_min = min(x, cur_min * x)
        best = max(best, cur_max)
    return best


# Test
print(max_product([2, 3, -2, 4]))   # 6
print(max_product([-2, 3, -4]))     # 24
print(max_product([-2, 0, -1]))     # 0
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(1) |
| Dual Kadane | O(n) | O(1) |

---

## Edge Cases to remember

- **Negatives flip max/min** — the core trick; a negative times the running *min* can be the new *max*.
- **Zeros reset** — `max(x, ...)` restarts naturally at the element after a 0.
- **Single element** → return it.
- **Init to nums[0]**, not 1 or 0, so all-negative / single-value inputs work.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Maximum Subarray Sum / Kadane (Q3) | Additive version |
| 2 | Maximum Circular Subarray | Kadane variants |
| 3 | Product of Array Except Self | Prefix/suffix products |
| 4 | Subarray Product Less Than K | Sliding window |
| 5 | Max sum after one deletion | Dual state |
| 6 | Min/Max path with sign flips | Track both extremes |
| 7 | Largest range product | Same |
| 8 | Stock with cooldown DP | Multiple states |

---

## The ONE trick to remember

```
"TRACK MAX AND MIN; A NEGATIVE FLIPS THEIR ROLES."
```

Because a negative number can turn the smallest product into the largest, carry both `cur_max` and `cur_min`, and swap them whenever the current element is negative.

> Memory hook: "The biggest can hide inside the smallest — keep both, and flip on a minus."

---
