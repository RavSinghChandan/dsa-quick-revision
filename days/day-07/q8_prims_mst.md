# Q8 — Minimum Spanning Tree — Prim's Algorithm

## 1. What is this question actually asking?

Given a connected, undirected, weighted graph, find a Minimum Spanning Tree (MST) — a subset of edges that connects all vertices with no cycles and with the minimum possible total edge weight. The MST has exactly V-1 edges for V vertices. Prim's algorithm grows the MST greedily starting from one vertex, always adding the cheapest edge that connects the current tree to a new vertex.

## 2. Pattern

Min-Heap (Priority Queue) + Greedy — always add the cheapest edge that connects the growing MST to an unvisited vertex

## 3. Understand with a Diagram

```
Graph (undirected, weighted):
    2       3
0 ----- 1 ----- 2
|     / |       |
|6  /5  |1      |4
| /     |       |
3 ----- 4 ----- 5
    3       2

Adjacency: 
0: [(1,2),(3,6)]
1: [(0,2),(2,3),(3,5),(4,1)]
2: [(1,3),(5,4)]
3: [(0,6),(1,5),(4,3)]
4: [(1,1),(3,3),(5,2)]
5: [(2,4),(4,2)]

Prim's from vertex 0:
visited = {}, MST_edges = [], total_cost = 0
heap = [(0, 0, -1)]  // (weight, to_node, from_node)

Step 1: pop (0,0,-1): add 0 to MST, visited={0}
  Push neighbors: (2,1,0), (6,3,0) → heap=[(2,1,0),(6,3,0)]

Step 2: pop (2,1,0): add edge 0-1(w=2), visited={0,1}
  MST_edges: [0-1], cost=2
  Push: (3,2,1),(5,3,1),(1,4,1) → heap=[(1,4,1),(3,2,1),(5,3,1),(6,3,0)]

Step 3: pop (1,4,1): add edge 1-4(w=1), visited={0,1,4}
  MST_edges: [0-1,1-4], cost=3
  Push: (3,3,4),(2,5,4) → heap=[(2,5,4),(3,2,1),(3,3,4),(5,3,1),(6,3,0)]

Step 4: pop (2,5,4): add edge 4-5(w=2), visited={0,1,4,5}
  MST_edges: [0-1,1-4,4-5], cost=5
  Push: (4,2,5) → heap=[(3,2,1),(3,3,4),(4,2,5),(5,3,1),(6,3,0)]

Step 5: pop (3,2,1): add edge 1-2(w=3), visited={0,1,2,4,5}
  MST_edges: [0-1,1-4,4-5,1-2], cost=8
  (Push 4,2,5 but 5 visited; push nothing new from 2 except (4,5,2) but 5 visited)

Step 6: pop (3,3,4): add edge 4-3(w=3), visited={0,1,2,3,4,5}
  MST_edges: [0-1,1-4,4-5,1-2,4-3], cost=11
  All nodes visited → done!

Total MST cost: 0+2+1+2+3+3 = 11
MST edges: 0-1(2), 1-4(1), 4-5(2), 1-2(3), 4-3(3)
```

## 4. Brute Force → Better → Optimal

