# Q2 — Search in 2D Matrix (LeetCode 74)

## 1. What is this question actually asking?

You have a 2D matrix where each row is sorted left to right, and the first element of each row is greater than the last element of the previous row. This means the whole matrix is essentially one long sorted array folded into rows. Given a target number, determine if it exists in the matrix. You need to do this efficiently — O(log(m*n)) — not by checking every cell.

## 2. Pattern

Binary Search — treat the 2D matrix as a flattened 1D sorted array, use index math to convert

## 3. Understand with a Diagram

```
Matrix (3x4):
     col: 0   1   2   3
row 0: [  1,  3,  5,  7 ]
row 1: [ 10, 11, 16, 20 ]
row 2: [ 23, 30, 34, 60 ]

Flattened view (conceptual):
index:  0   1   2   3   4   5   6   7   8   9  10  11
value: [ 1,  3,  5,  7, 10, 11, 16, 20, 23, 30, 34, 60]

Binary search on range [0, m*n - 1]:
mid index → row = mid // n, col = mid % n

Search for target=11:
lo=0, hi=11
mid=5 → row=5//4=1, col=5%4=1 → matrix[1][1]=11 == 11 ✓

Search for target=13:
lo=0, hi=11
mid=5 → matrix[1][1]=11 < 13 → lo=6
mid=8 → matrix[2][0]=23 > 13 → hi=7
mid=6 → matrix[1][2]=16 > 13 → hi=5
lo=6 > hi=5 → return False
```

## 4. Brute Force → Better → Optimal

### Brute Force — Check every cell
**Idea:** Loop through every row and every column. Return True if found.

```
for row in matrix:
    for val in row:
        if val == target:
            return True
return False
```

Why slow: O(m*n). Ignores all sorting structure.

### Better — Binary search each row
**Idea:** Since each row is sorted, binary search within each row.

```
for each row in matrix:
    if target >= row[0] and target <= row[-1]:
        binary search in row
```

Why better: O(m log n) — better than brute but doesn't use cross-row sorted property.

### Optimal — Treat as 1D sorted array
**Idea:** The matrix is a sorted 1D array folded into rows. Run binary search on indices 0 to m*n-1. Convert index to (row, col) using integer division and modulo.

```
lo = 0
hi = m * n - 1

while lo <= hi:
    mid = (lo + hi) // 2
    row = mid // n
    col = mid % n
    val = matrix[row][col]
    
    if val == target: return True
    elif val < target: lo = mid + 1
    else: hi = mid - 1

return False
```

**Trace for target=16:**
```
matrix 3x4, lo=0, hi=11

mid=5 → [1][1]=11 < 16 → lo=6
mid=8 → [2][0]=23 > 16 → hi=7
mid=6 → [1][2]=16 == 16 → True ✓
```

## 5. Pseudocode (Optimal)

```
function searchMatrix(matrix[][], target):
    m = rows, n = cols
    lo = 0
    hi = m * n - 1
    
    while lo <= hi:
        mid = (lo + hi) // 2
        val = matrix[mid // n][mid % n]
        
        if val == target: return True
        elif val < target: lo = mid + 1
        else: hi = mid - 1
    
    return False
```

## 6. Python Code

```python
def searchMatrix(matrix, target):
    if not matrix or not matrix[0]:
        return False
    
    m, n = len(matrix), len(matrix[0])
    lo, hi = 0, m * n - 1
    
    while lo <= hi:
        mid = (lo + hi) // 2
        val = matrix[mid // n][mid % n]
        
        if val == target:
            return True
        elif val < target:
            lo = mid + 1
        else:
            hi = mid - 1
    
    return False


# Test 1 — standard case, target found
matrix = [
    [1,  3,  5,  7],
    [10, 11, 16, 20],
    [23, 30, 34, 60]
]
print(searchMatrix(matrix, 3))    # True
print(searchMatrix(matrix, 16))   # True
print(searchMatrix(matrix, 11))   # True

# Test 2 — target not found
print(searchMatrix(matrix, 13))   # False
print(searchMatrix(matrix, 0))    # False
print(searchMatrix(matrix, 61))   # False

# Test 3 — single cell
print(searchMatrix([[5]], 5))     # True
print(searchMatrix([[5]], 1))     # False

# Test 4 — single row
print(searchMatrix([[1,3,5,7]], 5))   # True
print(searchMatrix([[1,3,5,7]], 4))   # False
```

## 7. Complexity Table

| Approach          | Time          | Space | Notes                          |
|-------------------|---------------|-------|--------------------------------|
| Brute Force       | O(m*n)        | O(1)  | Check every cell               |
| Binary per row    | O(m log n)    | O(1)  | Binary search each row         |
| 1D Binary Search  | O(log(m*n))   | O(1)  | Optimal — treat as 1D          |

## 8. Edge Cases to Remember

- Empty matrix — return False immediately
- Single cell — works normally
- Target smaller than first element — binary search exits with lo > hi
- Target larger than last element — same
- Single row or single column — index math still works
- Matrix with non-uniform rows — this problem guarantees uniform, but watch out in variants
- Note: LC 240 (Search a 2D Matrix II) is different — rows sorted, columns sorted, but rows don't connect, needs staircase search

## 9. Similar Questions

| Question                              | Where Pattern Applies                              | LeetCode # |
|---------------------------------------|----------------------------------------------------|------------|
| Search a 2D Matrix II                 | Rows & cols sorted; start top-right corner         | LC 240     |
| Binary Search                         | Foundation technique                               | LC 704     |
| Find Minimum in Rotated Sorted Array  | Binary search on transformed array                 | LC 153     |
| Search in Rotated Sorted Array        | Binary search on rotated sorted array              | LC 33      |
| Kth Smallest in Sorted Matrix         | Binary search on value range                       | LC 378     |
| Row with Maximum 1s                   | Binary search per row                              | GFG        |
| Find Peak Element                     | Binary search on 1D array with condition           | LC 162     |
| Median of Two Sorted Arrays           | Binary search on merged sorted structure           | LC 4       |

## 10. The ONE Trick to Remember

**"Fold it flat — mid // cols gives row, mid % cols gives column."**

The entire matrix is a sorted array in disguise. Binary search on flat indices 0 to m*n-1, and whenever you need the actual value, convert back with row = mid // n and col = mid % n. Two lines of math unlock O(log(m*n)) search on any such matrix.
