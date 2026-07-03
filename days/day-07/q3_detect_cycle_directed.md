# Q3 — Detect Cycle in Directed Graph

## 1. What is this question actually asking?

Given a directed graph with V vertices and E edges, determine if the graph contains a cycle. A cycle in a directed graph means you can start at a node, follow directed edges, and return to the same node. This is different from undirected cycles because direction matters — A→B and B→A is a cycle, but A→B and A→C with no path back to A is not.

## 2. Pattern

DFS with "recursion stack" tracking — track both visited nodes and nodes in the current DFS path; cycle detected when you reach a node already in the current path

## 3. Understand with a Diagram

```
Graph with cycle:
0 → 1 → 2
        ↓
    3 ← 4
    ↓
    2 (back to 2 creates cycle: 2→4→3→2)

Wait, let me use a cleaner example:
0 → 1
↑   ↓
3 ← 2

DFS from 0:
visited = {}, rec_stack = {}

Visit 0: visited={0}, rec_stack={0}
  Visit 1: visited={0,1}, rec_stack={0,1}
    Visit 2: visited={0,1,2}, rec_stack={0,1,2}
      Visit 3: visited={0,1,2,3}, rec_stack={0,1,2,3}
        Visit 0: 0 in rec_stack → CYCLE DETECTED! ✓

For a DAG (no cycle):
0 → 1 → 3
↓       
2 → 3

DFS from 0:
Visit 0: rec_stack={0}
  Visit 1: rec_stack={0,1}
    Visit 3: rec_stack={0,1,3}
      3 has no neighbors. Backtrack.
    rec_stack={0,1}. 1 done.
  rec_stack={0}
  Visit 2: rec_stack={0,2}
    Visit 3: already visited, not in rec_stack → no cycle from this path
  rec_stack={0}
0 done. No cycle found.

Key: visited = nodes DFS is done with. rec_stack = nodes in current DFS path.
```

## 4. Brute Force → Better → Optimal

### Brute Force — DFS from every node, track full path
**Idea:** For each node, DFS while keeping a full path. If we revisit a node in the current path, there's a cycle.

Correct but O(V*(V+E)) — redundant work.

### Optimal — DFS with rec_stack
**Idea:** Maintain two boolean arrays: `visited[]` (permanently visited) and `rec_stack[]` (in current DFS path). Start DFS. When entering a node, mark both. When leaving, remove from rec_stack. If you reach a node that's in rec_stack, cycle found.

```
visited = [False] * V
rec_stack = [False] * V

function hasCycle():
    for each node v:
        if not visited[v]:
            if dfs(v): return True
    return False

function dfs(v):
    visited[v] = True
    rec_stack[v] = True
    
    for each neighbor u of v:
        if not visited[u]:
            if dfs(u): return True
        elif rec_stack[u]:
            return True  // back edge → cycle
    
    rec_stack[v] = False  // done with this node's DFS path
    return False
```

O(V+E) time, O(V) space.

### Alternative — Kahn's BFS (Topological Sort)
**Idea:** Try to find a valid topological ordering using BFS. If all nodes are processed, no cycle. If some nodes remain (in-degree never reaches 0), there's a cycle.

```
in_degree = [0] * V
for each edge (u,v): in_degree[v]++

queue = all nodes with in_degree == 0
count = 0
while queue:
    u = queue.popleft(); count++
    for each neighbor v: in_degree[v]--; if 0 → queue.append(v)

return count != V  // if not all processed, cycle exists
```

## 5. Pseudocode (Optimal — DFS)

```
function isCyclic(graph, V):
    visited   = [False] * V
    rec_stack = [False] * V
    
    function dfs(v):
        visited[v] = True
        rec_stack[v] = True
        for neighbor in graph[v]:
            if not visited[neighbor]:
                if dfs(neighbor): return True
            elif rec_stack[neighbor]:
                return True
        rec_stack[v] = False
        return False
    
    for v in range(V):
        if not visited[v]:
            if dfs(v): return True
    return False
```

