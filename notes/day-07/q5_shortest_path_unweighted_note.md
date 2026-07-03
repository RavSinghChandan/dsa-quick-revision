# Q5 — Shortest Path Unweighted Graph (2-min card)

**Find minimum number of edges from source to all nodes (unweighted graph).**

**Pattern:** BFS — level order = distance; first visit = shortest path guaranteed

```
Graph: 0-[1,2], 1-[0,3], 2-[0,3], 3-[1,2]

BFS from 0:
queue=[0] dist={0:0}
dequeue 0 → visit 1(dist=1), 2(dist=1) → queue=[1,2]
dequeue 1 → visit 3(dist=2) → queue=[2,3]
dequeue 2 → 3 already visited
dequeue 3 → done

dist = [0, 1, 1, 2] ✓
```

**The trick:** "BFS explores level by level — first visit is always shortest in unweighted graphs."

```python
from collections import deque
dist = [-1]*V; dist[src] = 0
queue = deque([src])
while queue:
    u = queue.popleft()
    for v in adj[u]:
        if dist[v] == -1:
            dist[v] = dist[u]+1; queue.append(v)
return dist
```

**Complexity:** Time O(V+E) | Space O(V)

**Same pattern solves:** Word Ladder (LC 127), Rotten Oranges (LC 994), 01 Matrix (LC 542), Shortest Binary Matrix (LC 1091), Open Lock (LC 752), Snakes & Ladders (LC 909), Jump Game III (LC 1306), Min Jumps (LC 45).
