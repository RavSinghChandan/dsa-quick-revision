# ⚡ Quick Note · Day 6 — Binary Tree · Q2 · Bottom View
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Nodes visible looking at the tree from below (last per column)."

## Pattern
`BFS level-order + horizontal-distance map (overwrite)`

## Visual in your head
```
root hd=0, left=hd-1, right=hd+1
BFS; map[hd]=node.val (overwrite)
output sorted by hd
```

## The trick (say it out loud)
> "HD map + BFS. Overwrite per column → bottom-most wins. Top view = keep FIRST per column."

## Code skeleton
```python
q=deque([(root,0)])
while q:
    node,hd=q.popleft(); hd_map[hd]=node.val
    if node.left: q.append((node.left,hd-1))
    if node.right: q.append((node.right,hd+1))
return [hd_map[h] for h in sorted(hd_map)]
```

## Complexity
- Time O(n log n) · Space O(n)

## This trick solves more
Top View · Vertical Order (987) · Right/Left View (199) · Level Order (102) · Diagonal traversal
