# Day 9 — Dynamic Programming · Q3 · Minimum Falling Path Sum

---

## What is this question actually asking?

Starting from any cell in the **top row** of a matrix, fall to the bottom moving to the cell **directly below or diagonally below**. Find the **minimum path sum**.

```
[[2,1,3],
 [6,5,4],
 [7,8,9]] → 13   (1 → 5 → 7)
```

> A grid DP where each cell depends on the three cells above it. The template for many "path down a grid" problems.

---

## Pattern

```
PATTERN: Grid DP — dp[r][c] += min of the 3 cells above (up-left, up, up-right)
```

Whenever you see: *"minimum/maximum path down a grid with diagonal moves"* → `dp[r][c] = matrix[r][c] + min(dp[r-1][c-1], dp[r-1][c], dp[r-1][c+1])`. Answer = min of the last row.

---

## Understand with a diagram

```
Each cell can come from directly above or the two diagonals above:

        (r-1,c-1) (r-1,c) (r-1,c+1)
              \      |      /
                  (r, c)

dp[r][c] = mat[r][c] + min(those three, respecting borders)
answer = min(dp[last row])
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — try all paths — O(3^n)
Every falling path from each top cell.

### 🟢 Optimal — bottom-up DP — O(R·C)
Fill row by row; each cell takes the min of the three above. O(1) extra if you update in place.

---

## Pseudocode (Optimal)

```
function min_falling_path(mat):
    n = len(mat)
    for r in 1..n-1:
        for c in 0..n-1:
            best = mat[r-1][c]
            if c > 0: best = min(best, mat[r-1][c-1])
            if c < n-1: best = min(best, mat[r-1][c+1])
            mat[r][c] += best
    return min(mat[last row])
```

---

## Python Code

```python
def min_falling_path_sum(matrix: list[list[int]]) -> int:
    n = len(matrix)
    dp = [row[:] for row in matrix]          # copy to avoid mutating input
    for r in range(1, n):
        for c in range(n):
            best = dp[r - 1][c]              # directly above
            if c > 0:
                best = min(best, dp[r - 1][c - 1])   # up-left
            if c < n - 1:
                best = min(best, dp[r - 1][c + 1])   # up-right
            dp[r][c] += best
    return min(dp[-1])


# Test
print(min_falling_path_sum([[2,1,3],[6,5,4],[7,8,9]]))   # 13
print(min_falling_path_sum([[-19,57],[-40,-5]]))         # -59
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Grid DP | O(R·C) | O(R·C) (O(C) rolling / in place) |

---

## Edge Cases to remember

- **Border columns** — only two cells above for `c=0` and `c=n-1`; guard the diagonals.
- **Answer is min of the LAST ROW**, not `dp[n-1][0]` — any bottom cell can end the path.
- **Negative values** allowed — no clamping.
- **In-place** — you can update `matrix` directly for O(1) extra space (or roll one row).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Triangle Minimum Path (LC 120) | Bottom-up DP |
| 2 | Minimum Path Sum (LC 64) | Right/down only |
| 3 | Maximum Falling Path (variant) | max instead of min |
| 4 | Cherry Pickup II (LC 1463) | Two-robot grid DP |
| 5 | Dungeon Game (LC 174) | Reverse grid DP |
| 6 | Unique Paths (LC 62) | Count paths |
| 7 | Max sum path in matrix | Grid DP |
| 8 | Falling path with non-zero shifts (LC 1289) | Grid DP + min-excluding |

---

## The ONE trick to remember

```
"dp[r][c] += min(up-left, up, up-right); ANSWER = min OF LAST ROW."
```

Each cell's best path sum is its value plus the minimum of the (up to) three cells above it. Fill top-down and take the smallest value in the final row.

> Memory hook: "Fall into each cell from the cheapest of the three cells above."

---
