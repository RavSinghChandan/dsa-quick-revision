# Q1 — Validate BST (LeetCode 98)

## 1. What is this question actually asking?

Given a binary tree, determine if it is a valid Binary Search Tree (BST). A valid BST requires: every node in the left subtree has a value strictly less than the current node, every node in the right subtree has a value strictly greater than the current node, and both left and right subtrees must also be valid BSTs. The common trap: checking just the immediate children isn't enough — you need to ensure the entire subtree satisfies the constraint.

## 2. Pattern

DFS with valid range — pass (min_val, max_val) bounds down the tree; every node must satisfy min_val < node.val < max_val

## 3. Understand with a Diagram

```
Valid BST:
      5
     / \
    1   4         ← INVALID! 4 < 5 but 4 is in right subtree
       / \
      3   6

Common trap: just checking parent vs children says 4 > nothing (root's right must be > 5).
We need range validation.

Range validation:
Each node gets a range (min, max) it must fall within.
           5   (range: -inf, +inf)
          / \
         1   4   ← node 4's range should be (5, +inf) but 4 < 5 → INVALID
        
Valid BST example:
       5   range=(-inf, +inf)  → 5 is OK
      / \
     3   7
    / \ / \
   1  4 6  9

Node 3: range=(-inf, 5)   → 3 OK
Node 1: range=(-inf, 3)   → 1 OK
Node 4: range=(3, 5)      → 4 OK
Node 7: range=(5, +inf)   → 7 OK
Node 6: range=(5, 7)      → 6 OK
Node 9: range=(7, +inf)   → 9 OK
→ Valid BST ✓

Range propagation rule:
  Going LEFT: range becomes (min, node.val)
  Going RIGHT: range becomes (node.val, max)
```

## 4. Brute Force → Better → Optimal

### Brute Force — For each node, check all descendants
**Idea:** For each node, verify that every node in its left subtree is less and every node in its right subtree is greater.

```
for each node in tree:
    if any node in left_subtree(node) >= node.val: return False
    if any node in right_subtree(node) <= node.val: return False
return True
```

Why slow: O(n²) — for each of n nodes, scan subtree.

### Better — In-order traversal should be sorted
**Idea:** In a valid BST, in-order traversal (left, root, right) produces values in strictly increasing order.

```
result = inorder(root)
for i from 1 to len(result)-1:
    if result[i] <= result[i-1]: return False
return True
```

O(n) time, O(n) space. Valid approach.

**In-order as iterator (O(1) space):**
```
prev = -infinity
def inorder(node):
    if not node: return True
    if not inorder(node.left): return False
    if node.val <= prev: return False
    prev = node.val
    return inorder(node.right)
```

### Optimal — DFS with Range (cleaner)
**Idea:** Pass bounds (lo, hi) representing the valid range for each node. Initially (-inf, +inf). Going left tightens the max; going right tightens the min.

```
function isValid(node, lo, hi):
    if node is None: return True
    if node.val <= lo or node.val >= hi: return False
    return isValid(node.left, lo, node.val) and isValid(node.right, node.val, hi)

return isValid(root, -inf, +inf)
```

O(n) time, O(h) space.

**Trace on [5,1,4,None,None,3,6]:**
```
isValid(5, -inf, +inf): 5 OK
  isValid(1, -inf, 5):   1 OK
    isValid(None,...): True
    isValid(None,...): True
  isValid(4, 5, +inf):   4 < 5 → False → overall False ✓
```

## 5. Pseudocode (Optimal)

```
function isValidBST(root):
    return validate(root, -infinity, +infinity)

function validate(node, lo, hi):
    if node is None: return True
    if node.val <= lo or node.val >= hi: return False
    return validate(node.left, lo, node.val) and
           validate(node.right, node.val, hi)
```

## 6. Python Code

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def isValidBST(root):
    def validate(node, lo, hi):
        if not node:
            return True
        if node.val <= lo or node.val >= hi:
            return False
        return validate(node.left, lo, node.val) and \
               validate(node.right, node.val, hi)
    
    return validate(root, float('-inf'), float('inf'))


# Test 1 — valid BST
root = TreeNode(2)
root.left  = TreeNode(1)
root.right = TreeNode(3)
print(isValidBST(root))   # True

# Test 2 — invalid: right child smaller than root
root = TreeNode(5)
root.left  = TreeNode(1)
root.right = TreeNode(4)
root.right.left  = TreeNode(3)
root.right.right = TreeNode(6)
print(isValidBST(root))   # False

# Test 3 — single node
print(isValidBST(TreeNode(5)))   # True

# Test 4 — duplicate values (not strictly greater)
root = TreeNode(2)
root.left  = TreeNode(2)
print(isValidBST(root))   # False (equal not allowed in strict BST)

# Test 5 — tricky: violates ancestor constraint
root = TreeNode(10)
root.left = TreeNode(5)
root.left.right = TreeNode(15)  # 15 > 10 but is in left subtree → invalid
print(isValidBST(root))   # False
```

## 7. Complexity Table

| Approach          | Time  | Space | Notes                              |
|-------------------|-------|-------|------------------------------------|
| Brute Force       | O(n²) | O(n)  | Check all descendants per node     |
| In-order sorted   | O(n)  | O(n)  | Store or check in-order            |
| Range DFS         | O(n)  | O(h)  | Most elegant — optimal             |

## 8. Edge Cases to Remember

- Duplicate values — BST requires STRICT inequalities; equal values at wrong position → invalid
- Left child equal to parent — invalid (strict less than required)
- Violates ancestor constraint (not just parent) — range check catches this
- INT_MIN or INT_MAX values in tree — use float('-inf') and float('inf') in Python (safe)
- Single node — always valid
- Empty tree — return True (vacuously valid)

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Kth Smallest in BST                   | In-order traversal gives sorted order               | LC 230     |
| LCA of BST                            | Use BST property: go left or right based on values  | LC 235     |
| Insert into BST                       | Find correct position using BST property            | LC 701     |
| Delete Node in BST                    | Find, handle 3 cases, relink                        | LC 450     |
| Search in BST                         | O(h) search using BST property                      | LC 700     |
| Convert Sorted Array to BST           | Recursive mid as root                               | LC 108     |
| Recover BST                           | Find two swapped nodes via in-order                 | LC 99      |
| Count Nodes in Complete Binary Tree   | Use tree structure for efficient count              | LC 222     |

## 10. The ONE Trick to Remember

**"Each node must satisfy min < val < max — going left tightens max, going right tightens min."**

The range (lo, hi) passed down represents the valid values for any node at that position. When you go left from a node with value v, the new max becomes v (nothing in the left subtree can be >= v). When you go right, the new min becomes v. If any node violates its range, return False immediately. Start with (-inf, +inf) at the root.
