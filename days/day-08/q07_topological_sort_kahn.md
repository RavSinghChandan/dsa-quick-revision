# Day 8 — Strings & Graphs · Q7 · Topological Sort (Kahn's Algorithm)

---

## What is this question actually asking?

Order the nodes of a **DAG** (directed acyclic graph) so every edge `u → v` has `u` **before** `v`. Return one valid ordering (or detect a cycle).

```
edges: 5→0, 5→2, 4→0, 4→1, 2→3, 3→1
one valid topo order: 4 5 2 3 1 0
```

> Tests **Kahn's BFS**: repeatedly remove nodes with **in-degree 0**. If you can't order everyone, there's a cycle.

---

## Pattern

```
PATTERN: Kahn's Algorithm — BFS on in-degrees (queue of zero in-degree nodes)
```

Whenever you see: *"order tasks by dependency / valid sequence / detect cycle in a directed graph"* → compute in-degrees; start with all zero-in-degree nodes; pop one, append to order, decrement neighbors' in-degrees, enqueue any that hit 0.

---

## Understand with a diagram

```
in-degree of each node = # incoming edges.
queue = all nodes with in-degree 0.

pop u → add to order → for each u→v: indeg[v]-- ; if 0 → enqueue

If order contains all n nodes → valid topo sort.
If fewer → a cycle exists (some nodes never reach in-degree 0).
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — try permutations — O(n!)
Check each ordering for validity. Infeasible.

### 🟢 Optimal — Kahn's BFS — O(V + E)
In-degree bookkeeping + one pass. Also detects cycles for free.

---

## Pseudocode (Optimal)

```
function topo_sort(n, edges):
    build adjacency + indegree[]
    queue = [nodes with indegree 0]
    order = []
    while queue:
        u = pop
        order.append(u)
        for v in adj[u]:
            indegree[v]--
            if indegree[v] == 0: queue.push(v)
    return order if len(order) == n else []   ← [] means cycle
```

---

## Python Code

```python
from collections import deque

def topological_sort(n: int, edges: list[list[int]]) -> list[int]:
    adj = [[] for _ in range(n)]
    indeg = [0] * n
    for u, v in edges:
        adj[u].append(v)
        indeg[v] += 1

    q = deque([i for i in range(n) if indeg[i] == 0])
    order = []
    while q:
        u = q.popleft()
        order.append(u)
        for v in adj[u]:
            indeg[v] -= 1
            if indeg[v] == 0:
                q.append(v)

    return order if len(order) == n else []    # [] → cycle detected


# Test
order = topological_sort(6, [[5,0],[5,2],[4,0],[4,1],[2,3],[3,1]])
print(len(order) == 6)            # True (valid)
# verify: every edge u->v has u before v
pos = {node: i for i, node in enumerate(order)}
print(all(pos[u] < pos[v] for u, v in [[5,0],[5,2],[4,0],[4,1],[2,3],[3,1]]))  # True
print(topological_sort(2, [[0,1],[1,0]]))   # []  (cycle)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Kahn's BFS | O(V + E) | O(V + E) |

---

## Edge Cases to remember

- **Cycle detection** — if the output has fewer than n nodes, the graph has a cycle (no valid topo order).
- **Multiple valid orders** — any is acceptable; use a min-heap instead of a queue for the lexicographically smallest.
- **Disconnected DAG** — all zero-in-degree nodes seed the queue.
- **DFS alternative** — post-order + reverse also gives a topo sort.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Course Schedule I/II (LC 207/210) | Topo + cycle |
| 2 | Alien Dictionary (Q9) | Build graph + topo |
| 3 | Minimum Height Trees (LC 310) | Trim leaves (indegree-like) |
| 4 | Parallel Courses (LC 1136) | Topo levels |
| 5 | Sequence Reconstruction (LC 444) | Unique topo |
| 6 | Build order of packages | Topo |
| 7 | Task scheduling with deps | Topo |
| 8 | Longest path in DAG | Topo + DP |

---

## The ONE trick to remember

```
"REPEATEDLY REMOVE IN-DEGREE-0 NODES; FEWER THAN n ORDERED ⇒ CYCLE."
```

Kahn's: start from nodes with no prerequisites, remove them, and free their dependents. If you can order all n, it's a valid topo sort; if some stay stuck (never reach in-degree 0), a cycle exists.

> Memory hook: "Do the tasks that have no blockers first, unlock the rest — stuck tasks mean a cycle."

---
