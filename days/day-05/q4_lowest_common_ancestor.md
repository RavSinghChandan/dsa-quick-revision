# Q4 — Lowest Common Ancestor (LeetCode 236)

## 1. What is this question actually asking?

Given a binary tree (not BST) and two nodes p and q, find their Lowest Common Ancestor (LCA). The LCA is the deepest node that is an ancestor of both p and q. A node can be an ancestor of itself. For example, in a tree where 5 is the parent of 6 and 2, the LCA of 6 and 2 is 5.

## 2. Pattern

DFS Post-order — if a node is p or q, return it; if both subtrees return non-null, current node is the LCA

## 3. Understand with a Diagram

```
Tree:
          3
        /   \
       5     1
      / \   / \
     6   2 0   8
        / \
       7   4

Find LCA(5, 1):
  LCA is 3 (root is ancestor of both)

Find LCA(5, 4):
  5 is an ancestor of 4 → LCA is 5

Recursive logic:
  At node 6: not p(5) nor q(4) → return None (leaf doesn't match)
  At node 7: return None
  At node 4: return node(4) ← found q!
  At node 2: left=None, right=node(4) → return node(4) upward
  At node 5: this is p itself → return node(5) upward
             (even though right subtree would return node(4))
             Wait — the algorithm returns the node if it IS p or q,
             before checking children further.

Actually the algorithm:
  if node is None: return None
  if node == p or node == q: return node
  left  = LCA(node.left)
  right = LCA(node.right)
  if left and right: return node  // both subtrees found something → this is LCA
  return left or right            // pass up whichever side found something

Trace LCA(5,4):
dfs(3): left=dfs(5), right=dfs(1)
  dfs(5): node==p(5) → return node(5) immediately
  dfs(1): p or q? no. left=dfs(0)=None, right=dfs(8)=None → return None
  Back at dfs(3): left=node(5), right=None → return node(5)

Wait — why? Because 5 IS the ancestor of 4 (4 is under 5).
When dfs(5) returns immediately (since node==p), we're saying "found p here, 
pass it upward." Since dfs(3) sees left=5, right=None, it passes 5 up.
The LCA is 5 — and indeed 5 is the LCA of 5 and 4. ✓

Trace LCA(6,4):
dfs(3): 
  dfs(5): not p or q
    dfs(6): node==p? No. 6==p(6) YES → return node(6)
    dfs(2): not p or q
      dfs(7): None
      dfs(4): node==q(4) YES → return node(4)
      back at dfs(2): left=None, right=node(4) → return node(4)
    back at dfs(5): left=node(6), right=node(4) → BOTH non-null → return node(5)
  dfs(1): ... → return None
  back at dfs(3): left=node(5), right=None → return node(5)
Answer: 5 ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — Path from root to each node
**Idea:** Find path from root to p, find path from root to q. The last common node in both paths is the LCA.

```
path_p = findPath(root, p)   // [3,5,6] for p=6
path_q = findPath(root, q)   // [3,5,2,4] for q=4

// Find last common element
LCA = None
for i in range(min(len(path_p), len(path_q))):
    if path_p[i] == path_q[i]: LCA = path_p[i]
    else: break
return LCA
```

Why not optimal: O(n) to find each path, O(n) space for paths.

### Optimal — Single DFS
**Idea:** DFS returns the found node (p, q, or LCA). If both children return non-null, the current node is the LCA. Otherwise pass up whichever returned non-null.

```
function lca(node, p, q):
    if node is None: return None
    if node == p or node == q: return node
    
    left  = lca(node.left, p, q)
    right = lca(node.right, p, q)
    
    if left and right: return node  // this is the LCA
    return left if left else right  // pass up the found one
```

O(n) time, O(h) space.

## 5. Pseudocode (Optimal)

```
function lowestCommonAncestor(root, p, q):
    if root is None: return None
    if root == p or root == q: return root
    
    left  = lowestCommonAncestor(root.left, p, q)
    right = lowestCommonAncestor(root.right, p, q)
    
    if left is not None and right is not None:
        return root     // p found in one subtree, q in the other
    
    return left if left is not None else right
```

## 6. Python Code

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def lowestCommonAncestor(root, p, q):
    if not root:
        return None
    if root == p or root == q:
        return root
    
    left  = lowestCommonAncestor(root.left, p, q)
    right = lowestCommonAncestor(root.right, p, q)
    
    if left and right:
        return root
    return left or right


# Build test tree: [3,5,1,6,2,0,8,None,None,7,4]
root = TreeNode(3)
n5 = TreeNode(5); n1 = TreeNode(1)
n6 = TreeNode(6); n2 = TreeNode(2); n0 = TreeNode(0); n8 = TreeNode(8)
n7 = TreeNode(7); n4 = TreeNode(4)
root.left=n5; root.right=n1
n5.left=n6; n5.right=n2; n1.left=n0; n1.right=n8
n2.left=n7; n2.right=n4

# Test 1 — LCA(5, 1) = 3
result = lowestCommonAncestor(root, n5, n1)
print(result.val)   # 3

# Test 2 — LCA(5, 4) = 5
result = lowestCommonAncestor(root, n5, n4)
print(result.val)   # 5

# Test 3 — LCA(6, 4) = 5
result = lowestCommonAncestor(root, n6, n4)
print(result.val)   # 5

# Test 4 — root is one of the nodes
result = lowestCommonAncestor(root, root, n4)
print(result.val)   # 3
```

## 7. Complexity Table

| Approach          | Time  | Space | Notes                             |
|-------------------|-------|-------|-----------------------------------|
| Path-based        | O(n)  | O(n)  | Store two root-to-node paths      |
| Single DFS        | O(n)  | O(h)  | One pass — optimal                |

## 8. Edge Cases to Remember

- One node is an ancestor of the other — algorithm handles: returns the ancestor immediately when found (no need to check deeper)
- Both p and q are the same node — returns that node (root==p triggers before going deeper)
- Root is p or q — returns root immediately
- One of the nodes doesn't exist in tree — problem guarantees both exist in the tree; if not, need to handle None return from both subtrees
- LCA is the root — both subtrees return non-null, so root is returned

## 9. Similar Questions

| Question                                   | Where Pattern Applies                          | LeetCode # |
|--------------------------------------------|------------------------------------------------|------------|
| LCA of BST                                 | Use BST property for O(h) without full scan    | LC 235     |
| LCA of Deepest Leaves                      | Find LCA of all deepest nodes                  | LC 1123    |
| Smallest Common Region                     | LCA in tree of regions (strings)               | LC 1257    |
| Find Distance Between Two Nodes            | LCA to compute distance                        | GFG        |
| All Nodes Distance K in Binary Tree        | LCA + BFS for distance                         | LC 863     |
| Step-By-Step Directions from Root to Target| Path finding using LCA                         | LC 2096    |
| Count Nodes Equal to Average of Subtree    | Post-order DFS aggregation                     | LC 2265    |
| Binary Tree Pruning                        | Post-order decision at each node               | LC 814     |

## 10. The ONE Trick to Remember

**"Return the node if it's p or q; if both children return non-null, current node is the LCA."**

The DFS returns a node upward whenever it finds p or q. If both the left and right subtrees return a node (meaning p and q are split across them), then the current node is the split point — the LCA. If only one side returns, pass it up (because both p and q are on the same side, and that side already found the LCA or one of the nodes).
