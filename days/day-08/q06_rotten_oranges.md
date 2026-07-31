# Day 8 — Strings & Graphs · Q6 · Rotten Oranges

---

## What is this question actually asking?

A grid has fresh (1), rotten (2), and empty (0) cells. Each minute, rotten oranges rot their 4-directional fresh neighbors. Return the **minutes** until none are fresh, or `-1` if some can never rot.

```
[[2,1,1],[1,1,0],[0,1,1]] → 4
[[2,1,1],[0,1,1],[1,0,1]] → -1  (bottom-left orange unreachable)
```

> The definitive **multi-source BFS** problem. All initial rotten cells start at level 0; BFS spreads level by level = minutes.

---

## Pattern

```
PATTERN: Multi-Source BFS (all sources at level 0, count levels)
```

Whenever you see: *"spread/infection from multiple starts, time to reach all"* → enqueue ALL sources first, then BFS level by level. The number of levels = the time; leftover unreached cells → impossible.

---

## Understand with a diagram

```
Enqueue every rotten cell (level 0). Count fresh oranges.

Each BFS level = 1 minute:
  pop all cells at current level, rot their fresh 4-neighbors, enqueue them.
minutes = number of levels processed (minus the initial).

At the end, if fresh_count > 0 → return -1, else minutes.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — repeat full-grid passes — O((R·C)²)
Scan the grid each minute rotting neighbors until stable.

### 🟢 Optimal — multi-source BFS — O(R·C)
Enqueue all rotten cells, BFS outward once. Each cell processed a constant number of times.

---

## Pseudocode (Optimal)

```
function oranges_rotting(grid):
    queue = all rotten cells (level 0)
    fresh = count of fresh cells
    minutes = 0
    while queue and fresh > 0:
        minutes++
        for each cell in current level:
            for 4 neighbors that are fresh:
                make rotten; fresh--; enqueue
    return minutes if fresh == 0 else -1
```

---

## Python Code

```python
from collections import deque

def oranges_rotting(grid: list[list[int]]) -> int:
    R, C = len(grid), len(grid[0])
    q = deque()
    fresh = 0
    for r in range(R):
        for c in range(C):
            if grid[r][c] == 2:
                q.append((r, c))       # source at level 0
            elif grid[r][c] == 1:
                fresh += 1

    minutes = 0
    dirs = [(1,0),(-1,0),(0,1),(0,-1)]
    while q and fresh > 0:
        minutes += 1
        for _ in range(len(q)):        # process one level
            r, c = q.popleft()
            for dr, dc in dirs:
                nr, nc = r + dr, c + dc
                if 0 <= nr < R and 0 <= nc < C and grid[nr][nc] == 1:
                    grid[nr][nc] = 2   # rot it
                    fresh -= 1
                    q.append((nr, nc))
    return minutes if fresh == 0 else -1


# Test
print(oranges_rotting([[2,1,1],[1,1,0],[0,1,1]]))   # 4
print(oranges_rotting([[2,1,1],[0,1,1],[1,0,1]]))   # -1
print(oranges_rotting([[0,2]]))                     # 0
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Multi-source BFS | O(R·C) | O(R·C) queue |

---

## Edge Cases to remember

- **Enqueue ALL rotten cells first** — this is the "multi-source" part; single-source BFS gives wrong times.
- **Count fresh; return -1 if any remain** — some fresh oranges may be unreachable.
- **No fresh at start** → 0 minutes (don't over-count).
- **Level-by-level** (`for _ in range(len(q))`) — increment minutes once per level, not per cell.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Rotting Oranges (LC 994) | This problem |
| 2 | Walls and Gates (LC 286) | Multi-source BFS |
| 3 | 01 Matrix / nearest 0 (LC 542) | Multi-source BFS |
| 4 | Shortest bridge (LC 934) | BFS from island |
| 5 | Burn binary tree (Day 6 Q4) | BFS levels |
| 6 | Flood fill (LC 733) | BFS/DFS grid |
| 7 | Number of islands (LC 200) | Grid traversal |
| 8 | As Far From Land (LC 1162) | Multi-source BFS |

---

## The ONE trick to remember

```
"ENQUEUE ALL SOURCES AT LEVEL 0; BFS LEVEL-BY-LEVEL = MINUTES; LEFTOVER FRESH → -1."
```

Start BFS from every rotten cell simultaneously. Each BFS level is one minute of spreading. Track the fresh count; if any fresh remain unreachable at the end, return -1.

> Memory hook: "All the rot starts together — count the ripples until nothing fresh is left."

---
