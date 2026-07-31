# ⚡ Quick Note · Day 6 — Binary Tree · Q4 · Burn Tree
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Fire from a target spreads to parent+children each second; time to burn all."

## Pattern
`Parent map → BFS (multi-directional) from target`

## Visual in your head
```
1. map parent[node], find target
2. BFS from target, neighbors = left,right,parent
time = BFS levels (farthest distance)
```

## The trick (say it out loud)
> "Add parent links → tree is a graph. BFS from target; number of levels = burn time."

## Code skeleton
```python
for nb in (node.left,node.right,parent.get(node)):
    if nb and nb not in visited: visited.add(nb); q.append(nb)
time+=1  # per level, init -1
```

## Complexity
- Time O(n) · Space O(n)

## This trick solves more
Nodes Distance K (863) · Infect Tree (2385) · Rotting Oranges (994) · Distance between nodes
