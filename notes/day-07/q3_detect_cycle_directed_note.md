# Q3 — Detect Cycle in Directed Graph (2-min card)

**Does a directed graph have a cycle?**

**Pattern:** DFS with recursion stack — cycle = reaching a node still in current DFS path

```
Graph: 0→1→2→0 (cycle!)

DFS from 0:
visited={0}, rec={0} → visit 1
visited={0,1}, rec={0,1} → visit 2
visited={0,1,2}, rec={0,1,2} → visit 0
0 in rec_stack → CYCLE! ✓

DAG: 0→1→3, 0→2→3 (no cycle)
All DFS paths complete without hitting rec_stack → False
```

**The trick:** "rec_stack = currently exploring; if neighbor is in rec_stack → back edge → cycle."

```python
visited = [False]*V; rec = [False]*V
def dfs(v):
    visited[v] = rec[v] = True
    for u in adj[v]:
        if not visited[u] and dfs(u): return True
        elif rec[u]: return True
    rec[v] = False; return False
return any(dfs(v) for v in range(V) if not visited[v])
```

**Complexity:** Time O(V+E) | Space O(V)

**Same pattern solves:** Course Schedule (LC 207), Course Schedule II (LC 210), Find Safe States (LC 802), Topological Sort, Alien Dictionary (LC 269), Parallel Courses (LC 1136), Detect Cycle Undirected (LC 684).
