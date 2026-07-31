# ⚡ Quick Note · Day 7 — BST & Heap · Q2 · Inorder Succ/Pred
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Next-larger (successor) and next-smaller (predecessor) of a value in a BST."

## Pattern
`BST guided walk — track candidate while descending`

## Visual in your head
```
successor: cur.val>target → succ=cur, go left; else go right
predecessor: cur.val<target → pred=cur, go right; else go left
```

## The trick (say it out loud)
> "Successor: record candidate on left turns (cur>target). Predecessor: mirror on right turns."

## Code skeleton
```python
# successor
while cur:
    if cur.val>target: succ=cur; cur=cur.left
    else: cur=cur.right
```

## Complexity
- Time O(h) · Space O(1)

## This trick solves more
Inorder Successor (285) · Ceil/Floor BST · Closest value (270) · BST Iterator (173) · Kth smallest (230)
