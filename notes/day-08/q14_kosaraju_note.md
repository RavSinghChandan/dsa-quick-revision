# ⚡ Quick Note · Day 8 — Strings & Graphs · Q14 · Kosaraju (SCC)
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Find strongly connected components of a directed graph."

## Pattern
`Two-pass DFS: finish-order + DFS on transposed graph`

## Visual in your head
```
1. DFS original → push nodes by finish time
2. transpose (reverse edges)
3. pop in reverse finish order, DFS transpose → each tree = 1 SCC
```

## The trick (say it out loud)
> "DFS for finish order, transpose, DFS in reverse finish order — each traversal is one SCC."

## Code skeleton
```python
# pass1: order.append(node) on finish
# transpose: radj[v].append(u)
# pass3: for u in reversed(order): if unvisited: DFS radj → one SCC
```

## Complexity
- Time O(V+E) · Space O(V+E)

## This trick solves more
Count SCCs · Bridges/Tarjan (1192) · Course Schedule · 2-SAT · Mother Vertex · Safe Nodes (802)
