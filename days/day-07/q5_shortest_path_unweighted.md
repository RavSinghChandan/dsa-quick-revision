# Q5 — Shortest Path in Unweighted Graph (BFS)

## 1. What is this question actually asking?

Given an unweighted graph (where every edge has the same cost, typically 1) and a source node, find the shortest path (minimum number of edges) from the source to every other node, or to a specific target. BFS naturally finds shortest paths in unweighted graphs because it explores nodes level by level — the first time you reach a node is guaranteed to be via the shortest path.

## 2. Pattern

BFS — level-by-level exploration; distance from source increments by 1 each level; first visit = shortest path

## 3. Understand with a Diagram

```
Graph:
0 --- 1 --- 3
|         /
2 --------

Adjacency: {0:[1,2], 1:[0,3], 2:[0,3], 3:[1,2]}

BFS from source=0:
Level 0: {0}        dist[0]=0
Level 1: {1,2}      dist[1]=1, dist[2]=1
Level 2: {3}        dist[3]=2  (via 1 or 2)

Shortest paths:
0→0: 0 edges
0→1: 1 edge  (0-1)
0→2: 1 edge  (0-2)
0→3: 2 edges (0-1-3 or 0-2-3)

BFS Queue trace:
queue=[0], dist={0:0}, visited={0}
  dequeue 0, neighbors=[1,2]:
    1 not visited: dist[1]=0+1=1, visited={0,1}, queue=[1,2]
    2 not visited: dist[2]=0+1=1, visited={0,1,2}, queue=[1,2]
  dequeue 1, neighbors=[0,3]:
    0 visited, skip
    3 not visited: dist[3]=1+1=2, queue=[2,3]
  dequeue 2, neighbors=[0,3]:
    0 visited, 3 visited, skip
  dequeue 3: neighbors all visited

dist = {0:0, 1:1, 2:1, 3:2}

Path reconstruction (using parent array):
parent[1]=0, parent[2]=0, parent[3]=1
Path to 3: 3←1←0 → [0,1,3]
```

## 4. Brute Force → Better → Optimal

### Brute Force — DFS with path tracking
**Idea:** DFS from source, track path length. Not shortest because DFS doesn't explore by level.

Why wrong for shortest path: DFS might find a long path first. For unweighted graphs, DFS guarantees correctness only with memoization — but BFS is naturally correct.

### Optimal — BFS
**Idea:** BFS explores nodes in order of increasing distance from source. The first time a node is visited, it's via the shortest path. Track distances (or levels) as you go.

```
dist = [-1] * V
dist[src] = 0
queue = [src]

while queue:
    u = queue.popleft()
    for v in adj[u]:
        if dist[v] == -1:  // not visited
            dist[v] = dist[u] + 1
            queue.append(v)

return dist  // dist[v] = shortest distance from src to v, -1 if unreachable
```

O(V+E) time, O(V) space.

## 5. Pseudocode (Optimal)

```
function shortestPath(adj, V, src):
    dist = [-1] * V
    dist[src] = 0
    queue = deque([src])
    
    while queue not empty:
        u = queue.popleft()
        for each neighbor v of u:
            if dist[v] == -1:
                dist[v] = dist[u] + 1
                queue.append(v)
    
    return dist

// To reconstruct path:
function shortestPathWithParent(adj, V, src, target):
    dist = [-1] * V; parent = [-1] * V
    dist[src] = 0; queue = deque([src])
    
    while queue:
        u = queue.popleft()
        for v in adj[u]:
            if dist[v] == -1:
                dist[v] = dist[u] + 1
                parent[v] = u
                queue.append(v)
    
    // Reconstruct path
    path = []; node = target
    while node != -1:
        path.append(node)
        node = parent[node]
    return path[::-1]
```

## 6. Python Code

```python
from collections import deque, defaultdict


def shortest_path_bfs(adj, V, src):
    """Returns shortest distances from src to all nodes."""
    dist = [-1] * V
    dist[src] = 0
    queue = deque([src])
    
    while queue:
        u = queue.popleft()
        for v in adj[u]:
            if dist[v] == -1:
                dist[v] = dist[u] + 1
                queue.append(v)
    
    return dist


def shortest_path_with_reconstruction(adj, V, src, target):
    """Returns shortest path from src to target."""
    dist = [-1] * V
    parent = [-1] * V
    dist[src] = 0
    queue = deque([src])
    
    while queue:
        u = queue.popleft()
        if u == target:
            break
        for v in adj[u]:
            if dist[v] == -1:
                dist[v] = dist[u] + 1
                parent[v] = u
                queue.append(v)
    
    if dist[target] == -1:
        return []  # unreachable
    
    path = []
    node = target
    while node != -1:
        path.append(node)
        node = parent[node]
    return path[::-1]


# Test 1 — simple graph
V = 4
adj = defaultdict(list)
adj[0].extend([1, 2])
adj[1].extend([0, 3])
adj[2].extend([0, 3])
adj[3].extend([1, 2])

print(shortest_path_bfs(adj, V, 0))   # [0, 1, 1, 2]

# Test 2 — path reconstruction
print(shortest_path_with_reconstruction(adj, V, 0, 3))   # [0, 1, 3] or [0, 2, 3]

# Test 3 — disconnected graph
adj2 = defaultdict(list)
adj2[0].append(1)
# Node 2 and 3 are isolated
print(shortest_path_bfs(adj2, 4, 0))   # [0, 1, -1, -1]

# Test 4 — source to itself
print(shortest_path_with_reconstruction(adj, V, 0, 0))   # [0]
```

## 7. Complexity Table

| Approach  | Time    | Space | Notes                              |
|-----------|---------|-------|------------------------------------|
| DFS       | O(V+E)  | O(V)  | NOT guaranteed shortest for unweighted |
| BFS       | O(V+E)  | O(V)  | Guarantees shortest path — optimal |

## 8. Edge Cases to Remember

- Source == target — dist = 0, path = [src]
- Disconnected graph — unreachable nodes get dist = -1
- No path to target — return -1 or empty path
- Graph with one node — dist[0] = 0
- Multiple shortest paths — BFS finds one; all have same length
- Dense graph (many edges) — still O(V+E), each edge processed once

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Word Ladder                           | BFS on word states (each word = node)               | LC 127     |
| Rotten Oranges                        | Multi-source BFS for min time                       | LC 994     |
| Snakes and Ladders                    | BFS on board positions                              | LC 909     |
| 01 Matrix                             | Multi-source BFS from all 0-cells                   | LC 542     |
| Shortest Path in Binary Matrix        | BFS on grid (8-directional)                         | LC 1091    |
| Jump Game III                         | BFS reachability                                    | LC 1306    |
| Open the Lock                         | BFS on states                                       | LC 752     |
| Minimum Jumps to Reach End            | BFS on array positions                              | LC 45      |

## 10. The ONE Trick to Remember

**"BFS = level order = shortest path in unweighted graph — first visit is always the shortest."**

BFS explores all nodes at distance d before any node at distance d+1. So the first time BFS reaches a node, the path taken is guaranteed to be the shortest. For weighted graphs, use Dijkstra. For unweighted (or uniform weight), BFS is optimal. Initialize dist[src]=0, all others=-1; enqueue src; for each dequeued node, update unvisited neighbors.
