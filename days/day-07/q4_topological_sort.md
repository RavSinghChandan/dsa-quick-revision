# Q4 — Topological Sort (DFS + Kahn's BFS)

## 1. What is this question actually asking?

Given a Directed Acyclic Graph (DAG), find a linear ordering of its nodes such that for every directed edge u→v, node u comes before node v in the ordering. Think of it as scheduling tasks where some tasks must complete before others can begin. Multiple valid orderings may exist.

## 2. Pattern

Two approaches: DFS post-order (add to result after all descendants are processed) OR Kahn's BFS (process nodes with in-degree 0 first, remove edges, repeat)

## 3. Understand with a Diagram

```
Graph (DAG):
5 → 0 ← 4
↓       ↓
2   1 → 3

Adjacency list:
5: [0, 2]
4: [0, 1]  (wait: 4→0 and 4→1)
Actually let me use:
5→2, 5→0, 4→0, 4→1, 2→3, 3→1

DFS Approach (post-order):
Start DFS from unvisited nodes. Add to stack AFTER all descendants are visited.
Stack is LIFO so the final reverse gives topological order.

DFS from 5:
  Visit 2: Visit 3: Visit 1 (leaf): stack=[1]
           back: stack=[1,3]
  back: stack=[1,3,2]
  Visit 0 (leaf): stack=[1,3,2,0]
back: stack=[1,3,2,0,5]

DFS from 4:
  0: already visited
  1: already visited
stack=[1,3,2,0,5,4]

Result (reverse stack): [4,5,0,2,3,1]

Kahn's BFS Approach:
In-degree: {0:2, 1:2, 2:1, 3:1, 4:0, 5:0}
Queue (in-degree 0): [4, 5]

Process 4: remove 4→0 (in[0]=1), 4→1 (in[1]=1). Queue=[5]
Process 5: remove 5→0 (in[0]=0), 5→2 (in[2]=0). Queue=[0,2]
Process 0: no outgoing. Queue=[2]
Process 2: remove 2→3 (in[3]=0). Queue=[3]
Process 3: remove 3→1 (in[1]=0). Queue=[1]
Process 1: no outgoing. Queue=[]

Result: [4,5,0,2,3,1] ✓ (matches DFS result)
```

## 4. Brute Force → Better → Optimal

### Brute Force — Repeatedly find nodes with no prerequisites
**Idea:** Scan for nodes with all prerequisites fulfilled. Add them to result. Remove them from graph. Repeat.

O(V²) per scan for V rounds → O(V³). Too slow.

### Optimal — DFS Post-Order
**Idea:** DFS from each unvisited node. After fully exploring a node (all its descendants visited), push it to a stack. Reverse the stack at the end.

```
visited = {}
stack = []

function dfs(v):
    visited[v] = True
    for u in adj[v]:
        if not visited[u]: dfs(u)
    stack.append(v)  // POST-order: add AFTER descendants

for each vertex v:
    if not visited[v]: dfs(v)

return reversed(stack)
```

### Optimal — Kahn's BFS
**Idea:** Track in-degrees. Start with all nodes having in-degree 0. Process them one by one; for each processed node, decrement in-degrees of neighbors. Add newly 0-in-degree nodes to queue.

```
compute in_degree for each node
queue = [nodes with in_degree == 0]
result = []

while queue:
    u = queue.popleft()
    result.append(u)
    for v in adj[u]:
        in_degree[v] -= 1
        if in_degree[v] == 0:
            queue.append(v)

if len(result) == V: return result
else: return []  // cycle exists
```

Both: O(V+E) time, O(V) space.

## 5. Pseudocode (Both Approaches)

```
// DFS approach
function topoSortDFS(V, adj):
    visited = [False] * V
    stack = []
    
    function dfs(v):
        visited[v] = True
        for u in adj[v]:
            if not visited[u]: dfs(u)
        stack.append(v)
    
    for v in range(V):
        if not visited[v]: dfs(v)
    
    return stack[::-1]  // reverse

// Kahn's BFS
function topoSortBFS(V, adj):
    in_degree = [0] * V
    for v in range(V):
        for u in adj[v]: in_degree[u] += 1
    
    queue = deque([v for v in range(V) if in_degree[v] == 0])
    result = []
    
    while queue:
        u = queue.popleft()
        result.append(u)
        for v in adj[u]:
            in_degree[v] -= 1
            if in_degree[v] == 0: queue.append(v)
    
    return result if len(result) == V else []
```

