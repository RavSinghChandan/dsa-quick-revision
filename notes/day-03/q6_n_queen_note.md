# ⚡ Quick Note · Day 3 — SW & Backtracking · Q6 · N-Queen
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Place n queens, none attacking (row/col/diagonal)."

## Pattern
`Backtracking row-by-row + col/diag/anti-diag sets`

## Visual in your head
```
one queen per row.
cell (r,c) safe if c ∉ cols, (r-c) ∉ diag, (r+c) ∉ anti
place → recurse(row+1) → backtrack (remove keys)
```

## The trick (say it out loud)
> "One queen per row. Prune with cols, (r−c) diag, (r+c) anti-diag sets. Backtrack fully."

## Code skeleton
```python
for col in range(n):
    if col in cols or (r-col) in diag or (r+col) in anti: continue
    add keys; dfs(r+1); remove keys
```

## Complexity
- Time O(n!) · Space O(n)

## This trick solves more
N-Queens II (52) · Sudoku Solver (37) · Rat in Maze · Permutations (46) · Word Search (79)
