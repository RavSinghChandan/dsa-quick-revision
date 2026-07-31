# ⚡ Quick Note · Day 8 — Strings & Graphs · Q5 · Bipartite
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Can nodes split into 2 groups with every edge crossing groups?"

## Pattern
`BFS/DFS 2-coloring (conflict = odd cycle)`

## Visual in your head
```
color[]=-1; for each uncolored start: color 0, BFS
neighbor uncolored → 1-color[u]
neighbor same color → NOT bipartite
```

## The trick (say it out loud)
> "2-color via BFS. Neighbor same color ⇒ not bipartite (odd cycle). Loop all components."

## Code skeleton
```python
if color[v]==-1: color[v]=1-color[u]; q.append(v)
elif color[v]==color[u]: return False
```

## Complexity
- Time O(V+E) · Space O(V)

## This trick solves more
Is Bipartite (785) · Possible Bipartition (886) · Odd cycle detect · Flood Fill (733)
