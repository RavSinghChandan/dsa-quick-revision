# Day 2 — Binary Search · Q1 · Search in Rotated Sorted Array II (with duplicates)

---

## What is this question actually asking?

A sorted array was **rotated** at some pivot, and it **may contain duplicates**. Return whether a `target` exists.

```
[2,5,6,0,0,1,2], target 0 → True
[2,5,6,0,0,1,2], target 3 → False
```

> The "II" twist is **duplicates**, which break the clean rotated binary search — you must handle the `nums[lo]==nums[mid]==nums[hi]` ambiguity.

---

## Pattern

```
PATTERN: Modified Binary Search on a rotated array (+ duplicate guard)
```

Whenever you see: *"search in a rotated sorted array"* → at each step one half is definitely sorted; check if the target lies in that sorted half, else go the other way. With duplicates, add a guard to shrink the window when ends are equal.

---

## Understand with a diagram

```
[2,5,6,0,0,1,2]   lo, mid, hi

If nums[lo]==nums[mid]==nums[hi]:  can't tell which half is sorted → lo++, hi--  (shrink)
Else if left half sorted (nums[lo] <= nums[mid]):
    target in [nums[lo], nums[mid])? → hi=mid-1 else lo=mid+1
Else (right half sorted):
    target in (nums[mid], nums[hi]]? → lo=mid+1 else hi=mid-1
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — linear scan — O(n)
Just loop. Ignores the sorted structure.

### 🟢 Optimal — modified binary search — O(log n) avg, O(n) worst
Binary search deciding which half is sorted. Worst case O(n) *only* when many duplicates force the shrink step (e.g. all equal).

---

## Pseudocode (Optimal)

```
function search(nums, target):
    lo, hi = 0, n-1
    while lo <= hi:
        mid = (lo+hi)//2
        if nums[mid] == target: return True
        if nums[lo] == nums[mid] == nums[hi]:
            lo++; hi--                        ← duplicate guard
        elif nums[lo] <= nums[mid]:           ← left sorted
            if nums[lo] <= target < nums[mid]: hi = mid-1
            else: lo = mid+1
        else:                                 ← right sorted
            if nums[mid] < target <= nums[hi]: lo = mid+1
            else: hi = mid-1
    return False
```

---

## Python Code

```python
def search_rotated_ii(nums: list[int], target: int) -> bool:
    lo, hi = 0, len(nums) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if nums[mid] == target:
            return True
        if nums[lo] == nums[mid] == nums[hi]:
            lo += 1                     # can't decide → shrink both ends
            hi -= 1
        elif nums[lo] <= nums[mid]:     # left half sorted
            if nums[lo] <= target < nums[mid]:
                hi = mid - 1
            else:
                lo = mid + 1
        else:                           # right half sorted
            if nums[mid] < target <= nums[hi]:
                lo = mid + 1
            else:
                hi = mid - 1
    return False


# Test
print(search_rotated_ii([2,5,6,0,0,1,2], 0))   # True
print(search_rotated_ii([2,5,6,0,0,1,2], 3))   # False
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Binary search | O(log n) avg, O(n) worst | O(1) |

---

## Edge Cases to remember

- **Duplicate ends** `nums[lo]==nums[mid]==nums[hi]` → shrink (`lo++, hi--`); this is the whole difference from version I.
- **All same values** → degrades to O(n); unavoidable with duplicates.
- **Boundary `<` vs `<=`** in the range checks — get these exactly right (target equal to `nums[lo]` or `nums[hi]`).
- **Single element / empty** → handle directly.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Search Rotated Sorted Array I (LC 33) | No duplicates, clean version |
| 2 | Find Minimum in Rotated Array (LC 153/154) | Find pivot |
| 3 | Find rotation count | Pivot index |
| 4 | Peak element (LC 162) | Half-decision search |
| 5 | Single element in sorted pairs (LC 540) | Binary search on parity |
| 6 | Search in nearly-sorted array | Modified BS |
| 7 | Ceil/floor in sorted array | Boundary BS |
| 8 | First/last position (LC 34) | Boundary BS |

---

## The ONE trick to remember

```
"ONE HALF IS ALWAYS SORTED — SEARCH IT. IF ENDS TIE, SHRINK BOTH."
```

Decide which half is sorted (`nums[lo] <= nums[mid]`), check if the target lies within it, and jump accordingly. The only new thing in version II: when `nums[lo]==nums[mid]==nums[hi]` you can't tell, so `lo++, hi--`.

> Memory hook: "Half the rotated array is still in order — bet on the ordered half."

---
