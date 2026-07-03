# Q3 — Median of Two Sorted Arrays (LeetCode 4)

## 1. What is this question actually asking?

You have two sorted arrays. You need to find the median of all the elements combined, as if you merged them into one sorted array. The median is the middle element (for odd total count) or the average of the two middle elements (for even total count). The catch: you must do this in O(log(m+n)) time — no merging allowed.

## 2. Pattern

Binary Search — partition the smaller array, derive the partition of the larger array, check balance condition

## 3. Understand with a Diagram

```
nums1 = [1, 3, 8, 9, 15]    (m=5)
nums2 = [7, 11, 18, 19, 21, 25]  (n=6)
Total = 11 elements → median at position 6

Idea: Partition both arrays such that:
  - left half has exactly (m+n+1)//2 elements
  - max of left parts <= min of right parts

Partition visualization:
nums1: [1, 3, 8 | 9, 15]
              ^
          partition1=3

nums2: [7, 11, 18 | 19, 21, 25]
               ^
           partition2=3

Left side:  {1, 3, 8}  ∪  {7, 11, 18}  → max = 18
Right side: {9, 15}    ∪  {19, 21, 25} → min = 9

Is 18 <= 9? NO — imbalance! Need to move partition1 left.

Try partition1=2:
nums1: [1, 3 | 8, 9, 15]
nums2: [7, 11, 18, 19 | 21, 25]

Left:  {1,3} ∪ {7,11,18,19} → max = 19
Right: {8,9,15} ∪ {21,25}   → min = 8
Is 19 <= 8? NO

Try partition1=1:
nums1: [1 | 3, 8, 9, 15]
nums2: [7, 11, 18, 19, 21 | 25]

Left:  {1} ∪ {7,11,18,19,21} → max(nums1_left)=1, max(nums2_left)=21
Right: {3,8,9,15} ∪ {25}     → min(nums1_right)=3, min(nums2_right)=25

Is max_left1=1 <= min_right2=25? YES
Is max_left2=21 <= min_right1=3? NO

Try partition1=2 again... let me use a cleaner example:

Simple example:
nums1 = [1, 3]
nums2 = [2]
Combined sorted: [1, 2, 3] → median = 2

Partition search:
half = (2+1+1)//2 = 2
Binary search partition in nums1 (smaller):
partition1=1: nums1=[1 | 3]
partition2=2-1=1: nums2=[2 | (nothing)]
max_left  = max(nums1[0], nums2[0]) = max(1,2) = 2
min_right = min(nums1[1], inf)      = min(3, inf) = 3
2 <= 3 and 2 <= 3 ✓ → odd total → median = max_left = 2 ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — Merge then find median
**Idea:** Merge both sorted arrays into one sorted array, then return the middle element(s).

```
merged = []
i = j = 0
while i < m and j < n:
    if nums1[i] < nums2[j]: merged.append(nums1[i++])
    else: merged.append(nums2[j++])
while i < m: merged.append(nums1[i++])
while j < n: merged.append(nums2[j++])

