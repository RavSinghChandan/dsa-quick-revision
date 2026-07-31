# ⚡ Quick Note · Day 8 — Strings & Graphs · Q11 · Cheapest Flights K Stops
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Cheapest src→dst using at most K stops."

## Pattern
`Bellman-Ford with K+1 rounds (snapshot per round)`

## Visual in your head
```
dist[src]=0
repeat K+1 times:
  temp=copy(dist)
  for (u,v,w): if dist[u]+w<temp[v]: temp[v]=dist[u]+w
  dist=temp
```

## The trick (say it out loud)
> "Bellman-Ford, K+1 rounds, SNAPSHOT distances each round so one round adds only one hop."

## Code skeleton
```python
for _ in range(K+1):
    temp=dist[:]
    for u,v,w in flights:
        if dist[u]+w<temp[v]: temp[v]=dist[u]+w
    dist=temp
```

## Complexity
- Time O(K·E) · Space O(V)

## This trick solves more
Cheapest Flights (787) · Bellman-Ford · Shortest ≤K edges · Network Delay (743) · Currency arbitrage
