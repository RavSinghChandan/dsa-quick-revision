# Q1 — Find Element in Rotated Sorted Array (LeetCode 33)

## 1. What is this question actually asking?

You're given a sorted array that has been rotated at some unknown pivot point. For example, [1,2,3,4,5] might become [4,5,1,2,3] after rotation. Given a target value, find its index in the array, or return -1 if it's not there. The key constraint is you must do this in O(log n) time — which means no linear scan allowed.

## 2. Pattern

Binary Search — modified to handle the rotation by identifying which half is sorted

## 3. Understand with a Diagram

```
Original sorted:  [1, 2, 3, 4, 5, 6, 7]
After rotation:   [4, 5, 6, 7, 1, 2, 3]
                   ^           ^
                 pivot        rotation point

Array layout:
index:  0  1  2  3  4  5  6
value:  4  5  6  7  1  2  3
        [LEFT SORTED ]  [RIGHT SORTED]
              ^
             mid=3 (value=7)

Key insight: at least ONE half is always sorted!
Left half [4,5,6,7] is sorted (arr[lo] <= arr[mid])
Right half [1,2,3] is sorted (arr[mid+1] <= arr[hi])

Search for target=1:
lo=0, hi=6, mid=3, arr[mid]=7
  Left sorted? arr[lo]=4 <= arr[mid]=7 → YES
  Is 1 in left range [4..7]? NO (1 < 4)
  → Search right: lo=4, hi=6

lo=4, hi=6, mid=5, arr[mid]=2
  Left sorted? arr[lo]=1 <= arr[mid]=2 → YES
  Is 1 in left range [1..2]? YES (1 >= 1 and 1 <= 2)
  → Search left: lo=4, hi=5

lo=4, hi=5, mid=4, arr[mid]=1
  arr[mid] == target → return 4 ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — Linear Scan
**Idea:** Just loop through every element and return the index when found.

```
for i from 0 to n-1:
    if arr[i] == target:
        return i
return -1
```

Why slow: O(n). Ignores the sorted structure entirely.

### Better — Find pivot then binary search
**Idea:** First find the pivot (the rotation point) using binary search in O(log n). Then determine which subarray the target belongs to, and binary search that subarray.

```
find pivot index p where arr[p] > arr[p+1]
if target >= arr[0]: binary search arr[0..p]
else: binary search arr[p+1..n-1]
```

Why better: O(log n) but requires two passes — find pivot, then search.

### Optimal — Single pass modified binary search
**Idea:** In every binary search step, at least one half is guaranteed to be sorted. Check which half is sorted, then check if target falls in that sorted range. If yes, search there; if no, search the other half.

```
lo = 0, hi = n - 1

while lo <= hi:
    mid = (lo + hi) // 2
    
    if arr[mid] == target: return mid
    
    if arr[lo] <= arr[mid]:     // left half is sorted
        if arr[lo] <= target < arr[mid]:
            hi = mid - 1        // target in left sorted half
        else:
            lo = mid + 1        // target in right (possibly rotated) half
    else:                       // right half is sorted
        if arr[mid] < target <= arr[hi]:
            lo = mid + 1        // target in right sorted half
        else:
            hi = mid - 1        // target in left (possibly rotated) half

return -1
```

**Trace for [4,5,6,7,1,2,3], target=2:**
```
lo=0, hi=6
Step 1: mid=3, arr[3]=7 ≠ 2
  arr[lo]=4 <= arr[mid]=7 → left sorted [4..7]
  Is 2 in [4..7]? NO → lo = 4

lo=4, hi=6
Step 2: mid=5, arr[5]=2 == 2 → return 5 ✓
```

## 5. Pseudocode (Optimal)

```
function search(arr[], target):
    lo = 0
    hi = len(arr) - 1
    
    while lo <= hi:
        mid = (lo + hi) // 2
        
        if arr[mid] == target:
            return mid
        
        // Determine which half is sorted
        if arr[lo] <= arr[mid]:
            // Left half [lo..mid] is sorted
            if arr[lo] <= target < arr[mid]:
                hi = mid - 1
            else:
                lo = mid + 1
        else:
            // Right half [mid..hi] is sorted
            if arr[mid] < target <= arr[hi]:
                lo = mid + 1
            else:
                hi = mid - 1
    
    return -1
```

## 6. Python Code

```python
def search(nums, target):
    lo, hi = 0, len(nums) - 1
    
    while lo <= hi:
        mid = (lo + hi) // 2
        
        if nums[mid] == target:
            return mid
        
        # Left half is sorted
        if nums[lo] <= nums[mid]:
            if nums[lo] <= target < nums[mid]:
                hi = mid - 1
            else:
                lo = mid + 1
        # Right half is sorted
        else:
            if nums[mid] < target <= nums[hi]:
                lo = mid + 1
            else:
                hi = mid - 1
    
    return -1


# Test 1 — standard rotation
print(search([4,5,6,7,0,1,2], 0))   # 4
print(search([4,5,6,7,0,1,2], 3))   # -1

# Test 2 — no rotation (normal sorted array)
print(search([1,2,3,4,5], 3))       # 2

# Test 3 — target at boundaries
print(search([4,5,6,7,0,1,2], 4))   # 0 (first element)
print(search([4,5,6,7,0,1,2], 2))   # 6 (last element)

# Test 4 — single element
print(search([1], 1))   # 0
print(search([1], 0))   # -1

# Test 5 — two elements
print(search([3,1], 1))  # 1
print(search([1,3], 3))  # 1
```

## 7. Complexity Table

| Approach          | Time     | Space | Notes                              |
|-------------------|----------|-------|------------------------------------|
| Brute Force       | O(n)     | O(1)  | Linear scan                        |
| Find Pivot + BS   | O(log n) | O(1)  | Two binary searches                |
| Modified BS       | O(log n) | O(1)  | Single pass — optimal              |

## 8. Edge Cases to Remember

- Array not rotated at all — the algorithm still works (left half always sorted)
- Target not in array — returns -1
- Single element — lo==hi==mid, check arr[mid]==target
- Two elements — handle carefully, rotation can put either element first
- Duplicates present — the condition arr[lo] <= arr[mid] becomes ambiguous. Need LC 81 variant with lo++ when arr[lo]==arr[mid]
- Target equals arr[lo] or arr[hi] — still caught by arr[mid]==target or range checks

## 9. Similar Questions

| Question                                      | Where Pattern Applies                       | LeetCode # |
|-----------------------------------------------|---------------------------------------------|------------|
| Search in Rotated Sorted Array II (duplicates)| Same but with duplicates, edge case lo++    | LC 81      |
| Find Minimum in Rotated Sorted Array          | Find pivot/minimum using binary search      | LC 153     |
| Find Minimum in Rotated (duplicates)          | Same with duplicates                        | LC 154     |
| Binary Search (basic)                         | Foundation of this technique                | LC 704     |
| Search a 2D Matrix                            | Binary search on flattened matrix           | LC 74      |
| Peak Index in Mountain Array                  | Binary search on unimodal array             | LC 852     |
| Find Peak Element                             | Binary search where arr[mid] > neighbors    | LC 162     |
| Kth Rotation of Array                         | Find pivot count using binary search        | GFG        |

## 10. The ONE Trick to Remember

**"One half is always sorted — figure out which one, then decide where to go."**

In a rotated sorted array, bisecting always gives you at least one completely sorted half. Check if arr[lo] <= arr[mid] (left is sorted) or not (right is sorted). Then ask: does my target fit in the sorted half's range? If yes, search there. If no, search the other side. This eliminates half the array every step.
