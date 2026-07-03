# Q5 — Max Sum Rectangle in 2D Matrix (2-min card)

**Find submatrix with maximum sum (matrix may contain negatives).**

**Pattern:** Fix left & right column → compress rows to 1D → apply Kadane's

```
Matrix:           Fix left=1, right=3 → row sums:
 1   2  -1 -4     row0: 2+(-1)+(-4) = -3
-8  -3   4  2     row1: -3+4+2 = 3
 3   8  10  1     row2: 8+10+1 = 19
-4  -1   1  7     row3: -1+1+7 = 7

Kadane's on [-3,3,19,7]:
  curr=-3→3→22→29, best=29  ← Answer
```

**The trick:** "Fix two column boundaries; accumulate row sums in temp[]; Kadane's 1D on temp gives best row range."

```python
def kadane(arr):
    curr = best = arr[0]
    for x in arr[1:]: curr = max(x, curr+x); best = max(best, curr)
    return best

max_sum = float('-inf')
for left in range(cols):
    temp = [0]*rows
    for right in range(left, cols):
        for r in range(rows): temp[r] += matrix[r][right]
        max_sum = max(max_sum, kadane(temp))
return max_sum
```

**Complexity:** Time O(cols^2 * rows) | Space O(rows)

**Same pattern solves:** Max Subarray (LC 53), Max Sum Submatrix<=K (LC 363), Maximal Rectangle (LC 85), Maximal Square (LC 221), Range Sum Query 2D (LC 304).
