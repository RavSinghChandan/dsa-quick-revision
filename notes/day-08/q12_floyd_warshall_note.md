# ⚡ Quick Note · Day 8 — Strings & Graphs · Q12 · Floyd-Warshall
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Shortest distance between ALL pairs of nodes."

## Pattern
`DP over intermediate node k (triple loop)`

## Visual in your head
```
for k: for i: for j:
  dist[i][j] = min(dist[i][j], dist[i][k]+dist[k][j])
k = OUTER loop (critical)
```

## The trick (say it out loud)
> "Every node k as a layover: dist[i][j] = min(itself, i→k→j). k MUST be the outer loop."

## Code skeleton
```python
for k in range(n):
    for i in range(n):
        for j in range(n):
            dist[i][j]=min(dist[i][j], dist[i][k]+dist[k][j])
```

## Complexity
- Time O(V³) · Space O(V²) · handles negative edges (no neg cycle)

## This trick solves more
Smallest Neighbors City (1334) · Transitive closure · Neg cycle (dist[i][i]<0) · Evaluate Division (399)
