# Q2 — Kth Smallest Element in BST (LeetCode 230)

## 1. What is this question actually asking?

Given a BST and an integer k, find the kth smallest element in the tree. Since in-order traversal of a BST visits nodes in ascending sorted order, the kth node visited is the kth smallest. You need to return that value, not the node.

## 2. Pattern

BST In-order Traversal — in-order gives sorted order; count to k and return

## 3. Understand with a Diagram

```
BST:
        5
       / \
      3   6
     / \
    2   4
   /
  1

In-order traversal (left → root → right):
  1 → 2 → 3 → 4 → 5 → 6
  1st 2nd 3rd 4th 5th 6th

k=3 → answer is 3
k=1 → answer is 1
k=6 → answer is 6

In-order trace:
  visit(5)
    visit(3)
      visit(2)
        visit(1): no left, output 1 (count=1), no right
        return to 2: output 2 (count=2), no right
      return to 3: output 3 (count=3 == k) → FOUND 3!

Iterative in-order with early exit (more efficient):
Use a stack. Drill left until leaf. Pop and visit. Go right.

stack=[], curr=5
Push 5→3→2→1, pop 1: count=1, k=3? No. curr=1.right=None
Pop 2: count=2, k=3? No. curr=2.right=None
Pop 3: count=3, k=3? YES → return 3 ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — Collect all in sorted order
**Idea:** In-order traversal collects all values sorted. Then return values[k-1].

```
def inorder(node, vals):
    if not node: return
    inorder(node.left, vals)
    vals.append(node.val)
    inorder(node.right, vals)

vals = []
inorder(root, vals)
return vals[k-1]
```

O(n) time, O(n) space.

### Optimal — Early Exit In-order
**Idea:** In-order traversal but stop as soon as we've visited k nodes. No need to traverse the rest.

**Recursive with counter:**
```
count = [0]
result = [None]

def inorder(node):
    if not node or result[0] is not None: return
    inorder(node.left)
    count[0] += 1
    if count[0] == k:
        result[0] = node.val
        return
    inorder(node.right)

inorder(root)
return result[0]
```

**Iterative (better for large trees):**
```
stack = [], curr = root, count = 0

while stack or curr:
    while curr:
        stack.append(curr)
        curr = curr.left
    curr = stack.pop()
    count += 1
    if count == k: return curr.val
    curr = curr.right
```

Both O(k) time for early exit, O(h) space.

## 5. Pseudocode (Optimal)

```
function kthSmallest(root, k):
    stack = []
    curr = root
    count = 0
    
    while stack is not empty or curr is not None:
        while curr is not None:
            stack.push(curr)
            curr = curr.left
        
        curr = stack.pop()
        count += 1
        if count == k: return curr.val
        
        curr = curr.right
    
    return -1  // k > tree size (shouldn't happen per constraints)
```

## 6. Python Code

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def kthSmallest(root, k):
    stack = []
    curr = root
    count = 0
    
    while stack or curr:
        # Drill to leftmost
        while curr:
            stack.append(curr)
            curr = curr.left
        
        # Visit node
        curr = stack.pop()
        count += 1
        if count == k:
            return curr.val
        
        # Move to right subtree
        curr = curr.right
    
    return -1


# Build BST
def insert(root, val):
    if not root: return TreeNode(val)
    if val < root.val: root.left = insert(root.left, val)
    else: root.right = insert(root.right, val)
    return root


# Test 1 — LeetCode example 1
root = None
for v in [3, 1, 4, 2]:
    root = insert(root, v)
print(kthSmallest(root, 1))   # 1

# Test 2 — LeetCode example 2
root = None
for v in [5, 3, 6, 2, 4, 1]:
    root = insert(root, v)
print(kthSmallest(root, 3))   # 3

# Test 3 — single node
print(kthSmallest(TreeNode(1), 1))   # 1

# Test 4 — k = size of tree
root = None
for v in [5, 3, 7]:
    root = insert(root, v)
print(kthSmallest(root, 3))   # 7
```

## 7. Complexity Table

| Approach          | Time  | Space | Notes                              |
|-------------------|-------|-------|------------------------------------|
| Collect all       | O(n)  | O(n)  | Store all values in array          |
| Early exit DFS    | O(k+h)| O(h)  | Stop after visiting k nodes        |
| Iterative in-order| O(k+h)| O(h)  | Same complexity, no recursion      |

## 8. Edge Cases to Remember

- k = 1 — return minimum (leftmost leaf)
- k = n — return maximum (rightmost leaf)
- BST with single node — return that node's value
- Augmented BST (Follow-up) — if BST is frequently modified, augment each node with subtree size to find kth in O(h)
- Duplicate values — standard BST doesn't have duplicates; if asked about duplicates, clarify

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Validate BST                          | Same in-order property used                         | LC 98      |
| Kth Largest in BST                    | Reverse in-order (right, root, left)                | —          |
| Binary Tree Inorder Traversal         | Foundation: in-order without BST property           | LC 94      |
| Recover BST                           | In-order to find two swapped nodes                  | LC 99      |
| Inorder Successor in BST              | In-order traversal or BST property                  | LC 285     |
| Find Mode in BST                      | In-order, track consecutive runs                    | LC 501     |
| Convert BST to Greater Tree           | Reverse in-order, running sum                       | LC 538     |
| Balance a BST                         | In-order to get sorted array, build balanced BST    | LC 1382    |

## 10. The ONE Trick to Remember

**"In-order of BST = sorted order — count k nodes during traversal and return the kth one."**

In a BST, left subtree < current < right subtree. So traversing left-root-right gives ascending sorted order. Count nodes as you visit them. The kth node you visit is the answer. Use iterative in-order with a stack for clean early exit — drill left, pop, visit, go right.
