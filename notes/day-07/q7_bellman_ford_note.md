# Q7 — Bellman-Ford Algorithm (2-min card)

**Shortest paths from source in weighted graph — handles negative weights, detects negative cycles.**

**Pattern:** Relax all edges V-1 times; Vth relaxation → negative cycle

```
edges: 0→1(-1), 0→2(4), 1→2(3), 1→3(2), 1→4(2), 3→2(5), 3→1(1), 4→3(-3)
src=0

dist = [0, inf, inf, inf, inf]
After iter 1: [0, -1, 2, 1, 1]   (after each edge relaxed)
After iter 2: [0, -1, 2, -2, 1]  (4→3 improves dist[3])
After iter 3: [0, -1, 2, -2, 1]  (converged)

No update in V-th iter → no negative cycle.
Final: [0, -1, 2, -2, 1] ✓
```

**The trick:** "Relax ALL edges V-1 times; if Vth still updates → negative cycle exists."

```python
dist = [float('inf')]*V; dist[src] = 0
for _ in range(V-1):
    for u,v,w in edges:
        if dist[u] != float('inf') and dist[u]+w < dist[v]:
            dist[v] = dist[u]+w
# Check: another pass, any update → negative cycle
```

**Complexity:** Time O(VE) | Space O(V)

**Same pattern solves:** Network Delay Time (LC 743), Cheapest Flights K Stops (LC 787, BF with K iters), Negative Cycle Detection, Floyd-Warshall (all-pairs), SPFA optimization, Arbitrage detection, Min Cost Reach Dest.
