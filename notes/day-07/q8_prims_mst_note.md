# Q8 — Prim's MST Algorithm (2-min card)

**Find Minimum Spanning Tree — subset of V-1 edges connecting all nodes with minimum total weight.**

**Pattern:** Min-Heap + Greedy — always add cheapest edge from tree to new node

```
Graph: 0-1(2), 0-3(6), 1-2(3), 1-3(5), 1-4(1), 2-5(4), 3-4(3), 4-5(2)

Start at 0: heap=[(0,0)]
pop(0,0): visit 0, push (2,1),(6,3)
pop(2,1): visit 1 +2, push (3,2),(5,3),(1,4)
pop(1,4): visit 4 +1, push (3,3),(2,5)
pop(2,5): visit 5 +2, push (4,2)
pop(3,2): visit 2 +3
pop(3,3): visit 3 +3

Total MST cost: 0+2+1+2+3+3 = 11 ✓
```

**The trick:** "Min-heap of (edge_weight, node); skip visited; cheapest crossing edge grows the tree."

```python
import heapq
heap=[(0,0)]; visited=[False]*V; cost=[inf]*V; cost[0]=0; total=0
while heap:
    w,u = heapq.heappop(heap)
    if visited[u]: continue
    visited[u]=True; total+=w
    for v,wt in adj[u]:
        if not visited[v] and wt<cost[v]:
            cost[v]=wt; heapq.heappush(heap,(wt,v))
return total
```

**Complexity:** Time O(E log V) | Space O(V+E)

**Same pattern solves:** Kruskal's MST, Min Cost Connect Points (LC 1584), Connecting Cities (LC 1135), Optimize Water (LC 1168), Critical/Pseudo-Critical MST (LC 1489), Dijkstra (similar heap), Network Delay Time (LC 743).
