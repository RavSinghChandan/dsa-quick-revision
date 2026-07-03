# Q2 — Search in 2D Matrix (2-min card) — LC 74

**Find target in a matrix where rows are sorted and last of row i < first of row i+1.**

**Pattern:** Binary Search — treat entire matrix as a flattened 1D sorted array

```
Matrix 3x4:             Flattened:
[1,  3,  5,  7 ]   → [1,3,5,7,10,11,16,20,23,30,34,60]
[10, 11, 16, 20]      index 0..11
[23, 30, 34, 60]
                    mid=5 → row=5//4=1, col=5%4=1 → val=11
```

**The trick:** "Fold it flat — row = mid // n, col = mid % n — then it's just binary search."

```python
m, n = len(matrix), len(matrix[0])
lo, hi = 0, m*n - 1
while lo <= hi:
    mid = (lo+hi)//2
    val = matrix[mid//n][mid%n]
    if val == target: return True
    elif val < target: lo = mid+1
    else: hi = mid-1
return False
```

**Complexity:** Time O(log(m*n)) | Space O(1)

**Same pattern solves:** Search in Rotated Array (LC 33), Find Min in Rotated (LC 153), Kth Smallest in Matrix (LC 378), Binary Search (LC 704), Find Peak (LC 162), Search 2D Matrix II (LC 240 — different algo), Row with Max 1s, Median Two Sorted Arrays (LC 4).
