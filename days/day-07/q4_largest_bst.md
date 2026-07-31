# Day 7 — BST & Heap · Q4 · Largest BST in a Binary Tree

---

## What is this question actually asking?

In an arbitrary binary tree, find the size (node count) of the **largest subtree that is itself a valid BST**.

```
      10
     /  \
    5    15
   / \     \
  1   8     7      Largest BST subtree = the (5,1,8) subtree → size 3
```

> Tests a **bottom-up post-order** where each node returns `(isBST, min, max, size)` so the parent can decide in O(1).

---

## Pattern

```
PATTERN: Post-order DFS returning (min, max, size, isBST) bundle
```

Whenever you see: *"largest valid-BST subtree / property that depends on both children's ranges"* → have DFS return a small tuple summarizing each subtree; the parent combines children's summaries to check the BST condition and compute its own.

---

## Understand with a diagram

```
For node with left summary L, right summary R:
  node is a BST if:
     L.isBST and R.isBST
     and L.max < node.val < R.min

If BST: return (min=L.min or node.val, max=R.max or node.val,
                size = L.size + R.size + 1, isBST=True)
Else:   return (isBST=False, size = max(L.size, R.size))   ← best BST seen below

Track the global max size.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — check each subtree for BST — O(n²)
For every node, validate its whole subtree as a BST and count. Repeated work.

### 🟢 Optimal — one post-order pass — O(n)
Each node computes its summary from its children in O(1). One traversal.

---

## Pseudocode (Optimal)

```
function largest_bst(root):
    best = 0
    dfs(node):
        if node is None: return (min=+inf, max=-inf, size=0, isBST=True)
        L = dfs(node.left); R = dfs(node.right)
        if L.isBST and R.isBST and L.max < node.val < R.min:
            size = L.size + R.size + 1
            best = max(best, size)
            return (min(L.min, node.val), max(R.max, node.val), size, True)
        else:
            return (min=-inf, max=+inf, size=max(L.size, R.size), isBST=False)
    dfs(root)
    return best
```

---

## Python Code

```python
class TreeNode:
    def __init__(self, val, left=None, right=None):
        self.val = val; self.left = left; self.right = right

def largest_bst(root) -> int:
    best = 0

    def dfs(node):
        nonlocal best
        if node is None:
            return (float('inf'), float('-inf'), 0, True)   # min, max, size, isBST
        lmin, lmax, lsize, lbst = dfs(node.left)
        rmin, rmax, rsize, rbst = dfs(node.right)
        if lbst and rbst and lmax < node.val < rmin:
            size = lsize + rsize + 1
            best = max(best, size)
            return (min(lmin, node.val), max(rmax, node.val), size, True)
        # not a BST here → return an "invalid range" but keep best size below
        return (float('-inf'), float('inf'), max(lsize, rsize), False)

    dfs(root)
    return best


# Test
n = {v: TreeNode(v) for v in [10,5,15,1,8,7]}
n[10].left, n[10].right = n[5], n[15]
n[5].left, n[5].right = n[1], n[8]
n[15].right = n[7]
print(largest_bst(n[10]))   # 3
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(h) |
| Post-order bundle | O(n) | O(h) |

---

## Edge Cases to remember

- **Null returns `(+inf, -inf, 0, True)`** — so a leaf's condition `lmax < val < rmin` becomes `-inf < val < +inf` → True.
- **Non-BST node returns invalid range** (`-inf, +inf`) so no ancestor mistakes it for a BST, but still propagates the best size found below.
- **Strict `<`** — BST here assumed to have no duplicate values.
- **Whole tree may be the BST** → answer = n.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Validate BST (LC 98) | Range check |
| 2 | Largest BST Subtree (LC 333) | This problem |
| 3 | Diameter of Binary Tree (LC 543) | Post-order bundle |
| 4 | Max path sum (LC 124) | Post-order return |
| 5 | Balanced binary tree (LC 110) | Height + flag |
| 6 | Count univalue subtrees | Post-order flag |
| 7 | House Robber III (LC 337) | Post-order states |
| 8 | Binary tree cameras (LC 968) | Post-order states |

---

## The ONE trick to remember

```
"POST-ORDER RETURN (min, max, size, isBST); PARENT CHECKS lmax < val < rmin."
```

Each node aggregates its children's summaries in O(1): it's a BST only if both children are BSTs and its value fits strictly between the left subtree's max and the right subtree's min. Track the largest BST size globally.

> Memory hook: "Each subtree hands its parent a report card: am I a BST, my range, my size."

---
