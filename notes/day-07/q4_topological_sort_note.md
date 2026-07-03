# Q4 — Topological Sort (2-min card)

**Order DAG nodes so every edge u→v has u before v.**

**Pattern:** DFS post-order OR Kahn's BFS (in-degree based)

```
Graph: 5→[2,0], 4→[0,1], 2→3, 3→1

DFS post-order: visit descendants first, add when done
  dfs(5): dfs(2)→dfs(3)→dfs(1)→stack=[1], stack=[1,3,2], dfs(0)→stack=[1,3,2,0,5]
  dfs(4): 0,1 visited → stack=[1,3,2,0,5,4]
  Reverse: [4,5,0,2,3,1] ✓

Kahn's BFS: in-degree={4:0,5:0,...}
queue=[4,5] → process 4→[0,1], 5→[0,2] → queue=[0,2] → ... → [4,5,0,2,3,1] ✓
```

**DFS trick:** "Add to stack AFTER all descendants — reverse stack = topo order."
**Kahn's trick:** "Queue = 0 in-degree nodes; decrement neighbors; if len(result)<V → cycle."

```python
# DFS
def dfs(v): visited[v]=True; [dfs(u) for u in adj[v] if not visited[u]]; stack.append(v)
# Kahn's
queue=deque(v for v in range(V) if in_deg[v]==0)
while queue: u=queue.popleft(); result.append(u); [queue.append(v) for v in adj[u] if not (in_deg.__setitem__(v,in_deg[v]-1) or in_deg[v])]
```

**Complexity:** Time O(V+E) | Space O(V)

**Same pattern solves:** Course Schedule (LC 207), Course Schedule II (LC 210), Alien Dictionary (LC 269), Min Height Trees (LC 310), Parallel Courses (LC 1136), Recipes (LC 2115), Sort by Groups (LC 1203).
