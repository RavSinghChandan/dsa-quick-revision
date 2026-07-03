# Q6 — Dijkstra's Algorithm

## 1. What is this question actually asking?

Given a weighted directed graph with non-negative edge weights and a source node, find the shortest path from the source to all other nodes. Dijkstra's algorithm extends BFS by using a min-heap (priority queue) instead of a regular queue — always processing the node with the smallest known distance next.

## 2. Pattern

Min-Heap (Priority Queue) + Greedy — always expand the unvisited node with the smallest current distance; relax all its edges

## 3. Understand with a Diagram

```
Graph (weighted directed):
    (4)      (1)
0 ------→ 1 ------→ 3
|          |         |
|(8)   (2)|       (5)|
|          ↓         ↓
└--------→ 2 ------→ 4
    (8)      (3)

Edges: 0→1(4), 0→2(8), 1→2(2), 1→3(1), 2→4(3), 3→4(5)

Dijkstra from source=0:
Initial dist = [0, inf, inf, inf, inf]
heap = [(0, 0)]  // (dist, node)

Step 1: pop (0,0)
  Relax 0→1: dist[1] = min(inf, 0+4) = 4 → push (4,1)
  Relax 0→2: dist[2] = min(inf, 0+8) = 8 → push (8,2)
  heap = [(4,1), (8,2)]

Step 2: pop (4,1)
  Relax 1→2: dist[2] = min(8, 4+2) = 6 → push (6,2) [old (8,2) stays but will be skipped]
  Relax 1→3: dist[3] = min(inf, 4+1) = 5 → push (5,3)
  heap = [(5,3), (6,2), (8,2)]

Step 3: pop (5,3)
  Relax 3→4: dist[4] = min(inf, 5+5) = 10 → push (10,4)
  heap = [(6,2), (8,2), (10,4)]

Step 4: pop (6,2)
  Relax 2→4: dist[4] = min(10, 6+3) = 9 → push (9,4)
  heap = [(8,2), (9,4), (10,4)]

Step 5: pop (8,2)
  Node 2 already processed (dist[2]=6 < 8) → SKIP
  heap = [(9,4), (10,4)]

Step 6: pop (9,4)
  Node 4 has no outgoing edges. Done.

Final dist = [0, 4, 6, 5, 9]
  0→0: 0
  0→1: 4 (direct)
  0→2: 6 (0→1→2)
  0→3: 5 (0→1→3)
  0→4: 9 (0→1→2→4)
```

## 4. Brute Force → Better → Optimal

### Brute Force — Relax all edges V-1 times
**Idea:** This is actually Bellman-Ford, not Dijkstra. For each of V-1 iterations, relax all edges. Handles negative weights but O(VE).

### Better — Array-based Dijkstra
**Idea:** Use a simple array for distances. Each iteration, find the unvisited node with minimum distance (O(V) scan), then relax all its edges.

```
dist = [inf]*V; dist[src]=0; visited=[False]*V

for _ in range(V):
    u = unvisited node with min dist  // O(V) scan
    visited[u] = True
    for (v, w) in adj[u]:
        dist[v] = min(dist[v], dist[u]+w)
```

O(V²) time. Good for dense graphs.

### Optimal — Min-Heap (Priority Queue)
**Idea:** Use a min-heap keyed by (distance, node). Always process the node with the smallest current distance. When a shorter path is found, push the new (distance, node) to the heap. Lazy deletion: skip nodes already processed (when popped dist > known dist).

```
dist = [inf]*V; dist[src]=0
heap = [(0, src)]

while heap:
    d, u = heappop(heap)
    if d > dist[u]: continue  // outdated entry
    for (v, w) in adj[u]:
        if dist[u] + w < dist[v]:
            dist[v] = dist[u] + w
            heappush(heap, (dist[v], v))
```

O((V+E) log V) time, O(V+E) space. Better for sparse graphs.

## 5. Pseudocode (Optimal)

