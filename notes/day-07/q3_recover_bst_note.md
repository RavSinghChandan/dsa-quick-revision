# ⚡ Quick Note · Day 7 — BST & Heap · Q3 · Recover BST
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Two nodes swapped in a BST — fix it (swap values back)."

## Pattern
`Inorder traversal — find the dip(s), swap`

## Visual in your head
```
inorder must be sorted
violation prev.val>cur.val:
  first None → first=prev (bigger)
  second = cur (smaller)
swap first.val, second.val
```

## The trick (say it out loud)
> "Inorder must be sorted. first = bigger node at first dip, second = smaller at last dip. Swap values."

## Code skeleton
```python
if prev and prev.val>node.val:
    if first is None: first=prev
    second=node
prev=node
# then swap first.val, second.val
```

## Complexity
- Time O(n) · Space O(h) (O(1) Morris)

## This trick solves more
Recover BST (99) · Validate BST (98) · Kth smallest (230) · Min abs diff BST (530) · Find mode