## 6. Python Code

```python
from collections import defaultdict, deque


def topo_sort_dfs(V, adj):
    """DFS post-order topological sort."""
    visited = [False] * V
    stack = []
    
    def dfs(v):
        visited[v] = True
        for u in adj[v]:
            if not visited[u]:
                dfs(u)
        stack.append(v)
    
    for v in range(V):
        if not visited[v]:
            dfs(v)
    
    return stack[::-1]


def topo_sort_bfs(V, adj):
    """Kahn's BFS topological sort."""
    in_degree = [0] * V
    for v in range(V):
        for u in adj[v]:
            in_degree[u] += 1
    
    queue = deque(v for v in range(V) if in_degree[v] == 0)
    result = []
    
    while queue:
        u = queue.popleft()
        result.append(u)
        for v in adj[u]:
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)
    
    return result if len(result) == V else []  # empty if cycle


# Test 1 — standard DAG
V = 6
adj = defaultdict(list)
adj[5].extend([2, 0])
adj[4].extend([0, 1])
adj[2].append(3)
adj[3].append(1)

print(topo_sort_dfs(V, adj))   # [5, 4, 2, 3, 1, 0] or valid ordering
print(topo_sort_bfs(V, adj))   # [4, 5, 0, 2, 3, 1] or valid ordering

# Verify: in any valid ordering, for edge u→v, u appears before v
def verify_topo(order, adj):
    pos = {v: i for i, v in enumerate(order)}
    for u in adj:
        for v in adj[u]:
            if pos.get(u, -1) >= pos.get(v, float('inf')):
                return False
    return True

print(verify_topo(topo_sort_dfs(V, adj), adj))   # True
print(verify_topo(topo_sort_bfs(V, adj), adj))   # True

# Test 2 — linear chain: 0→1→2→3
adj2 = defaultdict(list)
adj2[0].append(1); adj2[1].append(2); adj2[2].append(3)
print(topo_sort_bfs(4, adj2))   # [0, 1, 2, 3]

# Test 3 — cycle (BFS returns empty)
adj3 = defaultdict(list)
adj3[0].append(1); adj3[1].append(2); adj3[2].append(0)
print(topo_sort_bfs(3, adj3))   # []
```

## 7. Complexity Table

| Approach    | Time    | Space | Notes                              |
|-------------|---------|-------|------------------------------------|
| DFS         | O(V+E)  | O(V)  | Post-order + reverse stack         |
| Kahn's BFS  | O(V+E)  | O(V)  | Also detects cycles (empty result) |

## 8. Edge Cases to Remember

- Graph with cycle — DFS gives garbage ordering; Kahn's returns empty (explicit cycle detection)
- Disconnected graph — DFS outer loop handles it; Kahn's starts all 0-in-degree nodes
- Single node — returns [0]
- Multiple valid orderings — both algorithms return one valid ordering; Kahn's order depends on queue ordering
- Node with no edges — can appear anywhere in ordering
- Kahn's BFS also detects cycles — if result size < V, cycle exists

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Course Schedule                       | Topological sort feasibility (detect cycle)         | LC 207     |
| Course Schedule II                    | Return topological ordering of courses              | LC 210     |
| Alien Dictionary                      | Build graph from word order, topological sort       | LC 269     |
| Minimum Height Trees                  | Topological trim from leaves                        | LC 310     |
| Parallel Courses                      | Min semesters = longest path in topo order          | LC 1136    |
| Sequence Reconstruction               | Check if unique topo order exists                   | LC 444     |
| Find All Possible Recipes             | Topo sort with ingredient dependencies              | LC 2115    |
| Sort Items by Groups Respecting Deps  | Topological sort at two levels                      | LC 1203    |

## 10. The ONE Trick to Remember

**"DFS: add to stack AFTER all descendants; reverse stack = topological order."**
**"Kahn's: process 0-in-degree nodes; decrement neighbors; if size < V → cycle."**

DFS insight: the deepest node (no successors) should come LAST in the path, but EARLIEST in topological order. So add to stack when DFS is DONE with a node. Reverse gives topological order.

Kahn's insight: a node with in-degree 0 has no unprocessed prerequisites — it can go first. Process it, remove its outgoing edges, exposing new nodes with in-degree 0.
