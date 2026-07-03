# Q1 — Rotated Sorted Array Search (2-min card) — LC 33

**Find target in a sorted array that was rotated at an unknown pivot. Return index or -1.**

**Pattern:** Modified Binary Search — one half is always sorted

```
[4, 5, 6, 7, 1, 2, 3]   target=1
 lo=0       mid=3  hi=6
arr[lo]=4 <= arr[mid]=7 → LEFT sorted [4..7]
Is 1 in [4..7]? NO → go RIGHT
 lo=4, hi=6, mid=5, arr[5]=2 ≠ 1
arr[lo]=1 <= arr[mid]=2 → LEFT sorted [1..2]
Is 1 in [1..2]? YES → go LEFT
 lo=4, hi=4, arr[4]=1 → FOUND at index 4
```

**The trick:** "One half is always sorted — check if target fits, else go other side."

```python
lo, hi = 0, len(nums)-1
while lo <= hi:
    mid = (lo+hi)//2
    if nums[mid] == target: return mid
    if nums[lo] <= nums[mid]:  # left sorted
        if nums[lo] <= target < nums[mid]: hi = mid-1
        else: lo = mid+1
    else:                      # right sorted
        if nums[mid] < target <= nums[hi]: lo = mid+1
        else: hi = mid-1
```

**Complexity:** Time O(log n) | Space O(1)

**Same pattern solves:** Search in Rotated II/duplicates (LC 81), Find Min in Rotated (LC 153), Peak Element (LC 162), Peak in Mountain (LC 852), Binary Search (LC 704), Find Kth Rotation, Search 2D Matrix (LC 74).
