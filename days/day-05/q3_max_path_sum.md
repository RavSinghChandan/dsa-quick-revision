# Q3 — Maximum Path Sum in Binary Tree (LeetCode 124)

## 1. What is this question actually asking?

A path in a binary tree is any sequence of nodes where each consecutive pair is connected by an edge, and no node appears more than once. The path sum is the total of all node values along the path. Find the path with the maximum sum. The path doesn't need to go through the root or start/end at a leaf. Node values can be negative.

## 2. Pattern

DFS Post-order — at each node, the path through it = node.val + max_gain(left) + max_gain(right); update global max; return node.val + max(0, max_gain(left), max_gain(right)) to parent

## 3. Understand with a Diagram

```
Tree:
       -10
       /  \
      9    20
          /  \
         15   7

For each node, "gain" = what you contribute going through you toward root:
A path can split at a node (go left and right) but then it can't continue to the parent.
So at each node, two things:
  1) Update answer: val + gain(left) + gain(right) — full path through this node
  2) Return to parent: val + max(gain(left), gain(right)) — one direction only

gain(9):  9 + max(0,0) = 9 (no children)
           answer candidate = 9 + 0 + 0 = 9

gain(15): 15, answer candidate = 15
gain(7):  7, answer candidate = 7

gain(20): left=15, right=7
  answer candidate through 20: 20 + 15 + 7 = 42 ← NEW MAX
  return to parent: 20 + max(15, 7) = 35

gain(root=-10):
  left gain = 9, right gain = 35
  answer candidate: -10 + 9 + 35 = 34 < 42
  return: -10 + max(9, 35) = 25

Answer: 42

Negative value handling:
If gain from a subtree is negative, don't include it (use max(0, gain)).
This means we might skip an entire subtree if it hurts more than helps.

Another example:
  1
 / \
2   3
Answer: 6 (2+1+3, path through root)
```

## 4. Brute Force → Better → Optimal

### Brute Force — All paths between all pairs
**Idea:** For every pair of nodes (i, j), find the path between them and compute its sum. This requires finding the LCA and summing nodes on the path.

Why slow: O(n²) pairs, O(n) per path sum → O(n³). Not practical.

### Optimal — DFS with gain function
**Idea:** Recursive DFS. For each node, compute max gain it can contribute going upward (one direction only). At each node, also consider the full "bent" path that splits left and right. Track global maximum throughout.

```
max_sum = -infinity

function gain(node):
    if node is None: return 0
    left_gain  = max(0, gain(node.left))   // ignore negative contributions
    right_gain = max(0, gain(node.right))
    
    // Price of path through this node (could split L and R)
    price = node.val + left_gain + right_gain
    max_sum = max(max_sum, price)
    
    // Return max single-direction gain to parent (can't split)
    return node.val + max(left_gain, right_gain)

gain(root)
return max_sum
```

O(n) time, O(h) space.

**Full trace on [-10, 9, 20, None, None, 15, 7]:**
```
gain(9):   left=max(0,0)=0, right=0, price=9+0+0=9→max=9, return 9
gain(15):  price=15→max=15, return 15
gain(7):   price=7→max=15, return 7
gain(20):  left=max(0,15)=15, right=max(0,7)=7
           price=20+15+7=42→max=42, return 20+15=35
gain(-10): left=max(0,9)=9, right=max(0,35)=35
           price=-10+9+35=34<42, return -10+35=25
Answer: 42
```

## 5. Pseudocode (Optimal)

```
function maxPathSum(root):
    max_sum = [-infinity]
    
    function gain(node):
        if node is None: return 0
        left  = max(0, gain(node.left))
        right = max(0, gain(node.right))
        max_sum[0] = max(max_sum[0], node.val + left + right)
        return node.val + max(left, right)
    
    gain(root)
    return max_sum[0]
```

## 6. Python Code

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def maxPathSum(root):
    max_sum = [float('-inf')]
    
    def gain(node):
        if not node:
            return 0
        left  = max(0, gain(node.left))
        right = max(0, gain(node.right))
        max_sum[0] = max(max_sum[0], node.val + left + right)
        return node.val + max(left, right)
    
    gain(root)
    return max_sum[0]


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
root = build([1, 2, 3])
print(maxPathSum(root))   # 6

# Test 2 — LeetCode example 2
root = build([-10, 9, 20, None, None, 15, 7])
print(maxPathSum(root))   # 42

# Test 3 — all negative (must pick at least one node)
root = build([-3])
print(maxPathSum(root))   # -3

# Test 4 — single positive
print(maxPathSum(TreeNode(5)))   # 5

# Test 5 — negative root, positive children
root = build([-1, 2, 3])
print(maxPathSum(root))   # 4 (path is just 2→root→3? No: 2+(-1)+3=4. Yes!)
```

## 7. Complexity Table

| Approach          | Time  | Space | Notes                                |
|-------------------|-------|-------|--------------------------------------|
| All pairs         | O(n³) | O(n)  | Find all paths                       |
| DFS Gain          | O(n)  | O(h)  | Single pass — optimal                |

## 8. Edge Cases to Remember

- All negative values — can't ignore all nodes; must pick at least one. max_sum starts at -inf, and every node updates it, so we always pick at least the best single node.
- Root only — gain returns root.val, max_sum = root.val
- Negative children — max(0, gain(child)) ensures we don't include negative paths
- Path through root isn't optimal — global max catches maximum in subtrees
- Very deep unbalanced tree — O(h) stack space could be O(n)

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Diameter of Binary Tree               | Same DFS structure, length instead of sum           | LC 543     |
| Path Sum                              | Does any root-to-leaf path sum to target?           | LC 112     |
| Path Sum II                           | All root-to-leaf paths summing to target            | LC 113     |
| Binary Tree Maximum Path Sum (variant)| Same as this problem                                | LC 124     |
| Sum Root to Leaf Numbers              | Build number top-down, sum at leaves                | LC 129     |
| Longest Univalue Path                 | Same pattern but tracking consecutive same values   | LC 687     |
| Binary Tree Cameras                   | DFS returning state (covered/not/has camera)        | LC 968     |
| House Robber III                      | DFS returning (rob_this, skip_this) tuple           | LC 337     |

## 10. The ONE Trick to Remember

**"Gain = max(0, left) + node + max(0, right) updates answer; but only max single direction returns upward."**

At each node you can "bend" the path left-and-right to maximize the sum through that node — but if you bend, you can't continue to the parent. So you update the global answer with left+node+right (the bent path), but return node+max(left,right) to the parent (one direction only). Using max(0, gain) prevents negative subtrees from reducing your path sum.
