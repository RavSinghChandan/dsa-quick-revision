# Day 8 — Strings & Graphs · Q14 · Kosaraju's Algorithm (Strongly Connected Components)

---

## What is this question actually asking?

Find the **Strongly Connected Components** (SCCs) of a **directed** graph — maximal groups where every node can reach every other node in the group.

```
0→1, 1→2, 2→0, 2→3, 3→4, 4→3
SCCs: {0,1,2}, {3,4}
```

> The classic two-pass DFS. Order nodes by finish time, then DFS the **transposed** graph in that order — each DFS tree is one SCC.

---

## Pattern

```
PATTERN: Two-pass DFS — finish-order stack + DFS on the reversed graph
```

Whenever you see: *"strongly connected components / mutual reachability in a directed graph"* → (1) DFS the graph pushing nodes onto a stack by finish time; (2) reverse all edges; (3) pop nodes and DFS the reversed graph — each traversal collects one SCC.

---

## Understand with a diagram

```
Pass 1: DFS original graph, push each node when it FINISHES → order stack.
Pass 2: transpose the graph (reverse every edge).
Pass 3: pop nodes from the stack; DFS the transposed graph from each unvisited
        popped node → the set of nodes reached is ONE SCC.

Why it works: finishing order + edge reversal isolates each mutually-reachable group.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — reachability for every pair — O(V·(V+E))
DFS from each node both ways; group mutually reachable ones. Slow.

### 🟢 Optimal — Kosaraju two-pass — O(V + E)
Two linear DFS passes plus building the transpose.

---

## Pseudocode (Optimal)

```
function kosaraju(n, adj):
    visited = set(); order = []
    # Pass 1: finish-time order
    for u in 0..n-1:
        if u not visited: dfs1(u)      ← push u to order on finish
    # Pass 2: transpose
    radj = reverse all edges
    # Pass 3: pop in reverse finish order, DFS transpose
    visited.clear(); sccs = []
    for u in reversed(order):
        if u not visited:
            comp = []
            dfs2(u, radj, comp)        ← collect one SCC
            sccs.append(comp)
    return sccs
```

---

## Python Code

```python
def kosaraju(n: int, adj: list[list[int]]) -> list[list[int]]:
    visited = [False] * n
    order = []

    # Pass 1: push nodes by finish time (iterative DFS)
    def dfs1(start):
        stack = [(start, False)]
        while stack:
            node, processed = stack.pop()
            if processed:
                order.append(node)          # finished → push
                continue
            if visited[node]:
                continue
            visited[node] = True
            stack.append((node, True))      # re-visit to record finish
            for v in adj[node]:
                if not visited[v]:
                    stack.append((v, False))

    for u in range(n):
        if not visited[u]:
            dfs1(u)

    # Pass 2: transpose
    radj = [[] for _ in range(n)]
    for u in range(n):
        for v in adj[u]:
            radj[v].append(u)

    # Pass 3: DFS transpose in reverse finish order
    visited = [False] * n
    sccs = []
    for u in reversed(order):
        if not visited[u]:
            comp = []
            stack = [u]
            visited[u] = True
            while stack:
                node = stack.pop()
                comp.append(node)
                for v in radj[node]:
                    if not visited[v]:
                        visited[v] = True
                        stack.append(v)
            sccs.append(sorted(comp))
    return sccs


# Test
adj = [[1],[2],[0,3],[4],[3]]
print(sorted(kosaraju(5, adj)))   # [[0, 1, 2], [3, 4]]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Kosaraju | O(V + E) | O(V + E) |

---

## Edge Cases to remember

- **Order = finish time** (post-order), not discovery order — push a node only when its DFS fully completes.
- **Transpose the graph** between passes — reversing edges is essential.
- **Process pass 3 in REVERSE finish order** — pop from the end of `order`.
- **Tarjan's algorithm** is a single-pass alternative (low-link values) — mention it as the O(V+E) one-pass option.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Number of SCCs | This |
| 2 | Critical Connections / Bridges (LC 1192) | Tarjan low-link |
| 3 | Course Schedule (Q8) | Cycle → SCC of size >1 |
| 4 | Redundant Connection II (LC 685) | Directed cycle |
| 5 | Condensation graph / 2-SAT | SCC |
| 6 | Mother vertex | Finish order |
| 7 | Find eventual safe nodes (LC 802) | Reverse graph |
| 8 | Strongly connected check | SCC = 1 |

---

## The ONE trick to remember

```
"DFS FOR FINISH ORDER → TRANSPOSE → DFS IN REVERSE FINISH ORDER = SCCs."
```

Kosaraju's two passes: first DFS records finish times; then DFS the reversed graph in reverse-finish order, and each traversal tree is exactly one strongly connected component.

> Memory hook: "Finish, flip, and re-explore — each re-exploration is one tightly-knit group."

---
