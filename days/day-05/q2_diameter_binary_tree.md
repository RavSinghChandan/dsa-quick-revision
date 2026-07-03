# Q2 — Diameter of Binary Tree (LeetCode 543)

## 1. What is this question actually asking?

The diameter of a binary tree is the length of the longest path between any two nodes. The path may or may not pass through the root. Length is measured in number of edges (not nodes). So a path through a root connecting left depth 3 and right depth 4 has diameter 7. But a longer path may exist entirely within a subtree.

## 2. Pattern

DFS Post-order — at each node, diameter through that node = left_height + right_height; track global maximum

## 3. Understand with a Diagram

```
Tree:
          1
         / \
        2   3
       / \
      4   5

For each node, diameter through it = left_height + right_height:
Node 4: 0 + 0 = 0
Node 5: 0 + 0 = 0
Node 3: 0 + 0 = 0
Node 2: height(4)+height(5) = 1+1 = 2 ← current max
Node 1: height(2)+height(3) = 2+1 = 3 ← new max

Answer: 3 (path is 4→2→1→3 or 5→2→1→3, both length 3 edges)

Another example:
          1
         / \
        2   3
       / \
      4   5
         / \
        6   7

Node 6: 0
Node 7: 0
Node 5: 1+1=2
Node 4: 0
Node 2: height(4)+height(5) = 1+2 = 3 ← largest

At node 1: height(2)+height(3) = 3+1 = 4? 
Wait, height(2) = 1+max(height(4), height(5)) = 1+max(1,2) = 3
So at node 1: 3+1 = 4

But path through node 2 (4→2→5 side via 6): 1+3=? No:
At node 2: left_height(4)=1, right_height(5's subtree)=2
diameter through 2 = 1+2 = 3

Answer = max seen = 4 (path: 4→2→1→3 uses height 3+1=4? 
Actually path: 3←1→2→5→7 = 4 edges. ✓

Key insight: we pass height UP but update max diameter as we go.
```

## 4. Brute Force → Better → Optimal

### Brute Force — For every node, compute diameter through it
**Idea:** For each node, compute height of left and right subtrees separately. Diameter through that node = left + right. Take the maximum over all nodes.

```
function diameter_through(node):
    return height(node.left) + height(node.right)

max_diam = 0
for every node in tree:
    max_diam = max(max_diam, diameter_through(node))
return max_diam
```

Why slow: Computing height for each node takes O(n), done for every node → O(n²).

### Optimal — Single DFS with global variable
**Idea:** Combine height computation with diameter update. In the DFS, each node returns its height to the parent, but also updates a global maximum with left_height + right_height at that node.

```
max_diameter = 0

function height(node):
    if node is None: return 0
    left_h = height(node.left)
    right_h = height(node.right)
    max_diameter = max(max_diameter, left_h + right_h)
    return 1 + max(left_h, right_h)

height(root)
return max_diameter
```

O(n) time, O(h) space. Single pass.

**Trace on example [1,2,3,4,5]:**
```
height(4): left=0, right=0, update max=max(0,0+0)=0, return 1
height(5): left=0, right=0, update max=max(0,0+0)=0, return 1
height(2): left=1, right=1, update max=max(0,1+1)=2, return 2
height(3): left=0, right=0, update max=max(2,0+0)=2, return 1
height(1): left=2, right=1, update max=max(2,2+1)=3, return 3
Answer: 3
```

## 5. Pseudocode (Optimal)

```
function diameterOfBinaryTree(root):
    max_diam = [0]  // use list to allow mutation in nested function
    
    function height(node):
        if node is None: return 0
        left  = height(node.left)
        right = height(node.right)
        max_diam[0] = max(max_diam[0], left + right)
        return 1 + max(left, right)
    
    height(root)
    return max_diam[0]
```

## 6. Python Code

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def diameterOfBinaryTree(root):
    self_max = [0]
    
    def height(node):
        if not node:
            return 0
        left = height(node.left)
        right = height(node.right)
        self_max[0] = max(self_max[0], left + right)
        return 1 + max(left, right)
    
    height(root)
    return self_max[0]


def build(arr):
    if not arr: return None
    from collections import deque
    root = TreeNode(arr[0])
    q = deque([root]); i = 1
    while q and i < len(arr):
        node = q.popleft()
        if i < len(arr) and arr[i] is not None:
            node.left = TreeNode(arr[i]); q.append(node.left)
        i += 1
        if i < len(arr) and arr[i] is not None:
            node.right = TreeNode(arr[i]); q.append(node.right)
        i += 1
    return root


# Test 1 — LeetCode example 1
root = build([1, 2, 3, 4, 5])
print(diameterOfBinaryTree(root))   # 3

# Test 2 — LeetCode example 2 (single node)
print(diameterOfBinaryTree(TreeNode(1)))   # 0

# Test 3 — diameter NOT through root
n1 = TreeNode(1)
n2 = TreeNode(2)
n3 = TreeNode(3)
n4 = TreeNode(4)
n5 = TreeNode(5)
n1.right = n3
n1.left  = n2
n2.left  = n4
n2.right = n5
# Path 4→2→5 has length 2, path 4→2→1→3 has length 3
print(diameterOfBinaryTree(n1))   # 3

# Test 4 — skewed tree
root = TreeNode(1)
root.left = TreeNode(2)
root.left.left = TreeNode(3)
print(diameterOfBinaryTree(root))   # 2
```

## 7. Complexity Table

| Approach              | Time  | Space | Notes                           |
|-----------------------|-------|-------|---------------------------------|
| Brute Force (O(n²))   | O(n²) | O(n)  | height() called for every node  |
| Single DFS (optimal)  | O(n)  | O(h)  | Height + diameter in one pass   |

## 8. Edge Cases to Remember

- Empty tree — return 0
- Single node — left=0, right=0, diameter through root=0
- Diameter doesn't pass through root — global max catches it during subtree traversal
- All nodes in a line (skewed) — diameter = n-1 (one path from top to bottom)
- Answer is 0 for a single edge tree (2 nodes) — wait, n1—n2 gives left+right = 1+0 = 1 at parent, diameter = 1

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Height of Binary Tree                 | Same DFS, just return the height                    | LC 104     |
| Diameter of N-ary Tree                | Extend to multiple children                         | LC 1522    |
| Longest Univalue Path                 | Same pattern but only along same-value edges        | LC 687     |
| Binary Tree Maximum Path Sum          | Same structure — track path sum instead of length   | LC 124     |
| Balanced Binary Tree                  | Check if |left_h - right_h| ≤ 1 at every node     | LC 110     |
| Sum Root to Leaf Numbers              | DFS building number top-down                        | LC 129     |
| Path Sum II                           | DFS tracking path                                   | LC 113     |
| Minimum Depth                         | DFS but min, careful with leaf nodes                | LC 111     |

## 10. The ONE Trick to Remember

**"Return height up the tree, but update global diameter = left + right at each node."**

The diameter through any node equals the height of its left subtree plus the height of its right subtree. So compute heights post-order (bottom-up), and at each node update a global maximum with left_height + right_height. The height you return upward is 1 + max(left, right). One DFS pass solves both height and diameter simultaneously.
