# Q6 — Zigzag Level Order Traversal (LeetCode 103)

## 1. What is this question actually asking?

This is just like level order traversal, but you alternate the direction of each level. Level 0 goes left-to-right, level 1 goes right-to-left, level 2 goes left-to-right again, and so on. The zigzag pattern gives rows that alternate direction.

## 2. Pattern

BFS with direction flag — same as level order but reverse alternate levels

## 3. Understand with a Diagram

```
Tree:
        3
       / \
      9   20
         /  \
        15   7

Level 0 (left→right): [3]
Level 1 (right→left): [20, 9]   ← reversed
Level 2 (left→right): [15, 7]   ← back to normal

Output: [[3], [20, 9], [15, 7]]

BFS collects each level normally (left to right).
Then we reverse the even-indexed levels (level 1, 3, 5...).

Queue trace:
start: [3], direction=left_to_right (L2R)

Level 0: process [3] → level=[3], add children [9,20]
  L2R: append as-is → [3]
  flip direction

Level 1: queue=[9,20], size=2
  process 9: add no children
  process 20: add [15,7]
  level=[9,20]
  R2L: reverse → [20,9]
  flip direction

Level 2: queue=[15,7], size=2
  level=[15,7]
  L2R: as-is → [15,7]

Result: [[3],[20,9],[15,7]] ✓

Alternative: use deque for level collection
  L2R: appendleft each element → deque stores in reverse order
  Or: collect normally, reverse at the end if R2L
```

## 4. Brute Force → Better → Optimal

### Brute Force — BFS then reverse alternate levels
**Idea:** Do standard BFS to collect all levels, then reverse every other level.

```
levels = levelOrder(root)  // standard BFS
for i in range(1, len(levels), 2):
    levels[i] = levels[i][::-1]
return levels
```

Valid and works, O(n) time, O(n) space.

### Optimal — BFS with direction flag
**Idea:** Same BFS as level order. Keep a boolean flag `left_to_right`. After collecting each level, if flag is False, reverse the level list. Toggle the flag.

```
queue = [root]
left_to_right = True
result = []

while queue:
    level_size = len(queue)
    level = []
    
    for _ in range(level_size):
        node = queue.popleft()
        level.append(node.val)
        if node.left: queue.append(node.left)
        if node.right: queue.append(node.right)
    
    if not left_to_right:
        level.reverse()
    
    result.append(level)
    left_to_right = not left_to_right

return result
```

O(n) time, O(n) space.

**Trace:**
```
root=3, queue=[3], L2R=True

Level 0: size=1, level=[3], L2R→append [3], toggle→R2L
Level 1: size=2, level=[9,20], R2L→reverse→[20,9], toggle→L2R
Level 2: size=2, level=[15,7], L2R→append [15,7]

Result: [[3],[20,9],[15,7]]
```

## 5. Pseudocode (Optimal)

```
function zigzagLevelOrder(root):
    if root is None: return []
    
    queue = deque([root])
    result = []
    left_to_right = True
    
    while queue:
        level_size = len(queue)
        level = []
        
        for i in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            if node.left:  queue.append(node.left)
            if node.right: queue.append(node.right)
        
        if not left_to_right:
            level.reverse()
        
        result.append(level)
        left_to_right = not left_to_right
    
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


def zigzagLevelOrder(root):
    if not root:
        return []
    
    queue = deque([root])
    result = []
    left_to_right = True
    
    while queue:
        level_size = len(queue)
        level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            if node.left:  queue.append(node.left)
            if node.right: queue.append(node.right)
        
        if not left_to_right:
            level.reverse()
        
        result.append(level)
        left_to_right = not left_to_right
    
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
print(zigzagLevelOrder(root))   # [[3], [20, 9], [15, 7]]

# Test 2 — single node
print(zigzagLevelOrder(TreeNode(1)))   # [[1]]

# Test 3 — empty
print(zigzagLevelOrder(None))   # []

# Test 4 — four levels
root = build([1, 2, 3, 4, 5, 6, 7])
print(zigzagLevelOrder(root))   # [[1], [3,2], [4,5,6,7]]
```

## 7. Complexity Table

| Approach              | Time  | Space | Notes                             |
|-----------------------|-------|-------|-----------------------------------|
| BFS then reverse odds | O(n)  | O(n)  | Two passes                        |
| BFS with flag         | O(n)  | O(n)  | Single pass with conditional reverse|

## 8. Edge Cases to Remember

- Empty tree — return []
- Single node — [[root.val]], direction doesn't matter
- All nodes on one side — still zigzags by level correctly
- Even depth vs odd depth — flag toggles correctly
- Level 0 is always L2R (the root)
- Using deque with appendleft as alternative to reversing — valid but less readable

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Level Order Traversal                 | Same without the direction flag                     | LC 102     |
| Binary Tree Right Side View           | BFS — last element of each level                    | LC 199     |
| Average of Levels                     | BFS — average each level                            | LC 637     |
| Reverse Level Order Traversal         | BFS then reverse all levels                         | GFG        |
| Spiral Matrix                         | Same zigzag idea on a 2D matrix                     | LC 54      |
| Maximum Depth via BFS                 | Count levels in BFS                                 | LC 104     |
| Find Largest Value in Each Row        | BFS — max per level                                 | LC 515     |
| N-ary Tree Zigzag Level Order         | Same pattern with multiple children                 | —          |

## 10. The ONE Trick to Remember

**"BFS as usual — just reverse every other level before appending to result."**

The zigzag pattern is just level order traversal with a toggle. Collect each level normally via BFS. Before appending to result, check the flag — if it says "right to left," reverse the collected level list. Then flip the flag. The BFS queue is never modified; only the final level list is conditionally reversed. Two lines added to the standard BFS = zigzag done.