### Brute Force — Try all spanning trees
**Idea:** Enumerate all possible spanning trees (V^(V-2) by Cayley's formula), pick the minimum weight one.

Not practical for any reasonable input.

### Kruskal's Algorithm (complement to Prim's)
**Idea:** Sort ALL edges by weight. Add edge if it doesn't form a cycle (use Union-Find). O(E log E).

Good when E << V².

### Prim's Algorithm (Optimal for dense graphs)
**Idea:** Start with any vertex. Greedily add the minimum weight edge that extends the current tree to a new vertex. Use a min-heap to efficiently find the next minimum edge.

```
dist = [+inf] * V  // min edge weight to add each node to MST
dist[src] = 0
heap = [(0, src)]
visited = set()
mst_cost = 0

while heap:
    (d, u) = heappop(heap)
    if u in visited: continue
    visited.add(u)
    mst_cost += d
    
    for (v, w) in adj[u]:
        if v not in visited and w < dist[v]:
            dist[v] = w
            heappush(heap, (w, v))

return mst_cost
```

O(E log V) with heap. O(V²) with array.

## 5. Pseudocode (Optimal)

```
function primMST(V, adj):
    min_cost = [+inf] * V
    min_cost[0] = 0
    visited = [False] * V
    heap = [(0, 0)]  // (weight, vertex)
    total = 0
    mst_edges = []
    parent = [-1] * V
    
    while heap:
        (w, u) = heappop(heap)
        if visited[u]: continue
        visited[u] = True
        total += w
        if parent[u] != -1:
            mst_edges.append((parent[u], u, w))
        
        for (v, weight) in adj[u]:
            if not visited[v] and weight < min_cost[v]:
                min_cost[v] = weight
                parent[v] = u
                heappush(heap, (weight, v))
    
    return total, mst_edges
```

## 6. Python Code

```python
import heapq
from collections import defaultdict


def prim_mst(V, adj):
    """
    adj[u] = list of (v, weight)  (undirected graph)
    Returns (total_cost, mst_edges)
    """
    min_cost = [float('inf')] * V
    min_cost[0] = 0
    visited = [False] * V
    parent = [-1] * V
    heap = [(0, 0)]  # (cost, vertex)
    total = 0
    mst_edges = []
    
    while heap:
        w, u = heapq.heappop(heap)
        
        if visited[u]:
            continue
        
        visited[u] = True
        total += w
        
        if parent[u] != -1:
            mst_edges.append((parent[u], u, w))
        
        for v, weight in adj[u]:
            if not visited[v] and weight < min_cost[v]:
                min_cost[v] = weight
                parent[v] = u
                heapq.heappush(heap, (weight, v))
    
    return total, mst_edges


# Test 1 — standard example
V = 6
adj = defaultdict(list)
def add_edge(u, v, w):
    adj[u].append((v, w))
    adj[v].append((u, w))

add_edge(0, 1, 2); add_edge(0, 3, 6)
add_edge(1, 2, 3); add_edge(1, 3, 5); add_edge(1, 4, 1)
add_edge(2, 5, 4); add_edge(3, 4, 3); add_edge(4, 5, 2)

cost, edges = prim_mst(V, adj)
print(f"MST cost: {cost}")    # MST cost: 11
print(f"MST edges: {edges}")  # 5 edges connecting all 6 nodes

# Test 2 — simple triangle
V2 = 3
adj2 = defaultdict(list)
adj2[0].extend([(1,1),(2,4)]); adj2[1].extend([(0,1),(2,2)]); adj2[2].extend([(0,4),(1,2)])
cost2, _ = prim_mst(V2, adj2)
print(f"Triangle MST: {cost2}")  # 3 (edges 0-1 weight 1, 1-2 weight 2)
```

## 7. Complexity Table

| Approach       | Time          | Space   | Notes                              |
|----------------|---------------|---------|----------------------------------|
| Kruskal's      | O(E log E)    | O(V+E)  | Sort edges + Union-Find            |
| Prim's (array) | O(V²)         | O(V)    | Good for dense graphs              |
| Prim's (heap)  | O(E log V)    | O(V+E)  | Good for sparse graphs             |

## 8. Edge Cases to Remember

- Disconnected graph — MST doesn't exist (can't span all nodes); detect when visited count < V
- Single vertex — MST cost = 0, no edges
- Multiple edges between same pair — choose minimum weight one
- Graph with equal weight edges — any valid MST is acceptable
- Negative weights — Prim's handles them correctly (unlike Dijkstra for shortest path, MST with negatives is fine)
- Dense vs sparse — for dense graphs (E ≈ V²), array-based Prim's beats heap-based Kruskal's

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Kruskal's MST                         | Sort edges + Union-Find approach to MST             | —          |
| Min Cost to Connect All Points        | MST on Euclidean distances                          | LC 1584    |
| Critical Connections in Network       | Find bridges (non-MST edges)                        | LC 1192    |
| Connecting Cities with Min Cost       | MST via Prim's or Kruskal's                         | LC 1135    |
| Optimize Water Distribution           | Virtual node → MST problem                          | LC 1168    |
| Find Critical and Pseudo-Critical MST | Enumerate MSTs with/without edges                   | LC 1489    |
| Dijkstra's Algorithm                  | Same heap pattern, different relaxation             | —          |
| Network Delay Time                    | Dijkstra (not MST but similar heap pattern)         | LC 743     |

## 10. The ONE Trick to Remember

**"Grow the tree greedily — always pick the cheapest edge connecting the tree to a new node."**

Start with any node. The MST grows one node at a time. At each step, look at all edges crossing the "in-tree vs out-of-tree" boundary. Pick the cheapest one. Add the new node (and its edge) to the tree. Repeat V-1 times. Use a min-heap to efficiently find the cheapest crossing edge. Skip nodes already in the tree (visited check).
