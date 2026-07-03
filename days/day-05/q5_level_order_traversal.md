# Q5 — Level Order Traversal (LeetCode 102)

## 1. What is this question actually asking?

Given a binary tree, return its level order traversal as a list of lists. Each inner list contains the values of all nodes at that depth. The root is at level 0, its children at level 1, and so on. Also called BFS traversal — you visit nodes level by level, left to right within each level.

## 2. Pattern

BFS with a queue — process nodes level by level; capture current level size before processing to separate levels

## 3. Understand with a Diagram

```
Tree:
        3
       / \
      9   20
         /  \
        15   7

Level 0: [3]
Level 1: [9, 20]
Level 2: [15, 7]

Output: [[3], [9, 20], [15, 7]]

BFS Queue trace:
Start: queue = [3]

Level 0 (size=1):
  dequeue 3, add to level=[3]
  enqueue 3.left=9, 3.right=20
  queue = [9, 20]
  result = [[3]]

Level 1 (size=2):
  dequeue 9, add to level=[9]
  9 has no children
  dequeue 20, add to level=[9,20]
  enqueue 20.left=15, 20.right=7
  queue = [15, 7]
  result = [[3], [9,20]]

Level 2 (size=2):
  dequeue 15, add to level=[15]
  15 has no children
  dequeue 7, add to level=[15,7]
  7 has no children
  queue = []
  result = [[3], [9,20], [15,7]]

Queue state at each level:
┌────────────────────────────────┐
│ Initial: [3]                   │
│          ↓                     │
│ After L0: [9, 20]              │
│          ↓                     │
│ After L1: [15, 7]              │
│          ↓                     │
│ After L2: []  (done)           │
└────────────────────────────────┘
```

## 4. Brute Force → Better → Optimal

### Brute Force — DFS with depth tracking
**Idea:** DFS traversal, pass current depth as parameter. Add each node's value to result[depth].

```
function dfs(node, depth, result):
    if not node: return
    if depth == len(result): result.append([])
    result[depth].append(node.val)
    dfs(node.left, depth+1, result)
    dfs(node.right, depth+1, result)
```

Works but is a DFS masquerading as BFS. O(n) time, O(n) space.

### Optimal — BFS with Level Batching
**Idea:** Use a queue. Before processing each level, record the current queue size — that's how many nodes are at this level. Process exactly that many nodes, collecting their values, and enqueue their children. This naturally separates levels.

```
queue = [root]
result = []

while queue:
    level_size = len(queue)
    level_vals = []
    
    for _ in range(level_size):
        node = queue.popleft()
        level_vals.append(node.val)
        if node.left:  queue.append(node.left)
        if node.right: queue.append(node.right)
    
    result.append(level_vals)

return result
```

O(n) time, O(n) space.

## 5. Pseudocode (Optimal)

```
function levelOrder(root):
    if root is None: return []
    
    queue = deque([root])
    result = []
    
    while queue:
        level_size = len(queue)
        level = []
        
        for i from 0 to level_size-1:
            node = queue.popleft()
            level.append(node.val)
            if node.left:  queue.append(node.left)
            if node.right: queue.append(node.right)
        
        result.append(level)
    
    return result
```

## 6. Python Code

```python
from collections import deque

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def levelOrder(root):
    if not root:
        return []
    
    queue = deque([root])
    result = []
    
    while queue:
        level_size = len(queue)
        level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            if node.left:  queue.append(node.left)
            if node.right: queue.append(node.right)
        
        result.append(level)
    
    return result


def build(arr):
    if not arr: return None
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


# Test 1 — LeetCode example
root = build([3, 9, 20, None, None, 15, 7])
print(levelOrder(root))   # [[3], [9, 20], [15, 7]]

# Test 2 — single node
print(levelOrder(TreeNode(1)))   # [[1]]

# Test 3 — empty tree
print(levelOrder(None))   # []

# Test 4 — full binary tree
root = build([1, 2, 3, 4, 5, 6, 7])
print(levelOrder(root))   # [[1], [2, 3], [4, 5, 6, 7]]

# Test 5 — skewed
root = build([1, 2, None, 3, None])
print(levelOrder(root))   # [[1], [2], [3]]
```

## 7. Complexity Table

| Approach          | Time  | Space | Notes                           |
|-------------------|-------|-------|---------------------------------|
| DFS with depth    | O(n)  | O(h)  | Not naturally level-by-level    |
| BFS with batching | O(n)  | O(n)  | Queue holds widest level        |

## 8. Edge Cases to Remember

- Empty tree — return [] immediately
- Single node — one level with one element [[root.val]]
- Skewed tree — each level has one node
- Level with None children — only append non-None children to queue
- Root has only left child or only right child — handle each case
- Binary tree vs N-ary tree — for N-ary, iterate all children instead of just left/right

## 9. Similar Questions

| Question                                   | Where Pattern Applies                          | LeetCode # |
|--------------------------------------------|------------------------------------------------|------------|
| Zigzag Level Order Traversal               | Same BFS but alternate left/right per level    | LC 103     |
| Binary Tree Right Side View                | BFS — take last element of each level          | LC 199     |
| Average of Levels in Binary Tree           | BFS — average each level                       | LC 637     |
| Find Largest Value in Each Tree Row        | BFS — max of each level                        | LC 515     |
| Maximum Width of Binary Tree               | BFS with indices to compute width              | LC 662     |
| Cousins in Binary Tree                     | BFS to find nodes at same depth, different parent | LC 993  |
| N-ary Tree Level Order                     | Same BFS with multiple children                | LC 429     |
| Minimum Depth of Binary Tree               | BFS — first level with a leaf                  | LC 111     |

## 10. The ONE Trick to Remember

**"Snapshot queue size before the loop — that's the level boundary."**

The key to separating BFS into levels is recording `level_size = len(queue)` before entering the inner loop. This freezes the count of nodes at the current level (even though the queue grows as you add children). Process exactly `level_size` nodes, collect their values, append children. When the inner loop finishes, one complete level is done.
