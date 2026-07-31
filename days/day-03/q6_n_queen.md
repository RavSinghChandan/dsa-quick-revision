# Day 3 — Sliding Window & Backtracking · Q6 · N-Queen

---

## What is this question actually asking?

Place `n` queens on an `n×n` board so **no two attack each other** (no shared row, column, or diagonal). Return all valid arrangements (or just the count).

```
n = 4 → 2 distinct solutions
```

> The definitive backtracking problem. Tests: place one queen per row, prune columns and both diagonals with O(1) sets.

---

## Pattern

```
PATTERN: Backtracking row-by-row + column/diagonal conflict sets
```

Whenever you see: *"place items so none conflict / all valid configurations"* → DFS one **row** at a time; try each column; mark used **column**, **diagonal (r−c)**, **anti-diagonal (r+c)** in sets for O(1) checks; backtrack.

---

## Understand with a diagram

```
Place exactly one queen per row (rows 0..n-1).
Conflict keys for a cell (r,c):
   column     = c
   diagonal   = r - c   (constant along ↘)
   anti-diag  = r + c   (constant along ↙)

If none of {cols, diag, anti} contains the key → safe → place, recurse to row+1.
On return, remove the keys (backtrack) and try the next column.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all placements — O(n^n) or C(n², n)
Try every way to place n queens, check validity. Astronomically slow.

### 🟢 Optimal — backtracking with sets — O(n!) worst
One queen per row (rows can't clash automatically). For each row, try safe columns using three sets for O(1) attack checks. Prune deeply.

---

## Pseudocode (Optimal)

```
function solveNQueens(n):
    cols, diag, anti = sets()
    board = empty
    result = []
    dfs(row=0)
    return result

function dfs(row):
    if row == n: record board; return
    for col in 0..n-1:
        if col in cols or (row-col) in diag or (row+col) in anti: continue
        place queen; add col, row-col, row+col to sets
        dfs(row+1)
        remove queen and keys        ← backtrack
```

---

## Python Code

```python
def solve_n_queens(n: int) -> list[list[str]]:
    cols, diag, anti = set(), set(), set()
    placement = []          # placement[row] = col
    result = []

    def dfs(row: int):
        if row == n:
            board = ["." * c + "Q" + "." * (n - c - 1) for c in placement]
            result.append(board)
            return
        for col in range(n):
            if col in cols or (row - col) in diag or (row + col) in anti:
                continue                        # under attack → skip
            cols.add(col); diag.add(row - col); anti.add(row + col)
            placement.append(col)
            dfs(row + 1)
            placement.pop()                     # backtrack
            cols.remove(col); diag.remove(row - col); anti.remove(row + col)

    dfs(0)
    return result


# Test
sols = solve_n_queens(4)
print(len(sols))          # 2
for row in sols[0]:
    print(row)
# .Q..
# ...Q
# Q...
# ..Q.
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Backtracking | O(n!) worst | O(n) sets + recursion |

---

## Edge Cases to remember

- **One queen per row** — iterating rows removes row-conflicts automatically; you only check column + 2 diagonals.
- **Diagonal keys**: `r − c` (main ↘) and `r + c` (anti ↙) are constant along each diagonal — the O(1) trick.
- **Backtrack fully** — remove all three keys and the placement on return.
- **n = 2, 3** → 0 solutions; **n = 1** → 1. Good sanity checks.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | N-Queens II (count only, LC 52) | Same, return count |
| 2 | Sudoku Solver (LC 37) | Backtracking + constraint sets |
| 3 | Rat in a Maze | DFS + backtrack |
| 4 | Permutations (LC 46) | Choose/undo |
| 5 | Word Search (LC 79) | DFS on grid |
| 6 | Combination Sum II (Q5) | Backtracking |
| 7 | Palindrome Partitioning (LC 131) | DFS partitions |
| 8 | Knight's tour | Backtracking |

---

## The ONE trick to remember

```
"ONE QUEEN PER ROW; PRUNE WITH cols, (r−c), (r+c) SETS; BACKTRACK."
```

Rows are handled by recursing one per level. A cell is safe if its column and both diagonal keys (`r−c`, `r+c`) are unused. Add on place, remove on backtrack — O(1) conflict checks make it fast.

> Memory hook: "Row by row; column and two diagonals are the only enemies."

---
