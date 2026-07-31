# ⚡ Quick Note · Day 8 — Strings & Graphs · Q10 · Dijkstra
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Shortest paths from a source (non-negative weights)."

## Pattern
`Greedy + min-heap: pop nearest, relax edges`

## Visual in your head
```
dist[src]=0; heap=[(0,src)]
pop (d,u): if d>dist[u] skip
for (v,w): if d+w<dist[v]: dist[v]=d+w; push
```

## The trick (say it out loud)
> "Min-heap pops the nearest node; relax its edges; push improved neighbors. Skip stale entries."

## Code skeleton
```python
d,u=heapq.heappop(heap)
if d>dist[u]: continue
for v,w in adj[u]:
    if d+w<dist[v]: dist[v]=d+w; heapq.heappush(heap,(d+w,v))
```

## Complexity
- Time O((V+E) log V) · Space O(V+E) · NON-negative only

## This trick solves more
Network Delay (743) · Cheapest Flights K · Min Effort Path (1631) · Swim Water (778) · Max Prob (1514)
