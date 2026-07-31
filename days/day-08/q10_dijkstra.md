# Day 8 — Strings & Graphs · Q10 · Dijkstra's Algorithm

---

## What is this question actually asking?

Find the **shortest path distances** from a source to all nodes in a graph with **non-negative** edge weights.

```
graph (node: [(neighbor, weight)])  source 0
→ dist[] = shortest distance from 0 to every node
```

> The canonical weighted shortest-path algorithm. A **min-heap** always expands the closest unfinalized node next (greedy + relaxation).

---

## Pattern

```
PATTERN: Greedy + Min-Heap (priority queue) — expand nearest, relax edges
```

Whenever you see: *"shortest path with non-negative weights"* → keep tentative distances; repeatedly pop the closest node from a min-heap; relax its edges (`dist[v] = min(dist[v], dist[u] + w)`) and push updated neighbors.

---

## Understand with a diagram

```
dist[src]=0, others = ∞. heap = [(0, src)].

pop (d, u): if d > dist[u]: skip (stale)
            for (v, w) in adj[u]:
                if dist[u] + w < dist[v]:
                    dist[v] = dist[u] + w
                    push (dist[v], v)

The heap always gives the globally-nearest unfinalized node → its distance is final.
```

---

## Brute Force → Better → Optimal

### 🔴 Bellman-Ford — O(V·E)
Works with negatives but slower.

### 🟢 Optimal — Dijkstra with binary heap — O((V + E) log V)
Non-negative weights let the greedy "nearest first" be correct. Heap gives log-time min extraction.

---

## Pseudocode (Optimal)

```
function dijkstra(n, adj, src):
    dist = [inf]*n; dist[src] = 0
    heap = [(0, src)]
    while heap:
        d, u = pop_min(heap)
        if d > dist[u]: continue          ← stale entry
        for (v, w) in adj[u]:
            nd = d + w
            if nd < dist[v]:
                dist[v] = nd
                push (nd, v)
    return dist
```

---

## Python Code

```python
import heapq

def dijkstra(n: int, adj: list[list[tuple]], src: int) -> list[int]:
    INF = float('inf')
    dist = [INF] * n
    dist[src] = 0
    heap = [(0, src)]                     # (distance, node)
    while heap:
        d, u = heapq.heappop(heap)
        if d > dist[u]:                   # outdated entry → skip
            continue
        for v, w in adj[u]:
            nd = d + w
            if nd < dist[v]:              # relaxation
                dist[v] = nd
                heapq.heappush(heap, (nd, v))
    return dist


# Test
#   0 --4--> 1 --1--> 2      0 --1--> 2 (via edge weight 1?)
adj = [
    [(1, 4), (2, 1)],   # 0 -> 1 (4), 0 -> 2 (1)
    [(3, 1)],           # 1 -> 3 (1)
    [(1, 2), (3, 5)],   # 2 -> 1 (2), 2 -> 3 (5)
    [],
]
print(dijkstra(4, adj, 0))   # [0, 3, 1, 4]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Binary heap | O((V + E) log V) | O(V + E) |

---

## Edge Cases to remember

- **Non-negative weights only** — Dijkstra breaks with negative edges (use Bellman-Ford / Q12 Floyd-Warshall then).
- **Skip stale heap entries** — `if d > dist[u]: continue`; you push duplicates instead of decrease-key.
- **Unreachable nodes** stay `inf`.
- **Path reconstruction** — keep a `parent[]` updated during relaxation.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Network Delay Time (LC 743) | Dijkstra |
| 2 | Cheapest Flights K Stops (Q11) | Modified BFS/Dijkstra |
| 3 | Path With Minimum Effort (LC 1631) | Dijkstra on grid |
| 4 | Swim in Rising Water (LC 778) | Dijkstra variant |
| 5 | Shortest path in weighted grid | Dijkstra |
| 6 | Minimum cost to reach destination | Dijkstra |
| 7 | K shortest paths | Modified Dijkstra |
| 8 | Path with max probability (LC 1514) | Max-heap Dijkstra |

---

## The ONE trick to remember

```
"MIN-HEAP: POP THE NEAREST, RELAX ITS EDGES, PUSH IMPROVED NEIGHBORS."
```

With non-negative weights, the closest unfinalized node's distance is already optimal — so a min-heap greedily finalizes nodes in increasing distance, relaxing edges as it goes. Skip stale heap entries.

> Memory hook: "Always grow the frontier from the closest known point outward."

---
