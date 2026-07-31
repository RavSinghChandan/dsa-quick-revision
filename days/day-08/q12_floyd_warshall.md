# Day 8 — Strings & Graphs · Q12 · Floyd-Warshall (All-Pairs Shortest Path)

---

## What is this question actually asking?

Compute the shortest distance between **every pair** of nodes in a weighted graph (weights may be negative, but no negative cycles).

```
dist[i][j] = shortest path from i to j for ALL i, j
```

> The all-pairs shortest path algorithm. Beautifully simple: try every node `k` as an intermediate, and see if going `i → k → j` beats the current `i → j`.

---

## Pattern

```
PATTERN: Dynamic Programming over intermediate nodes (triple loop)
```

Whenever you see: *"shortest path between all pairs / transitive closure / reachability matrix"* → DP where `dist[i][j]` is relaxed through each possible intermediate `k`: `dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])`.

---

## Understand with a diagram

```
Init dist[i][j] = edge weight (or ∞), dist[i][i] = 0.

for k in nodes:                    ← intermediate must be the OUTER loop
    for i in nodes:
        for j in nodes:
            dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])

After k iterations, dist uses intermediates {0..k}. After all k → all pairs.
```

---

## Brute Force → Better → Optimal

### 🟡 Run Dijkstra from every node — O(V · (E log V))
Good for sparse graphs with non-negative weights.

### 🟢 Floyd-Warshall — O(V³)
Simple, handles negative edges, gives the full matrix. Best for dense/small graphs.

---

## Pseudocode (Optimal)

```
function floyd_warshall(n, dist):     # dist init with edges + ∞ + 0 diagonal
    for k in 0..n-1:
        for i in 0..n-1:
            for j in 0..n-1:
                if dist[i][k] + dist[k][j] < dist[i][j]:
                    dist[i][j] = dist[i][k] + dist[k][j]
    return dist
    # negative cycle iff any dist[i][i] < 0 afterward
```

---

## Python Code

```python
def floyd_warshall(n: int, edges: list[list[int]]) -> list[list[float]]:
    INF = float('inf')
    dist = [[INF] * n for _ in range(n)]
    for i in range(n):
        dist[i][i] = 0
    for u, v, w in edges:                     # directed edges
        dist[u][v] = min(dist[u][v], w)

    for k in range(n):                        # intermediate node — OUTER loop
        for i in range(n):
            if dist[i][k] == INF:
                continue
            for j in range(n):
                if dist[i][k] + dist[k][j] < dist[i][j]:
                    dist[i][j] = dist[i][k] + dist[k][j]
    return dist


# Test
n = 4
edges = [[0,1,3],[1,2,1],[0,2,5],[2,3,2],[1,3,6]]
d = floyd_warshall(n, edges)
print(d[0][3])   # 6  (0->1->2->3 = 3+1+2)
print(d[0][2])   # 4  (0->1->2 = 3+1, better than direct 5)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Floyd-Warshall | O(V³) | O(V²) |

---

## Edge Cases to remember

- **k is the OUTERMOST loop** — the single most common bug is putting i or j outside k; the DP correctness depends on k first.
- **Negative cycle detection** — after running, `dist[i][i] < 0` for some i means a negative cycle exists.
- **Initialize diagonal to 0** and unknown pairs to ∞.
- **Skip `dist[i][k] == INF`** to avoid `inf + x` issues (and speed).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Find the City With Smallest Neighbors (LC 1334) | All-pairs then count |
| 2 | Transitive closure / reachability | Boolean Floyd-Warshall |
| 3 | Shortest path all pairs | This |
| 4 | Minimum cost to connect (dense) | APSP |
| 5 | Detect negative cycle | dist[i][i]<0 |
| 6 | Course prerequisites closure | Reachability |
| 7 | Network diameter | Max over all-pairs |
| 8 | Evaluate division (LC 399) | Path products |

---

## The ONE trick to remember

```
"TRIPLE LOOP; k OUTERMOST: dist[i][j] = min(dist[i][j], dist[i][k]+dist[k][j])."
```

Consider each node k as a possible waypoint. If routing i→j through k is shorter, take it. k must be the outer loop so that by the time you use it, shorter paths through earlier intermediates are already computed.

> Memory hook: "Let every city be a possible layover — keep the cheapest connection."

---
