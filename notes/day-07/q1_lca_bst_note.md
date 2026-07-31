# ⚡ Quick Note · Day 7 — BST & Heap · Q1 · LCA in BST
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "LCA of two nodes in a BST (use ordering)."

## Pattern
`BST value comparison — walk to the split point`

## Visual in your head
```
both < cur → go left
both > cur → go right
else → cur is LCA (split)
```

## The trick (say it out loud)
> "Both smaller go left, both larger go right, split point is the LCA. O(h), O(1)."

## Code skeleton
```python
while cur:
    if p.val<cur.val and q.val<cur.val: cur=cur.left
    elif p.val>cur.val and q.val>cur.val: cur=cur.right
    else: return cur
```

## Complexity
- Time O(h) · Space O(1)

## This trick solves more
LCA Binary Tree (general) · Search BST (700) · Insert BST (701) · Closest value (270) · Two Sum BST
