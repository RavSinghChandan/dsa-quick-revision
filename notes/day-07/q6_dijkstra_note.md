# Q6 — Dijkstra's Algorithm (2-min card)

**Shortest path from source to all nodes in weighted graph (non-negative weights).**

**Pattern:** Min-Heap + Greedy — always expand lowest-cost known node; skip stale entries

```
Graph: 0→1(4), 0→2(8), 1→2(2), 1→3(1), 2→4(3), 3→4(5)

heap=[(0,0)], dist=[0,∞,∞,∞,∞]
pop(0,0): relax 1→4, 2→8. heap=[(4,1),(8,2)]
pop(4,1): relax 2→6(update!), 3→5. heap=[(5,3),(6,2),(8,2)]
pop(5,3): relax 4→10. heap=[(6,2),(8,2),(10,4)]
pop(6,2): relax 4→9(update!). heap=[(8,2),(9,4),(10,4)]
pop(8,2): 8>dist[2]=6 → SKIP (stale)
pop(9,4): done.

dist=[0,4,6,5,9] ✓
```

**The trick:** "Pop smallest; if d>dist[u] skip (stale); else relax neighbors."

```python
import heapq
dist = [float('inf')]*V; dist[src]=0; heap=[(0,src)]
while heap:
    d,u = heapq.heappop(heap)
    if d > dist[u]: continue
    for v,w in adj[u]:
        if dist[u]+w < dist[v]:
            dist[v]=dist[u]+w; heapq.heappush(heap,(dist[v],v))
```

**Complexity:** Time O((V+E) log V) | Space O(V+E)

**Same pattern solves:** Network Delay Time (LC 743), Min Effort Path (LC 1631), Cheapest Flights K Stops (LC 787), Max Probability Path (LC 1514), Find City Fewest Reachable (LC 1334), Swim Rising Water (LC 778), Min Weighted Subgraph (LC 2203).
