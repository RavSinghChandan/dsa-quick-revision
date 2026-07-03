# Q7 — Bellman-Ford Algorithm

## 1. What is this question actually asking?

Find the shortest path from a single source to all other nodes in a weighted graph, even when edge weights are negative. Unlike Dijkstra, Bellman-Ford handles negative weights but is slower. It also detects negative-weight cycles — paths where following a cycle reduces the total path cost indefinitely, making shortest path undefined.

## 2. Pattern

Dynamic Programming on Edges — relax all edges V-1 times; on the Vth relaxation, if any distance still updates, there's a negative cycle

## 3. Understand with a Diagram

```
Graph: 5 nodes, edges:
0→1 (weight -1)
0→2 (weight 4)
1→2 (weight 3)
1→3 (weight 2)
1→4 (weight 2)
3→2 (weight 5)
3→1 (weight 1)  ← negative path direction
4→3 (weight -3)

Initial dist = [0, inf, inf, inf, inf]  (src=0)

Iteration 1 (relax ALL edges):
0→1: dist[1] = min(inf, 0+(-1)) = -1
0→2: dist[2] = min(inf, 0+4) = 4
1→2: dist[2] = min(4, -1+3) = 2
1→3: dist[3] = min(inf, -1+2) = 1
1→4: dist[4] = min(inf, -1+2) = 1
3→2: dist[2] = min(2, 1+5) = 2 (no change)
3→1: dist[1] = min(-1, 1+1) = -1 (no change)
4→3: dist[3] = min(1, 1+(-3)) = -2
dist = [0, -1, 2, -2, 1]

Iteration 2 (relax ALL edges again):
4→3: dist[3] = min(-2, 1-3) = -2 (no change)
3→1: dist[1] = min(-1, -2+1) = -1 (no change)
... most no longer update ...
dist = [0, -1, 2, -2, 1]

After V-1=4 iterations, dist = [0, -1, 2, -2, 1]

Negative cycle check (5th iteration):
If any edge (u,v,w) has dist[v] > dist[u]+w → negative cycle exists
(No such update here → no negative cycle)

Why V-1 iterations?
Shortest path in a graph without negative cycles has at most V-1 edges.
Each iteration guarantees shortest paths of increasing edge count.
After V-1 iterations, all shortest paths are found.
```

## 4. Brute Force → Better → Optimal

### Brute Force — DFS with memoization (for negative edges)
Dijkstra fails with negative weights. BFS doesn't apply for weighted graphs. Bellman-Ford IS the solution for graphs with negative weights.

### Bellman-Ford — The Standard Algorithm
**Idea:** Initialize dist[src]=0, all others=infinity. Repeat V-1 times: for every edge (u,v,w), if dist[u]+w < dist[v], update dist[v]. After V-1 iterations, run one more to check for negative cycles.

```
dist = [+inf] * V
dist[src] = 0
edges = list of all (u, v, w)

for _ in range(V - 1):
    for (u, v, w) in edges:
        if dist[u] != +inf and dist[u] + w < dist[v]:
            dist[v] = dist[u] + w

// Negative cycle detection
for (u, v, w) in edges:
    if dist[u] != +inf and dist[u] + w < dist[v]:
        print("Negative cycle detected")
        return None

return dist
```

O(VE) time, O(V) space.

## 5. Pseudocode (Optimal)

```
function bellmanFord(V, edges, src):
    dist = [+inf] * V
    dist[src] = 0
    
    // Relax all edges V-1 times
    for i from 1 to V-1:
        for (u, v, w) in edges:
            if dist[u] != +inf and dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
    
    // Check for negative cycle
    for (u, v, w) in edges:
        if dist[u] != +inf and dist[u] + w < dist[v]:
            return "Negative cycle exists"
    
    return dist
```

## 6. Python Code

