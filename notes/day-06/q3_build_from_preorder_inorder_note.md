# ⚡ Quick Note · Day 6 — Binary Tree · Q3 · Build from Pre+In
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Rebuild the tree from preorder + inorder."

## Pattern
`Recursion: preorder→root, inorder→left/right split (+ index map)`

## Visual in your head
```
pre[0]=root; find in inorder at mid
inorder[lo..mid-1]=left, [mid+1..hi]=right
build LEFT first (pre pointer advances)
```

## The trick (say it out loud)
> "Preorder[0] is the root; its inorder index splits left|right. Build left before right. Map for O(1)."

## Code skeleton
```python
root=TreeNode(preorder[pre[0]]); pre[0]+=1
mid=idx[root.val]
root.left=helper(lo,mid-1); root.right=helper(mid+1,hi)
```

## Complexity
- Time O(n) · Space O(n)

## This trick solves more
Inorder+Postorder (106) · Pre+Post (889) · Serialize/Deserialize (297) · BST from preorder (1008)
