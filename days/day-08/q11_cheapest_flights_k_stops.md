# Day 8 — Strings & Graphs · Q11 · Cheapest Flights Within K Stops

---

## What is this question actually asking?

Find the **cheapest price** from `src` to `dst` using **at most K stops** (K+1 edges). Return `-1` if unreachable within the limit.

```
n=4, flights=[[0,1,100],[1,2,100],[2,0,100],[1,3,600],[2,3,200]]
src=0, dst=3, K=1 → 700  (0→1→3)  (0→1→2→3 is cheaper=400 but needs 2 stops)
```

> A shortest-path with a **hop constraint**. The clean answer is **Bellman-Ford limited to K+1 relaxation rounds** (or a BFS/Dijkstra tracking stops).

---

## Pattern

```
PATTERN: Bellman-Ford with K+1 rounds (bounded-hop shortest path)
```

Whenever you see: *"cheapest/shortest with at most K edges/stops"* → run Bellman-Ford but only `K+1` relaxation passes, using a **snapshot** of distances each round so a single round can't chain multiple edges.

---

## Understand with a diagram

```
dist[src] = 0, rest = ∞.
Repeat K+1 times:
    temp = copy(dist)                 ← snapshot: prevents multi-edge in one round
    for each flight (u, v, w):
        if dist[u] + w < temp[v]:
            temp[v] = dist[u] + w
    dist = temp
answer = dist[dst] (or -1 if ∞)

The snapshot ensures round i uses paths with at most i edges.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — enumerate all paths ≤ K stops — exponential
DFS all routes. Blows up.

### 🟢 Optimal — Bellman-Ford K+1 rounds — O(K·E)
Each round extends reachable paths by one edge. K+1 rounds = at most K stops.

### 🟢 Alternative — Dijkstra/BFS with (cost, node, stops) — O(E·K log …)
Push stops into the state; allow revisits with fewer stops.

---

## Pseudocode (Bellman-Ford bounded)

```
function cheapest(n, flights, src, dst, K):
    dist = [inf]*n; dist[src] = 0
    for round in range(K+1):
        temp = copy(dist)
        for (u, v, w) in flights:
            if dist[u] != inf and dist[u] + w < temp[v]:
                temp[v] = dist[u] + w
        dist = temp
    return dist[dst] if dist[dst] != inf else -1
```

---

## Python Code

```python
def find_cheapest_price(n: int, flights: list[list[int]],
                        src: int, dst: int, K: int) -> int:
    INF = float('inf')
    dist = [INF] * n
    dist[src] = 0
    for _ in range(K + 1):               # at most K stops = K+1 edges
        temp = dist[:]                   # snapshot prevents multi-hop per round
        for u, v, w in flights:
            if dist[u] != INF and dist[u] + w < temp[v]:
                temp[v] = dist[u] + w
        dist = temp
    return dist[dst] if dist[dst] != INF else -1


# Test
flights = [[0,1,100],[1,2,100],[2,0,100],[1,3,600],[2,3,200]]
print(find_cheapest_price(4, flights, 0, 3, 1))   # 700
print(find_cheapest_price(4, flights, 0, 3, 2))   # 400  (0->1->2->3)
print(find_cheapest_price(3, [[0,1,100],[1,2,100],[0,2,500]], 0, 2, 0))  # 500
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Bellman-Ford K+1 | O(K · E) | O(V) |

---

## Edge Cases to remember

- **Snapshot each round (`temp = dist[:]`)** — without it, one round could relax a chain of edges, exceeding K stops. This is THE bug.
- **K stops = K+1 edges** — run `K+1` rounds.
- **Unreachable within K** → return -1.
- **Dijkstra variant** must allow revisiting a node with **fewer stops** (don't finalize on first pop).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Cheapest Flights K Stops (LC 787) | This problem |
| 2 | Bellman-Ford shortest path | Full relaxation |
| 3 | Shortest path with at most K edges | Bounded BF |
| 4 | Network Delay Time (LC 743) | Dijkstra |
| 5 | Minimum cost with constraints | Bounded DP |
| 6 | Path with limited moves | State = (node, moves) |
| 7 | K-stop reachability | BFS levels |
| 8 | Currency arbitrage (neg cycle) | Bellman-Ford |

---

## The ONE trick to remember

```
"BELLMAN-FORD, K+1 ROUNDS, SNAPSHOT DISTANCES EACH ROUND."
```

Limit relaxation to K+1 passes so paths use at most K stops. The per-round snapshot (`temp = dist[:]`) is essential — it stops a single pass from chaining multiple new edges.

> Memory hook: "One new hop per round — copy the distances so the round can't cheat ahead."

---
