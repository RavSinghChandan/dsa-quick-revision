# Day 8 — Strings & Graphs · Q5 · Bipartite Graph (2-Coloring)

---

## What is this question actually asking?

Can the graph's nodes be split into **two groups** so every edge connects nodes in **different** groups? (Equivalently: is it 2-colorable / has no odd cycle?)

```
0-1, 1-2, 2-3, 3-0 (a 4-cycle) → bipartite (True)
triangle 0-1,1-2,2-0            → NOT bipartite (odd cycle)
```

> Tests **2-coloring via BFS/DFS**: color a node, force neighbors the opposite color; a conflict means not bipartite.

---

## Pattern

```
PATTERN: Graph coloring via BFS/DFS (2 colors, detect conflict)
```

Whenever you see: *"split into two sets / no same-group edge / 2-colorable / odd cycle"* → color each unvisited node, propagate the opposite color to neighbors. Any neighbor already colored the same → not bipartite.

---

## Understand with a diagram

```
color[] = -1 (uncolored)
for each uncolored node: BFS/DFS, assign color 0
  neighbor uncolored → give opposite color, enqueue
  neighbor same color as current → CONFLICT → not bipartite

Handle disconnected graphs by looping over all nodes.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — try all 2-partitions — O(2^n)
Check every assignment. Infeasible.

### 🟢 Optimal — BFS/DFS 2-coloring — O(V + E)
One traversal colors and checks simultaneously.

---

## Pseudocode (Optimal — BFS)

```
function is_bipartite(graph):
    color = [-1] * n
    for start in range(n):
        if color[start] != -1: continue
        color[start] = 0
        queue = [start]
        while queue:
            u = pop
            for v in graph[u]:
                if color[v] == -1:
                    color[v] = 1 - color[u]     ← opposite color
                    queue.push(v)
                elif color[v] == color[u]:
                    return False                ← conflict
    return True
```

---

## Python Code

```python
from collections import deque

def is_bipartite(graph: list[list[int]]) -> bool:
    n = len(graph)
    color = [-1] * n
    for start in range(n):
        if color[start] != -1:
            continue
        color[start] = 0
        q = deque([start])
        while q:
            u = q.popleft()
            for v in graph[u]:
                if color[v] == -1:
                    color[v] = 1 - color[u]       # opposite color
                    q.append(v)
                elif color[v] == color[u]:
                    return False                  # same-color edge → odd cycle
    return True


# Test  (adjacency list)
print(is_bipartite([[1,3],[0,2],[1,3],[0,2]]))   # True  (4-cycle)
print(is_bipartite([[1,2],[0,2],[0,1]]))         # False (triangle)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| BFS/DFS coloring | O(V + E) | O(V) |

---

## Edge Cases to remember

- **Disconnected graph** — loop over ALL nodes as potential starts.
- **Self-loop** → never bipartite (an edge to itself, same color).
- **Odd cycle ⇔ not bipartite** — the underlying theorem.
- **BFS or DFS** both work; DFS uses recursion, BFS a queue.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Is Graph Bipartite? (LC 785) | This problem |
| 2 | Possible Bipartition (LC 886) | Dislikes graph |
| 3 | Detect odd cycle | Same |
| 4 | Graph coloring (2 colors) | 2-coloring |
| 5 | Flood fill (LC 733) | BFS/DFS color |
| 6 | Number of connected components | Traversal |
| 7 | Course schedule (Q8) | Graph traversal |
| 8 | Team formation by conflicts | Bipartition |

---

## The ONE trick to remember

```
"2-COLOR VIA BFS/DFS; NEIGHBOR SAME COLOR ⇒ NOT BIPARTITE. LOOP ALL COMPONENTS."
```

Assign opposite colors across edges. A conflict (a neighbor already the same color) means an odd cycle exists → not bipartite. Iterate over every node to cover disconnected components.

> Memory hook: "Paint neighbors the opposite color — a clash means it can't be split in two."

---
