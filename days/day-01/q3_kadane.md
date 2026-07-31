# Day 1 — Arrays · Q3 · Kadane's Algorithm (Maximum Subarray Sum)

---

## What is this question actually asking?

Find the **largest sum of any contiguous subarray**.

```
[-2, 1, -3, 4, -1, 2, 1, -5, 4] → 6   (subarray [4,-1,2,1])
```

> The most famous DP-in-disguise question. Tests the "extend or restart" insight.

---

## Pattern

```
PATTERN: Kadane — running sum, reset when it goes negative
```

Whenever you see: *"maximum sum contiguous subarray"* → keep a running sum; if it ever drops below 0, it can only hurt what follows, so **reset to 0** (or start fresh at the current element). Track the best seen.

---

## Understand with a diagram

```
[-2, 1, -3, 4, -1, 2, 1, -5, 4]
cur:  -2→ reset0 ... at 4 cur=4, best=4
      4,-1→3, +2→5, +1→6 (best=6), -5→1, +4→5
best = 6   (from [4,-1,2,1])

Rule: cur = max(x, cur + x)   ← restart at x, or extend
      best = max(best, cur)
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all subarrays — O(n²)
Two loops summing every `[i..j]`.

### 🟢 Optimal — Kadane — O(n), O(1)
One pass: `cur = max(x, cur + x)` decides extend-vs-restart; `best` tracks the max.

---

## Pseudocode (Optimal)

```
function max_subarray(nums):
    best = cur = nums[0]
    for x in nums[1:]:
        cur = max(x, cur + x)      ← restart at x or extend
        best = max(best, cur)
    return best
```

---

## Python Code

```python
def max_subarray(nums: list[int]) -> int:
    best = cur = nums[0]
    for x in nums[1:]:
        cur = max(x, cur + x)      # extend or restart
        best = max(best, cur)
    return best


# Test
print(max_subarray([-2,1,-3,4,-1,2,1,-5,4]))   # 6
print(max_subarray([-3,-1,-2]))                # -1  (all-negative case)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(1) |
| Kadane | O(n) | O(1) |

---

## Edge Cases to remember

- **All negatives** → answer is the least-negative element; initializing `best = nums[0]` (not 0) handles it.
- **Return the subarray itself** → track start/end indices when `cur` restarts and when `best` updates.
- **Empty array** → define behavior (usually not allowed).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Maximum Product Subarray (Q6) | Track min & max |
| 2 | Maximum Circular Subarray Sum | Kadane + total-min |
| 3 | Max sum with at most one deletion | Two Kadane states |
| 4 | Best time to buy/sell stock I | Running min |
| 5 | Maximum sum rectangle 2D | Kadane on columns |
| 6 | Longest turbulent subarray | Running state |
| 7 | House Robber (Day 9) | Extend/skip DP |
| 8 | Max subarray after modification | Kadane variants |

---

## The ONE trick to remember

```
"cur = max(x, cur + x). Negative running sum? Restart."
```

A negative prefix can never help a later subarray — so drop it. Track the best running sum. Initialize `best` to the first element so all-negative arrays work.

> Memory hook: "Carry the streak forward, but never carry a debt."

---