total = m + n
if total % 2 == 1: return merged[total//2]
else: return (merged[total//2-1] + merged[total//2]) / 2
```

Why slow: O(m+n) time and space.

### Better — Binary search on merged virtual array
**Idea:** Binary search for the kth element without merging. Use the property that you can eliminate k//2 elements in each step.

Why better: O(log(m+n)) time but complex implementation.

### Optimal — Partition both arrays
**Idea:** Binary search on the smaller array to find a partition point. Derive the corresponding partition in the larger array. Check if the partition is valid (max_left <= min_right for both arrays). If valid, the median is computed from the border elements.

```
ensure nums1 is smaller (swap if needed)
half = (m + n + 1) // 2

lo = 0, hi = m

while lo <= hi:
    p1 = (lo + hi) // 2    // partition in nums1
    p2 = half - p1          // partition in nums2
    
    max_left1  = nums1[p1-1] if p1 > 0 else -inf
    min_right1 = nums1[p1]   if p1 < m else +inf
    max_left2  = nums2[p2-1] if p2 > 0 else -inf
    min_right2 = nums2[p2]   if p2 < n else +inf
    
    if max_left1 <= min_right2 and max_left2 <= min_right1:
        // Found correct partition
        if (m + n) % 2 == 1:
            return max(max_left1, max_left2)
        else:
            return (max(max_left1, max_left2) + min(min_right1, min_right2)) / 2
    elif max_left1 > min_right2:
        hi = p1 - 1    // p1 too large, move left
    else:
        lo = p1 + 1    // p1 too small, move right
```

## 5. Pseudocode (Optimal)

```
function findMedianSortedArrays(nums1[], nums2[]):
    if len(nums1) > len(nums2):
        swap(nums1, nums2)
    
    m = len(nums1), n = len(nums2)
    half = (m + n + 1) // 2
    lo = 0, hi = m
    
    while lo <= hi:
        p1 = (lo + hi) // 2
        p2 = half - p1
        
        L1 = nums1[p1-1]  if p1 > 0 else -INF
        R1 = nums1[p1]    if p1 < m else +INF
        L2 = nums2[p2-1]  if p2 > 0 else -INF
        R2 = nums2[p2]    if p2 < n else +INF
        
        if L1 <= R2 and L2 <= R1:
            if (m+n) % 2 == 1: return max(L1, L2)
            else: return (max(L1, L2) + min(R1, R2)) / 2.0
        elif L1 > R2:
            hi = p1 - 1
        else:
            lo = p1 + 1
```

## 6. Python Code

```python
def findMedianSortedArrays(nums1, nums2):
    # Always binary search on smaller array
    if len(nums1) > len(nums2):
        nums1, nums2 = nums2, nums1
    
    m, n = len(nums1), len(nums2)
    half = (m + n + 1) // 2
    lo, hi = 0, m
    
    while lo <= hi:
        p1 = (lo + hi) // 2
        p2 = half - p1
        
        L1 = nums1[p1-1] if p1 > 0 else float('-inf')
        R1 = nums1[p1]   if p1 < m else float('inf')
        L2 = nums2[p2-1] if p2 > 0 else float('-inf')
        R2 = nums2[p2]   if p2 < n else float('inf')
        
        if L1 <= R2 and L2 <= R1:
            if (m + n) % 2 == 1:
                return float(max(L1, L2))
            else:
                return (max(L1, L2) + min(R1, R2)) / 2.0
        elif L1 > R2:
            hi = p1 - 1
        else:
            lo = p1 + 1
    
    return -1  # unreachable


# Test 1 — odd total
print(findMedianSortedArrays([1, 3], [2]))        # 2.0

# Test 2 — even total
print(findMedianSortedArrays([1, 2], [3, 4]))     # 2.5

# Test 3 — one empty array
print(findMedianSortedArrays([], [1, 2, 3, 4]))   # 2.5

# Test 4 — both single element
print(findMedianSortedArrays([1], [2]))            # 1.5

# Test 5 — no overlap
print(findMedianSortedArrays([1, 2, 3], [10, 11, 12]))  # 6.5
```

## 7. Complexity Table

| Approach          | Time          | Space | Notes                              |
|-------------------|---------------|-------|------------------------------------|
| Merge + median    | O(m+n)        | O(m+n)| Simple but linear                  |
| Kth element BS    | O(log(m+n))   | O(1)  | Eliminate k//2 elements per step   |
| Partition BS      | O(log(min(m,n)))| O(1)| Binary search on smaller array     |

## 8. Edge Cases to Remember

- One array empty — partition falls at 0 or m, handled by -inf/+inf guards
- Arrays of different sizes — always binary search on smaller for efficiency
- Odd vs even total — compute both L and R at valid partition, return appropriately
- All elements of nums1 < all of nums2 — partition1=m, partition2=0, valid immediately
- All elements of nums1 > all of nums2 — partition1=0, partition2=half, valid immediately
- Single element arrays — works, -inf/+inf guards handle boundary

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Kth Smallest in Sorted Matrix         | Binary search on value range                        | LC 378     |
| Kth Largest Element in Array          | QuickSelect or binary search on values              | LC 215     |
| Find K-th Smallest Pair Distance      | Binary search on answer                             | LC 719     |
| Median of Data Stream                 | Two heaps to maintain running median                | LC 295     |
| Find K Pairs with Smallest Sums       | Heap + sorted structure                             | LC 373     |
| Split Array Largest Sum               | Binary search on answer value                       | LC 410     |
| Minimize Max Distance to Gas Station  | Binary search on answer                             | LC 774     |
| Search a 2D Matrix                    | Binary search on sorted structure                   | LC 74      |

## 10. The ONE Trick to Remember

**"Partition the smaller array, derive the other partition, and check if left halves are smaller than right halves."**

Binary search on partition point p1 in the smaller array. Partition p2 in the larger array is automatically half - p1. You have a valid partition when max(left sides) <= min(right sides). When not valid, adjust p1 left or right. At the valid partition, the median lives at the boundary elements.
