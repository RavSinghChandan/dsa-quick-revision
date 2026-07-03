# Q1 — Height of Binary Tree (LeetCode 104)

## 1. What is this question actually asking?

Given a binary tree, find its maximum depth — the number of nodes along the longest path from the root node down to the farthest leaf node. Also called the height of the tree. For example, a tree with just a root node has height 1. An empty tree has height 0.

## 2. Pattern

DFS (Depth-First Search) — recursive post-order traversal: height = 1 + max(left height, right height)

## 3. Understand with a Diagram

```
Tree:
        3
       / \
      9   20
         /  \
        15   7

Height calculation (post-order — process children before parent):

Node 9:  leaf → height = 1
Node 15: leaf → height = 1
Node 7:  leaf → height = 1
Node 20: max(height(15), height(7)) + 1 = max(1,1) + 1 = 2
Node 3:  max(height(9), height(20)) + 1 = max(1,2) + 1 = 3

Answer: 3

Recursive call tree:
maxDepth(3)
├── maxDepth(9) → 1
└── maxDepth(20)
    ├── maxDepth(15) → 1
    └── maxDepth(7)  → 1
    → max(1,1)+1 = 2
→ max(1,2)+1 = 3

Unbalanced tree:
    1
     \
      2
       \
        3
         \
          4
Answer: 4
```

## 4. Brute Force → Better → Optimal

### Brute Force — BFS level by level
**Idea:** Use BFS (level order traversal). Count how many levels you traverse before running out of nodes.

```
if not root: return 0
queue = [root]
depth = 0

while queue:
    depth += 1
    for i in range(len(queue)):  // process entire level
        node = queue.pop(0)
        if node.left: queue.append(node.left)
        if node.right: queue.append(node.right)

return depth
```

O(n) time, O(n) space for the queue (can hold entire level).

### Optimal — DFS Recursive
**Idea:** Base case: empty node has height 0. For any node, height = 1 + max(height of left subtree, height of right subtree).

```
function maxDepth(root):
    if root is None: return 0
    return 1 + max(maxDepth(root.left), maxDepth(root.right))
```

O(n) time, O(h) space where h is height (call stack). For balanced trees O(log n), worst case O(n).

### Optimal Iterative — DFS with stack
**Idea:** Use explicit stack, track (node, depth) pairs.

```
stack = [(root, 1)]
max_depth = 0
while stack:
    node, depth = stack.pop()
    if node:
        max_depth = max(max_depth, depth)
        stack.append((node.left, depth+1))
        stack.append((node.right, depth+1))
return max_depth
```

## 5. Pseudocode (Optimal)

```
function maxDepth(root):
    if root is None:
        return 0
    left_height  = maxDepth(root.left)
    right_height = maxDepth(root.right)
    return 1 + max(left_height, right_height)
```

## 6. Python Code

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def maxDepth(root):
    if not root:
        return 0
    return 1 + max(maxDepth(root.left), maxDepth(root.right))


# Helper to build tree from list (level-order, None = missing node)
def build(arr):
    if not arr: return None
    from collections import deque
    root = TreeNode(arr[0])
    q = deque([root])
    i = 1
    while q and i < len(arr):
        node = q.popleft()
        if i < len(arr) and arr[i] is not None:
            node.left = TreeNode(arr[i])
            q.append(node.left)
        i += 1
        if i < len(arr) and arr[i] is not None:
            node.right = TreeNode(arr[i])
            q.append(node.right)
        i += 1
    return root


# Test 1 — LeetCode example
root = build([3, 9, 20, None, None, 15, 7])
print(maxDepth(root))   # 3

# Test 2 — single node
print(maxDepth(TreeNode(1)))   # 1

# Test 3 — empty tree
print(maxDepth(None))   # 0

# Test 4 — skewed right
root = build([1, None, 2, None, None, None, 3])
# Let's build manually
n1 = TreeNode(1)
n2 = TreeNode(2)
n3 = TreeNode(3)
n1.right = n2
n2.right = n3
print(maxDepth(n1))   # 3

# Test 5 — perfect binary tree
root = build([1, 2, 3, 4, 5, 6, 7])
print(maxDepth(root))   # 3
```

## 7. Complexity Table

| Approach      | Time  | Space  | Notes                           |
|---------------|-------|--------|---------------------------------|
| BFS           | O(n)  | O(n)   | Queue holds widest level        |
| DFS Recursive | O(n)  | O(h)   | h=height, best O(log n) balanced|
| DFS Iterative | O(n)  | O(h)   | Same as recursive in practice   |

## 8. Edge Cases to Remember

- Empty tree (root = None) — return 0
- Single node — return 1
- Left-skewed (all left children) — height = n
- Right-skewed (all right children) — height = n
- Perfect binary tree — height = log₂(n+1)
- Tree where one subtree is much deeper than the other — max() handles correctly

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Minimum Depth of Binary Tree          | Same DFS but take min, handle leaf carefully        | LC 111     |
| Diameter of Binary Tree               | Use height to compute diameter                      | LC 543     |
| Balanced Binary Tree                  | Check height of both subtrees, ensure diff ≤ 1     | LC 110     |
| Count Complete Tree Nodes             | Use height to count efficiently                     | LC 222     |
| Path Sum                              | DFS subtracting from sum                            | LC 112     |
| Maximum Path Sum                      | Post-order DFS tracking gains                       | LC 124     |
| Sum of Left Leaves                    | DFS marking left/right nodes                        | LC 404     |
| Binary Tree Right Side View           | BFS or DFS rightmost per level                      | LC 199     |

## 10. The ONE Trick to Remember

**"Height = 1 + max(left height, right height) — base case is 0 for None."**

Three-line recursive solution. The postorder nature (process children first, then parent) naturally bubbles up the maximum depth. The base case of returning 0 for None means leaf nodes return 1 (since 1 + max(0,0) = 1). Same DFS template extends to diameter, balanced check, and path sum problems.