## 6. Python Code

```python
from collections import defaultdict, deque


def has_cycle_dfs(V, adj):
    """DFS with recursion stack approach."""
    visited   = [False] * V
    rec_stack = [False] * V
    
    def dfs(v):
        visited[v] = True
        rec_stack[v] = True
        for u in adj[v]:
            if not visited[u]:
                if dfs(u): return True
            elif rec_stack[u]:
                return True
        rec_stack[v] = False
        return False
    
    for v in range(V):
        if not visited[v]:
            if dfs(v): return True
    return False


def has_cycle_bfs(V, adj):
    """Kahn's BFS topological sort approach."""
    in_degree = [0] * V
    for v in range(V):
        for u in adj[v]:
            in_degree[u] += 1
    
    queue = deque(v for v in range(V) if in_degree[v] == 0)
    count = 0
    
    while queue:
        v = queue.popleft()
        count += 1
        for u in adj[v]:
            in_degree[u] -= 1
            if in_degree[u] == 0:
                queue.append(u)
    
    return count != V


# Test 1 — graph with cycle: 0→1→2→0
adj1 = defaultdict(list)
adj1[0].append(1)
adj1[1].append(2)
adj1[2].append(0)
print(has_cycle_dfs(3, adj1))   # True
print(has_cycle_bfs(3, adj1))   # True

# Test 2 — DAG (no cycle): 0→1, 0→2, 1→3
adj2 = defaultdict(list)
adj2[0].extend([1, 2])
adj2[1].append(3)
print(has_cycle_dfs(4, adj2))   # False
print(has_cycle_bfs(4, adj2))   # False

# Test 3 — self-loop: 0→0
adj3 = defaultdict(list)
adj3[0].append(0)
print(has_cycle_dfs(1, adj3))   # True

# Test 4 — disconnected with cycle in one component
adj4 = defaultdict(list)
adj4[0].append(1)
adj4[2].append(3)
adj4[3].append(2)
print(has_cycle_dfs(4, adj4))   # True
```

## 7. Complexity Table

| Approach       | Time    | Space | Notes                              |
|----------------|---------|-------|------------------------------------|
| DFS rec_stack  | O(V+E)  | O(V)  | Clean, standard                    |
| Kahn's BFS     | O(V+E)  | O(V)  | Also detects and aids topological sort |

## 8. Edge Cases to Remember

- Self-loop (node points to itself) — caught when neighbor is in rec_stack
- Disconnected graph — outer loop ensures all components are checked
- Single node with no edges — no cycle
- Multiple edges between same two nodes — cycle depends on direction
- DAG — DFS completes without finding rec_stack hit
- Undirected graph — different algorithm: use visited set only (parent tracking needed)

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Topological Sort                      | Only possible if no cycle (Kahn's / DFS)            | —          |
| Course Schedule                       | Can you complete courses? = cycle detection         | LC 207     |
| Course Schedule II                    | Return topological order or [] if cycle             | LC 210     |
| Detect Cycle Undirected Graph         | DFS with parent tracking instead of rec_stack       | LC 684     |
| Find Eventual Safe States             | Nodes not part of any cycle                         | LC 802     |
| Alien Dictionary                      | Topological sort + cycle detection                  | LC 269     |
| Parallel Courses                      | Topological sort to find min semesters              | LC 1136    |
| Build Order (CTCI)                    | Course scheduling variant                           | —          |

## 10. The ONE Trick to Remember

**"Two colors: 'currently exploring' vs 'fully done' — cycle = visiting a node still being explored."**

visited[] = nodes where DFS is complete. rec_stack[] = nodes in the active DFS call stack right now. A back edge (edge to a node still in rec_stack) means we've found a cycle. When DFS finishes a node, remove it from rec_stack (but keep in visited) so we don't misidentify cross edges as cycles.
