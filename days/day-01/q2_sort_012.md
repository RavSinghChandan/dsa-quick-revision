# Day 1 — Arrays · Q2 · Sort an Array of 0's, 1's and 2's

---

## What is this question actually asking?

Sort an array containing only `0`, `1`, `2` — **in one pass, in place**, without a sorting library.

```
[2,0,2,1,1,0] → [0,0,1,1,2,2]
```

> The **Dutch National Flag** problem. Tests the three-pointer partition trick — sort three groups in O(n), O(1).

---

## Pattern

```
PATTERN: Dutch National Flag — three pointers (low, mid, high)
```

Whenever you see: *"array of only 3 distinct values, sort in one pass"* → keep `low` (boundary of 0s), `high` (boundary of 2s), and `mid` scanning. Swap based on what `mid` sees.

---

## Understand with a diagram

```
[0 .. low-1] = 0s   [low .. mid-1] = 1s   [mid .. high] = unknown   [high+1 ..] = 2s

mid sees 0 → swap(low, mid), low++, mid++
mid sees 1 → mid++            (already in place)
mid sees 2 → swap(mid, high), high--   (don't mid++, recheck swapped value)
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — sort() — O(n log n)
`nums.sort()`. Works but ignores that there are only 3 values.

### 🟡 Better — counting sort — two passes O(n)
Count 0s/1s/2s, then overwrite. O(n) but two passes.

### 🟢 Optimal — Dutch Flag — one pass O(n), O(1)
Three pointers partition the array in a single sweep.

---

## Pseudocode (Optimal)

```
function sort012(nums):
    low = mid = 0
    high = n - 1
    while mid <= high:
        if nums[mid] == 0: swap(nums[low], nums[mid]); low++; mid++
        elif nums[mid] == 1: mid++
        else: swap(nums[mid], nums[high]); high--    ← do NOT advance mid
```

---

## Python Code

```python
def sort_012(nums: list[int]) -> None:
    low = mid = 0
    high = len(nums) - 1
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:  # nums[mid] == 2
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1                       # recheck the swapped-in value


# Test
a = [2, 0, 2, 1, 1, 0]
sort_012(a)
print(a)   # [0, 0, 1, 1, 2, 2]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| sort() | O(n log n) | O(1) |
| Dutch Flag | O(n) | O(1) |

---

## Edge Cases to remember

- **Don't advance `mid` after swapping with `high`** — the value pulled from `high` is unchecked.
- **`mid <= high`** loop condition (not `<`) — the `high` position must be examined.
- **All same value / already sorted** → still O(n).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Sort Colors (LC 75) | Identical |
| 2 | Move Zeroes (Day 12 Q3) | Partition variant |
| 3 | Partition array around pivot | Three-way partition |
| 4 | Quicksort 3-way (Dutch) partition | Same core |
| 5 | Segregate even/odd | Two pointer |
| 6 | Wiggle sort | Partition idea |
| 7 | Rearrange +/- numbers | Partition |
| 8 | Remove duplicates sorted | Write index |

---

## The ONE trick to remember

```
"low/mid/high: 0→swap low, 1→skip, 2→swap high (don't move mid)."
```

Three regions, one sweep. The only subtlety: after swapping a 2 to the back, re-examine that position — so don't advance `mid`.

> Memory hook: "Push 0s to the front, 2s to the back, 1s fall in the middle."

---
