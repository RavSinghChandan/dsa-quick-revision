# Q5 — Maximum Sum Rectangle in 2D Matrix (Kadane's on Columns)

## 1. What is this question actually asking?

Given a 2D matrix of integers (may contain negatives), find the submatrix (rectangle) with the maximum sum. Return the maximum sum. This is the 2D extension of the Maximum Subarray (Kadane's) problem.

Example:
```
Matrix:
 1  2 -1 -4 -20
-8 -3  4  2   1
 3  8 10  1   3
-4 -1  1  7  -6
```
Maximum sum rectangle: rows 1-3, columns 1-3, sum = 29.

## 2. Pattern

Fix left and right column boundaries → compress 2D to 1D using row sums → apply Kadane's 1D algorithm.

## 3. Understand with a Diagram

```
Matrix (4 rows, 5 cols):
  1   2  -1  -4 -20
 -8  -3   4   2   1
  3   8  10   1   3
 -4  -1   1   7  -6

Step 1: Fix left=0, right=0. Row sums for each row:
  row 0: 1
  row 1: -8
  row 2: 3
  row 3: -4
  Apply Kadane's on [1,-8,3,-4] → max contiguous = 3

Step 2: Fix left=0, right=1. Row sums:
  row 0: 1+2=3
  row 1: -8+(-3)=-11
  row 2: 3+8=11
  row 3: -4+(-1)=-5
  Apply Kadane's on [3,-11,11,-5] → max = 11

Step 3: Fix left=1, right=3. Row sums:
  row 0: 2+(-1)+(-4)=-3
  row 1: -3+4+2=3
  row 2: 8+10+1=19
  row 3: -1+1+7=7
  Apply Kadane's on [-3,3,19,7] → max = 3+19+7=29 ← ANSWER

Algorithm:
for left in 0..cols-1:
    temp = [0]*rows
    for right in left..cols-1:
        for r in rows:
            temp[r] += matrix[r][right]    // add column to running row sums
        max_here = kadane(temp)             // best contiguous rows
        global_max = max(global_max, max_here)

Kadane's 1D:
  curr = temp[0]; best = temp[0]
  for x in temp[1:]:
      curr = max(x, curr+x)
      best = max(best, curr)

Total time: O(cols^2 * rows)

Visualization of optimal rectangle (left=1, right=3, rows=1..3):
  -3  4   2  →  sum 3
   8  10  1  →  sum 19
  -1  1   7  →  sum 7
  Total = 29
```

## 4. Brute Force → Better → Optimal

### Brute Force — O(n^4) or O(n^6)
**Idea:** Try all (r1, c1, r2, c2) combos, sum each submatrix.

O(n^4) with prefix sums, O(n^6) without. Impractical for large matrices.

### Optimal — Kadane's with column fixing O(rows * cols^2)
**Idea:** Fix left and right column indices. Collapse the 2D problem into 1D by computing row sums for each row. Apply Kadane's to find optimal row range.

O(cols^2 * rows) time, O(rows) space.

## 5. Pseudocode (Optimal)

```
function maxSumRectangle(matrix):
    rows = len(matrix), cols = len(matrix[0])
    max_sum = -infinity
    
    for left from 0 to cols-1:
        temp = [0] * rows  // row sums between left and right
        
        for right from left to cols-1:
            // Add current column to running row sums
            for r from 0 to rows-1:
                temp[r] += matrix[r][right]
            
            // Apply Kadane's on temp
            curr = temp[0], best = temp[0]
            start_r = 0, best_start = 0, best_end = 0
            for r from 1 to rows-1:
                if curr + temp[r] > temp[r]:
                    curr += temp[r]
                else:
                    curr = temp[r]
                    start_r = r
                if curr > best:
                    best = curr
                    best_start = start_r
                    best_end = r
            
            if best > max_sum:
                max_sum = best
                // record left, right, best_start, best_end as submatrix bounds
    
    return max_sum
```

## 6. Python Code

```python
def maxSumRectangle(matrix):
    """
    Find maximum sum submatrix in a 2D matrix.
    Uses Kadane's algorithm on column-compressed row sums.
    """
    if not matrix or not matrix[0]:
        return 0
    
    rows, cols = len(matrix), len(matrix[0])
    max_sum = float('-inf')
    
    def kadane(arr):
        curr = best = arr[0]
        for x in arr[1:]:
            curr = max(x, curr + x)
            best = max(best, curr)
        return best
    
    for left in range(cols):
        temp = [0] * rows    # accumulated row sums
        
        for right in range(left, cols):
            # Add right column to row sums
            for r in range(rows):
                temp[r] += matrix[r][right]
            
            # Best contiguous subarray sum in temp = best row range
            curr_sum = kadane(temp)
            max_sum = max(max_sum, curr_sum)
    
    return max_sum


def maxSumRectangleWithBounds(matrix):
    """Also returns the bounding box of the max sum rectangle."""
    rows, cols = len(matrix), len(matrix[0])
    max_sum = float('-inf')
    result = (0, 0, 0, 0)  # (r1, c1, r2, c2)
    
    for left in range(cols):
        temp = [0] * rows
        
        for right in range(left, cols):
            for r in range(rows):
                temp[r] += matrix[r][right]
            
            # Kadane's with row tracking
            curr = temp[0]
            best = temp[0]
            start = 0
            best_start = best_end = 0
            
            for r in range(1, rows):
                if curr + temp[r] >= temp[r]:
                    curr += temp[r]
                else:
                    curr = temp[r]
                    start = r
                if curr > best:
                    best = curr
                    best_start = start
                    best_end = r
            
            if best > max_sum:
                max_sum = best
                result = (best_start, left, best_end, right)
    
    return max_sum, result


# Test 1
matrix1 = [
    [1,  2, -1, -4, -20],
    [-8, -3,  4,  2,   1],
    [3,   8, 10,  1,   3],
    [-4, -1,  1,  7,  -6]
]
print(maxSumRectangle(matrix1))    # 29

# Test 2 — all negative
matrix2 = [[-1, -2], [-3, -4]]
print(maxSumRectangle(matrix2))    # -1

# Test 3 — single row
matrix3 = [[-2, 1, -3, 4, -1, 2, 1, -5, 4]]
print(maxSumRectangle(matrix3))    # 6

# Test 4 — single column
matrix4 = [[-2], [1], [-3], [4], [-1]]
print(maxSumRectangle(matrix4))    # 4

# Test 5 — with bounds
score, bounds = maxSumRectangleWithBounds(matrix1)
print(score, bounds)   # 29 (1, 1, 3, 3)
```

## 7. Complexity Table

| Approach              | Time              | Space   | Notes                           |
|-----------------------|-------------------|---------|---------------------------------|
| Brute Force (no PS)   | O(r^3 * c^3)     | O(1)    | Enumerate all, sum each         |
| Brute Force (with PS) | O(r^2 * c^2)     | O(r*c)  | Prefix sums for O(1) range sum  |
| Kadane's on columns   | O(c^2 * r)       | O(r)    | Optimal                         |

## 8. Edge Cases to Remember

- All negative matrix — max sum = largest (least negative) single element
- Single row — equivalent to max subarray (Kadane's 1D directly)
- Single column — same, Kadane's on that column
- Square matrix — same algorithm, just rows == cols
- To get the actual submatrix: track start_row, end_row, left, right columns during Kadane's

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Maximum Subarray (Kadane's)           | 1D version — foundation of this problem             | LC 53      |
| Maximum Sum Submatrix <= K            | Kadane's + binary search on prefix sums             | LC 363     |
| Largest Rectangle in Histogram        | Column-based DP/stack                               | LC 84      |
| Maximal Square                        | DP on 2D grid                                       | LC 221     |
| Maximal Rectangle                     | Histogram approach on each row                      | LC 85      |
| Sum of All Submatrices                | Prefix sum contribution counting                    | GFG        |
| Range Sum Query 2D                    | Prefix sums for O(1) 2D range queries               | LC 304     |
| Subarray Sum Equals K                 | HashMap on prefix sums (1D)                         | LC 560     |

## 10. The ONE Trick to Remember

**"Fix left and right columns; accumulate row sums in temp[]; apply Kadane's 1D on temp[] to find best row range."**

The key insight: fixing two column boundaries converts the 2D problem into 1D. The row sums between those columns form a 1D array, and Kadane's algorithm finds the contiguous subarray with the largest sum, giving us the optimal row range. Running over all O(cols^2) column pairs with O(rows) Kadane's gives O(cols^2 * rows) total — efficient when rows > cols (rotate if needed).