```python
def bellman_ford(V, edges, src):
    """
    edges: list of (u, v, weight)
    Returns (dist, has_negative_cycle)
    """
    dist = [float('inf')] * V
    dist[src] = 0
    
    # Relax all edges V-1 times
    for i in range(V - 1):
        updated = False
        for u, v, w in edges:
            if dist[u] != float('inf') and dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                updated = True
        if not updated:
            break  # early termination if no updates
    
    # Check for negative cycle
    has_neg_cycle = False
    for u, v, w in edges:
        if dist[u] != float('inf') and dist[u] + w < dist[v]:
            has_neg_cycle = True
            break
    
    return dist, has_neg_cycle


# Test 1 — standard example
V = 5
edges = [
    (0, 1, -1), (0, 2, 4),
    (1, 2, 3), (1, 3, 2), (1, 4, 2),
    (3, 2, 5), (3, 1, 1), (4, 3, -3)
]
dist, neg = bellman_ford(V, edges, 0)
print(dist)    # [0, -1, 2, -2, 1]
print(neg)     # False

# Test 2 — graph with negative cycle
V2 = 3
edges2 = [(0, 1, 1), (1, 2, -2), (2, 0, -2)]  # 0→1→2→0 sum = -3
dist2, neg2 = bellman_ford(V2, edges2, 0)
print(neg2)   # True

# Test 3 — simple positive weights
V3 = 3
edges3 = [(0, 1, 5), (0, 2, 10), (1, 2, 3)]
dist3, _ = bellman_ford(V3, edges3, 0)
print(dist3)  # [0, 5, 8]  (0→1→2 = 8 < 0→2 = 10)

# Test 4 — disconnected node
V4 = 4
edges4 = [(0, 1, 2), (1, 2, 3)]
dist4, _ = bellman_ford(V4, edges4, 0)
print(dist4)  # [0, 2, 5, inf]
```

## 7. Complexity Table

| Approach      | Time   | Space | Notes                              |
|---------------|--------|-------|------------------------------------|
| Bellman-Ford  | O(VE)  | O(V)  | Handles negative weights           |
| Dijkstra      | O((V+E) log V) | O(V) | Faster but NO negative weights |
| Floyd-Warshall| O(V³)  | O(V²) | All pairs shortest path            |

## 8. Edge Cases to Remember

- Negative cycle — Vth iteration still updates → print warning, return None
- Disconnected nodes — stay at +inf (unreachable)
- All weights positive — Dijkstra preferred (faster); Bellman-Ford still correct
- Early termination — if a full iteration makes no updates, converged early
- Source unreachable to some nodes — dist stays +inf (guard against inf + w)
- Directed vs undirected — for undirected, each undirected edge becomes two directed edges

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Network Delay Time                    | Dijkstra (positive weights) or Bellman-Ford         | LC 743     |
| Cheapest Flights Within K Stops       | Bellman-Ford with K iterations                      | LC 787     |
| Negative Cycle Detection              | The Vth iteration check                             | —          |
| Floyd-Warshall                        | All-pairs shortest path                             | —          |
| SPFA (Shortest Path Faster Algorithm) | Queue-based Bellman-Ford optimization               | —          |
| Find Negative Cycle                   | Graph theory                                        | —          |
| Arbitrage Detection (Finance)         | Log-transform, find negative cycle                  | —          |
| Minimum Cost to Reach Destination     | DP on edges (same as Bellman-Ford)                  | LC 787     |

## 10. The ONE Trick to Remember

**"Relax all edges V-1 times — shortest path uses at most V-1 edges; Vth relaxation finds negative cycles."**

A shortest path in any graph without negative cycles has at most V-1 edges. Each Bellman-Ford iteration guarantees correct shortest paths using at most i edges. After V-1 iterations, all shortest paths are found. If the Vth iteration still updates a distance, it means there's a negative cycle allowing indefinitely shorter paths — report it. The key: unlike Dijkstra, Bellman-Ford relaxes ALL edges each iteration, not just one node's edges.
