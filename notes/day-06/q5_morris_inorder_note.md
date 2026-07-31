# ⚡ Quick Note · Day 6 — Binary Tree · Q5 · Morris Inorder
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Inorder traversal in O(1) space (no stack/recursion)."

## Pattern
`Morris threading — temporary predecessor links`

## Visual in your head
```
no left → visit, go right
else find pred = rightmost of left subtree
  pred.right None → thread pred.right=cur, go left
  else → unthread, visit, go right
```

## The trick (say it out loud)
> "Thread the inorder predecessor back to cur; visit on return; then remove the thread."

## Code skeleton
```python
while cur:
    if not cur.left: visit; cur=cur.right
    else:
        pred=cur.left
        while pred.right and pred.right is not cur: pred=pred.right
        if not pred.right: pred.right=cur; cur=cur.left
        else: pred.right=None; visit; cur=cur.right
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
Inorder (94) · Preorder O(1) (144) · Recover BST (99) · Kth Smallest BST (230) · Flatten (114)
