# ⚡ Quick Note · Day 8 — Strings & Graphs · Q7 · Topo Sort (Kahn)
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Order DAG nodes so every edge points forward (or detect cycle)."

## Pattern
`Kahn's — BFS on in-degrees`

## Visual in your head
```
indeg[]; queue = indeg==0 nodes
pop u → order; for u→v: indeg[v]--; if 0 enqueue
len(order)<n → cycle
```

## The trick (say it out loud)
> "Repeatedly remove in-degree-0 nodes. If fewer than n get ordered, there's a cycle."

## Code skeleton
```python
q=deque(i for i in range(n) if indeg[i]==0)
while q:
    u=q.popleft(); order.append(u)
    for v in adj[u]:
        indeg[v]-=1
        if indeg[v]==0: q.append(v)
return order if len(order)==n else []
```

## Complexity
- Time O(V+E) · Space O(V+E)

## This trick solves more
Course Schedule I/II (207/210) · Alien Dictionary · Parallel Courses (1136) · Longest path DAG
