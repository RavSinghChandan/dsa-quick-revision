# ⚡ Quick Note · Day 8 — Strings & Graphs · Q6 · Rotten Oranges
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Minutes for rot to spread to all fresh oranges (or -1)."

## Pattern
`Multi-source BFS (all sources level 0, count levels)`

## Visual in your head
```
enqueue ALL rotten cells; count fresh
each BFS level = 1 minute; rot fresh neighbors
end: fresh>0 → -1 else minutes
```

## The trick (say it out loud)
> "Enqueue all rotten sources at level 0. BFS level-by-level = minutes. Leftover fresh → -1."

## Code skeleton
```python
while q and fresh>0:
    minutes+=1
    for _ in range(len(q)):
        r,c=q.popleft()
        for nb fresh: rot; fresh-=1; q.append(nb)
```

## Complexity
- Time O(R·C) · Space O(R·C)

## This trick solves more
Rotting Oranges (994) · Walls and Gates (286) · 01 Matrix (542) · Shortest Bridge (934) · Burn Tree
