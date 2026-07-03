# Q4 — Lowest Common Ancestor (2-min card) — LC 236

**Find deepest node that is ancestor of both p and q in a binary tree.**

**Pattern:** DFS Post-order — return p/q when found; if both children return non-null → current node is LCA

```
          3
        /   \
       5     1
      / \   / \
     6   2 0   8
        / \
       7   4

LCA(6, 4):
dfs(6)→node(6), dfs(2): dfs(7)=None, dfs(4)=node(4) → return node(4)
dfs(5): left=node(6), right=node(4) → BOTH non-null → return node(5)
dfs(1)→None
dfs(3): left=node(5), right=None → return node(5) ✓
```

**The trick:** "If both subtrees find something — you're the LCA; otherwise pass up whichever found something."

```python
def lca(root, p, q):
    if not root or root == p or root == q: return root
    left  = lca(root.left, p, q)
    right = lca(root.right, p, q)
    if left and right: return root   # split here → LCA!
    return left or right             # pass up the found one
```

**Complexity:** Time O(n) | Space O(h)

**Same pattern solves:** LCA of BST (LC 235), LCA Deepest Leaves (LC 1123), All Nodes Distance K (LC 863), Step Directions (LC 2096), Binary Tree Pruning (LC 814), Count Nodes Avg Subtree (LC 2265), Smallest Common Region (LC 1257).
