# Day 6 — Binary Tree · Q1 · Lowest Common Ancestor in a Binary Tree

---

## What is this question actually asking?

Find the **lowest (deepest) node** that is an ancestor of both nodes `p` and `q`.

```
     3
    / \
   5   1
  / \
 6   2      LCA(6, 2) = 5 ;  LCA(6, 1) = 3
```

> A general binary tree (not a BST) — so you can't use value ordering. The clean answer is a single **post-order DFS** that bubbles findings up.

---

## Pattern

```
PATTERN: Post-order DFS — return where p and q are found; LCA is where both meet
```

Whenever you see: *"lowest common ancestor / where two subtrees converge"* → recurse left and right; if `p` and `q` are found in different subtrees of a node, that node is the LCA. If both in one subtree, the LCA is deeper there.

---

## Understand with a diagram

```
dfs(node):
  if node is None or node == p or node == q: return node   ← found a target
  left  = dfs(node.left)
  right = dfs(node.right)
  if left and right: return node     ← p and q on opposite sides → LCA
  return left or right               ← both on one side (or none)

LCA(6,2): at node 5, left finds 6, right finds 2 → 5 is LCA
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — find both paths, compare — O(n) time, O(n) space
Get root→p and root→q paths, find last common node. Works, two passes + storage.

### 🟢 Optimal — single post-order DFS — O(n) time, O(h) space
One recursion returns the meeting node. Elegant, one pass.

---

## Pseudocode (Optimal)

```
function lca(root, p, q):
    if root is None or root == p or root == q:
        return root
    left  = lca(root.left, p, q)
    right = lca(root.right, p, q)
    if left and right: return root      ← split → this is LCA
    return left if left else right
```

---

## Python Code

```python
class TreeNode:
    def __init__(self, val, left=None, right=None):
        self.val = val; self.left = left; self.right = right

def lowest_common_ancestor(root, p, q):
    if root is None or root is p or root is q:
        return root
    left = lowest_common_ancestor(root.left, p, q)
    right = lowest_common_ancestor(root.right, p, q)
    if left and right:
        return root            # p and q found on opposite sides
    return left if left else right


# Build tree and test
n3 = TreeNode(3)
n5 = TreeNode(5); n1 = TreeNode(1)
n6 = TreeNode(6); n2 = TreeNode(2)
n3.left, n3.right = n5, n1
n5.left, n5.right = n6, n2
print(lowest_common_ancestor(n3, n6, n2).val)   # 5
print(lowest_common_ancestor(n3, n6, n1).val)   # 3
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Path compare | O(n) | O(n) |
| Post-order DFS | O(n) | O(h) recursion |

---

## Edge Cases to remember

- **Assume both p and q exist** — the standard version guarantees this; otherwise add a "found both" check.
- **A node can be its own ancestor** — `LCA(5, 6) = 5` if 6 is in 5's subtree; the base case `root == p` handles it.
- **Identity vs value** — compare node references (`is`) for the general problem.
- **Post-order** — you must compute both children before deciding the current node.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | LCA in BST (Day 7 Q1) | Use value ordering |
| 2 | LCA with parent pointers (LC 1650) | Two-pointer like Y-list |
| 3 | LCA of deepest leaves (LC 1123) | DFS + depth |
| 4 | Distance between two nodes | LCA + depths |
| 5 | Path between two nodes | LCA split |
| 6 | Nodes at distance K (LC 863) | Parent map + BFS |
| 7 | Kth ancestor | Binary lifting |
| 8 | Smallest subtree containing all deepest | DFS |

---

## The ONE trick to remember

```
"POST-ORDER DFS: FOUND ON BOTH SIDES → THIS NODE IS THE LCA."
```

Return the target node when found. At each node, if one target came back from the left and the other from the right, this node is where they meet — the LCA. Otherwise pass up whichever side found something.

> Memory hook: "The LCA is the first node where the two searches come from different children."

---