```
function dijkstra(adj, V, src):
    dist = [+inf] * V
    dist[src] = 0
    heap = min-heap
    push (0, src) to heap
    
    while heap not empty:
        (d, u) = pop min from heap
        if d > dist[u]: continue  // stale entry
        
        for (v, weight) in adj[u]:
            new_dist = dist[u] + weight
            if new_dist < dist[v]:
                dist[v] = new_dist
                push (dist[v], v) to heap
    
    return dist
```

## 6. Python Code

```python
import heapq


def dijkstra(V, adj, src):
    """adj[u] = list of (v, weight)"""
    dist = [float('inf')] * V
    dist[src] = 0
    heap = [(0, src)]
    
    while heap:
        d, u = heapq.heappop(heap)
        
        if d > dist[u]:
            continue  # stale entry — skip
        
        for v, weight in adj[u]:
            new_dist = dist[u] + weight
            if new_dist < dist[v]:
                dist[v] = new_dist
                heapq.heappush(heap, (new_dist, v))
    
    return dist


# Test 1 — from the diagram example
from collections import defaultdict
V = 5
adj = defaultdict(list)
adj[0].extend([(1, 4), (2, 8)])
adj[1].extend([(2, 2), (3, 1)])
adj[2].extend([(4, 3)])
adj[3].extend([(4, 5)])

dist = dijkstra(V, adj, 0)
print(dist)   # [0, 4, 6, 5, 9]

# Test 2 — simple triangle
V2 = 3
adj2 = defaultdict(list)
adj2[0].extend([(1, 1), (2, 4)])
adj2[1].extend([(2, 2)])
dist2 = dijkstra(V2, adj2, 0)
print(dist2)   # [0, 1, 3]  (0→1→2 = 3, better than 0→2 = 4)

# Test 3 — disconnected node
V3 = 3
adj3 = defaultdict(list)
adj3[0].append((1, 5))
dist3 = dijkstra(V3, adj3, 0)
print(dist3)   # [0, 5, inf]  (node 2 unreachable)
```

## 7. Complexity Table

| Approach              | Time            | Space   | Notes                         |
|-----------------------|-----------------|---------|-------------------------------|
| Array-based Dijkstra  | O(V²)           | O(V)    | Good for dense graphs         |
| Min-heap Dijkstra     | O((V+E) log V)  | O(V+E)  | Good for sparse graphs        |
| Bellman-Ford          | O(VE)           | O(V)    | Handles negative weights      |

## 8. Edge Cases to Remember

- Negative edge weights — Dijkstra FAILS with negative weights; use Bellman-Ford instead
- Unreachable nodes — dist stays inf (no path exists)
- Multiple shortest paths — algorithm finds one; all have same minimum distance
- Self-loops — skipped because dist[u]+0 = dist[u], not less than dist[u]
- Stale heap entries — crucial to skip with `if d > dist[u]: continue`
- Disconnected graph — nodes in different components stay at inf

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Network Delay Time                    | Dijkstra for max shortest path from src             | LC 743     |
| Path with Minimum Effort              | Dijkstra with custom edge weight                    | LC 1631    |
| Cheapest Flights K Stops              | Modified Dijkstra with stop count                   | LC 787     |
| Path with Maximum Probability         | Dijkstra with max-heap for probabilities            | LC 1514    |
| Find the City with Fewest Reachables  | All-pairs shortest path (Floyd-Warshall)            | LC 1334    |
| Minimum Weighted Subgraph             | Dijkstra from both directions                       | LC 2203    |
| Swim in Rising Water                  | Dijkstra/binary search on max elevation             | LC 778     |
| Bellman-Ford / Negative Cycles        | When negative weights exist                         | —          |

## 10. The ONE Trick to Remember

**"Min-heap by distance — always expand cheapest known node; skip stale heap entries with d > dist[u]."**

Dijkstra is greedy BFS for weighted graphs. Instead of a FIFO queue, use a min-heap prioritizing smallest distance. When you pop (d, u) and d > dist[u], it's a stale entry from before you found a shorter path — skip it. When you do process a node, relax all its edges. Works only for non-negative weights because once a node is popped with the minimum distance, that distance is final.
