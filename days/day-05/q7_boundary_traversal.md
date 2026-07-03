# Q7 — Boundary Traversal of Binary Tree

## 1. What is this question actually asking?

Given a binary tree, print all boundary nodes in anticlockwise order starting from the root. The boundary consists of three parts: the left boundary (from root to the leftmost leaf), all the leaf nodes (from left to right), and the right boundary (from the rightmost leaf up to root, reversed). The root is included once. Nodes that are already leaves are included in the leaf section only.

## 2. Pattern

DFS — three separate traversals: left boundary top-down (excluding leaves), all leaves left-to-right, right boundary bottom-up (excluding leaves)

## 3. Understand with a Diagram

```
Tree:
             20
           /    \
          8      22
         / \       \
        4   12      25
           /  \
          10   14

Boundary (anticlockwise):
                   ┌─ 20 ─┐
                   │       │
         Left      │       │   Right
         boundary  │       │   boundary
         (top-down)│       │   (bottom-up)
                   │       │
                   8       22
                   4             (not 22 since 25 is below it)
                            25

Leaves (left to right): 4, 10, 14, 25

Full boundary:
20 (root)
↓ Left boundary (top-down, exclude leaves): 8, 4
  Wait: 4 IS a leaf. So left boundary = just 8.
↓ Leaves (all, L to R): 4, 10, 14, 25
↓ Right boundary (bottom-up, exclude leaves): 22 (25 is leaf so excluded)
  Reversed: 22

Result: [20, 8, 4, 10, 14, 25, 22]

Breakdown:
1. Root: [20]
2. Left boundary (root.left down, no leaves): 
   - 8 has children → include 8
   - 8.left=4 is leaf → stop (leaf captured in step 3)
   → [8]
3. Leaves left to right: [4, 10, 14, 25]
4. Right boundary (root.right down, no leaves, REVERSED):
   - 22 has right child 25 → include 22
   - 25 is leaf → stop
   → collected [22], reversed → [22]
   
Final: [20] + [8] + [4,10,14,25] + [22]
     = [20, 8, 4, 10, 14, 25, 22] ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — Mark boundary then traverse
**Idea:** Traverse the entire tree, mark which nodes are boundary (leftmost per level, rightmost per level, leaf). Collect in order.

Why slow: O(n²) if done naively, complex marking logic.

### Optimal — Three Separate DFS Passes
**Idea:**
1. Add root (if not a leaf)
2. Left boundary: traverse down root.left, always going left first (then right), add non-leaves
3. All leaves: DFS the whole tree, add nodes with no children
4. Right boundary: traverse down root.right, always going right first (then left), add non-leaves, then reverse

```
function boundary(root):
    if not root: return []
    
    result = []
    
    if not isLeaf(root):
        result.append(root.val)
    
    addLeftBoundary(root.left, result)
    addLeaves(root, result)
    addRightBoundary(root.right, result)
    
    return result

function addLeftBoundary(node, result):
    while node:
        if not isLeaf(node): result.append(node.val)
        if node.left: node = node.left
        else: node = node.right

function addLeaves(node, result):
    if not node: return
    if isLeaf(node): result.append(node.val); return
    addLeaves(node.left, result)
    addLeaves(node.right, result)

function addRightBoundary(node, result):
    temp = []
    while node:
        if not isLeaf(node): temp.append(node.val)
        if node.right: node = node.right
        else: node = node.left
    result.extend(reversed(temp))
```

O(n) time, O(n) space.

## 5. Pseudocode (Optimal)

```
function isLeaf(node): return not node.left and not node.right

function boundaryTraversal(root):
    if root is None: return []
    result = []
    
    if not isLeaf(root):
        result.append(root.val)
    
    // Left boundary (top-down, no leaves)
    node = root.left
    while node:
        if not isLeaf(node): result.append(node.val)
        node = node.left if node.left else node.right
    
    // All leaves (DFS left-to-right)
    addLeaves(root, result)
    
    // Right boundary (top-down, no leaves, then reversed)
    temp = []
    node = root.right
    while node:
        if not isLeaf(node): temp.append(node.val)
        node = node.right if node.right else node.left
    result.extend(reversed(temp))
    
    return result
```

## 6. Python Code

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def is_leaf(node):
    return not node.left and not node.right


def add_leaves(node, result):
    if not node:
        return
    if is_leaf(node):
        result.append(node.val)
        return
    add_leaves(node.left, result)
    add_leaves(node.right, result)


def boundary_traversal(root):
    if not root:
        return []
    
    result = []
    
    # Root (if not a leaf)
    if not is_leaf(root):
        result.append(root.val)
    
    # Left boundary (top-down, no leaves)
    node = root.left
    while node:
        if not is_leaf(node):
            result.append(node.val)
        node = node.left if node.left else node.right
    
    # All leaves (left to right)
    add_leaves(root, result)
    
    # Right boundary (collected top-down, added reversed)
    temp = []
    node = root.right
    while node:
        if not is_leaf(node):
            temp.append(node.val)
        node = node.right if node.right else node.left
    result.extend(reversed(temp))
    
    return result


# Build test tree
root = TreeNode(20)
root.left  = TreeNode(8)
root.right = TreeNode(22)
root.left.left  = TreeNode(4)
root.left.right = TreeNode(12)
root.right.right = TreeNode(25)
root.left.right.left  = TreeNode(10)
root.left.right.right = TreeNode(14)

print(boundary_traversal(root))
# [20, 8, 4, 10, 14, 25, 22]

# Test 2 — single node
print(boundary_traversal(TreeNode(1)))   # [1]

# Test 3 — two levels
root2 = TreeNode(1)
root2.left = TreeNode(2)
root2.right = TreeNode(3)
print(boundary_traversal(root2))   # [1, 2, 3]
```

## 7. Complexity Table

| Approach            | Time  | Space | Notes                             |
|---------------------|-------|-------|-----------------------------------|
| Marking + collect   | O(n)  | O(n)  | Complex marking logic             |
| Three DFS passes    | O(n)  | O(n)  | Clean separation into 3 parts     |

## 8. Edge Cases to Remember

- Single node (root is a leaf) — return [root.val]; root shouldn't be added in step 1 (it's a leaf)
- Root has no left child — left boundary is empty; leaves still collected
- Root has no right child — right boundary is empty
- All nodes are leaves — only root (if leaf, add to leaves part) and all leaves collected
- Left boundary node that IS a leaf — skip it (it'll appear in leaves section)
- Right boundary node that IS a leaf — skip it (it'll appear in leaves section)

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Level Order Traversal                 | Visit nodes level by level                          | LC 102     |
| Right Side View                       | Rightmost node per level                            | LC 199     |
| Left Side View                        | Leftmost node per level                             | GFG        |
| Print all paths root to leaf          | DFS with path tracking                              | LC 113     |
| Vertical Order Traversal              | Group nodes by column                               | LC 987     |
| All Nodes Distance K                  | BFS with distance tracking                          | LC 863     |
| Morris Traversal                      | In-order without recursion/stack                    | —          |
| Diagonal Traversal                    | Group nodes by diagonal                             | GFG        |

## 10. The ONE Trick to Remember

**"Three parts: left boundary top-down, leaves left-to-right, right boundary bottom-up (reversed)."**

Split the boundary into three non-overlapping parts. Left boundary: go down root.left always preferring left, collect non-leaves top-down. Leaves: full DFS collect all leaf nodes. Right boundary: go down root.right always preferring right, collect non-leaves top-down, then reverse before adding (because we need bottom-up). Root added separately (if not a leaf). This clean separation handles all edge cases.
