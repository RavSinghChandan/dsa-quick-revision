# ⚡ Quick Note · Day 6 — Binary Tree · Q1 · LCA (Binary Tree)
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Lowest common ancestor of p and q in a general binary tree."

## Pattern
`Post-order DFS — meet point of the two searches`

## Visual in your head
```
if node None/p/q: return node
left=dfs(L); right=dfs(R)
if left and right: return node  ← LCA
return left or right
```

## The trick (say it out loud)
> "Post-order DFS. If p and q come back from different children, this node is the LCA."

## Code skeleton
```python
if not root or root is p or root is q: return root
l=lca(root.left); r=lca(root.right)
return root if l and r else (l or r)
```

## Complexity
- Time O(n) · Space O(h)

## This trick solves more
LCA in BST · LCA w/ parent ptr (1650) · Distance between nodes · Nodes at distance K (863) · Kth ancestor
