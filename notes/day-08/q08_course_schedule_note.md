# ⚡ Quick Note · Day 8 — Strings & Graphs · Q8 · Course Schedule I
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Can all courses be finished (is the prereq graph acyclic)?"

## Pattern
`Directed cycle detection (Kahn topo count OR DFS 3-color)`

## Visual in your head
```
[a,b] → edge b→a
Kahn: order all n? yes → no cycle
DFS: gray(on path) revisited → cycle
```

## The trick (say it out loud)
> "No cycle ⇔ can finish. Kahn: ordered all n? DFS: revisiting a GRAY node = cycle."

## Code skeleton
```python
# Kahn
seen=0
while q: u=q.popleft(); seen+=1; for v: indeg[v]-=1; if 0 q.append(v)
return seen==n
```

## Complexity
- Time O(V+E) · Space O(V+E)

## This trick solves more
Course Schedule II (210) · Topo Sort · Alien Dictionary · Eventual Safe States (802) · Deadlock detect
