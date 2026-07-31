# Day 7 — BST & Heap · Q5 · Two Sum in a BST

---

## What is this question actually asking?

Given a BST and a target `k`, is there a **pair of nodes** whose values sum to `k`?

```
BST inorder 2 3 4 5 6 7, k = 9 → True (2+7, 3+6, 4+5)
k = 28 → False
```

> The BST twist on Two Sum. Two clean routes: a **hash set during traversal** (simple), or a **two-pointer BST iterator** (O(1)-ish extra using the sorted order).

---

## Pattern

```
PATTERN: Hash set during DFS  —  OR  —  Two BST iterators (inorder + reverse-inorder)
```

Whenever you see: *"pair summing to k in a BST"* → either traverse and keep a `seen` set (like array Two Sum), or exploit sorted order with two pointers walking inward from the smallest and largest values.

---

## Understand with a diagram

```
Approach A (set): DFS each node; if (k - node.val) in seen → True; else add node.val.

Approach B (two-pointer on sorted order):
   left iterator  = inorder (ascending, smallest first)
   right iterator = reverse-inorder (descending, largest first)
   sum < k → advance left (bigger)
   sum > k → advance right (smaller)
   sum == k → True
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — check all pairs — O(n²)
Nested loops over all node pairs.

### 🟢 Approach A — hash set — O(n) time, O(n) space
One traversal, complement lookup. Works on any tree, ignores BST order.

### 🟢 Approach B — two BST iterators — O(n) time, O(h) space
Uses sorted order; two-pointer inward. More space-efficient than the set for a balanced tree.

---

## Pseudocode (Approach A — set)

```
function two_sum_bst(root, k):
    seen = set()
    def dfs(node):
        if not node: return False
        if (k - node.val) in seen: return True
        seen.add(node.val)
        return dfs(node.left) or dfs(node.right)
    return dfs(root)
```

---

## Python Code

```python
class TreeNode:
    def __init__(self, val, left=None, right=None):
        self.val = val; self.left = left; self.right = right

def find_target(root, k: int) -> bool:
    seen = set()
    def dfs(node):
        if not node:
            return False
        if (k - node.val) in seen:      # complement already seen
            return True
        seen.add(node.val)
        return dfs(node.left) or dfs(node.right)
    return dfs(root)


# Two-pointer version using inorder list (sorted)
def find_target_two_ptr(root, k: int) -> bool:
    vals = []
    def inorder(n):
        if n: inorder(n.left); vals.append(n.val); inorder(n.right)
    inorder(root)                        # sorted ascending
    l, r = 0, len(vals) - 1
    while l < r:
        s = vals[l] + vals[r]
        if s == k: return True
        elif s < k: l += 1
        else: r -= 1
    return False


# Test  (inorder 2 3 4 5 6 7)
n = {v: TreeNode(v) for v in [5,3,6,2,4,7]}
n[5].left, n[5].right = n[3], n[6]
n[3].left, n[3].right = n[2], n[4]
n[6].right = n[7]
print(find_target(n[5], 9))            # True
print(find_target(n[5], 28))           # False
print(find_target_two_ptr(n[5], 9))    # True
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(1) |
| Hash set | O(n) | O(n) |
| Two-pointer (BST iterators) | O(n) | O(h) |

---

## Edge Cases to remember

- **Distinct nodes** — a value can't pair with itself; the set/two-pointer both ensure two different nodes.
- **Set approach ignores BST order** — works on any binary tree; only the two-pointer route uses sortedness.
- **True O(h) space** needs BST-iterator objects (inorder + reverse), not a full inorder list.
- **Duplicates** — clarify whether equal values may pair; adjust the set logic if so.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Two Sum (Day 12 Q1) | Hash set complement |
| 2 | Two Sum IV — Input is a BST (LC 653) | This problem |
| 3 | Two Sum II sorted (LC 167) | Two pointer |
| 4 | 3Sum in a BST | Fix one + two-sum |
| 5 | Pair with target diff in BST | Two pointer |
| 6 | BST Iterator (LC 173) | Ordered streaming |
| 7 | Kth smallest in BST (LC 230) | Inorder |
| 8 | Closest pair to target | Two pointer |

---

## The ONE trick to remember

```
"HASH SET COMPLEMENT (any tree)  OR  TWO POINTERS ON THE SORTED INORDER."
```

Simplest: traverse and keep a `seen` set, checking for `k − node.val` (exactly array Two Sum). To use the BST's order, two-pointer inward on the ascending inorder sequence.

> Memory hook: "Two Sum, but the numbers live in a tree — set it, or squeeze the sorted order."

---
