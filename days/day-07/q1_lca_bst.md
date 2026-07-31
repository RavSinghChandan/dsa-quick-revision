# Day 7 — BST & Heap · Q1 · Lowest Common Ancestor in a BST

---

## What is this question actually asking?

Find the LCA of two nodes `p` and `q` in a **Binary Search Tree** — using the BST ordering to do it faster than the general tree version.

```
        6
       / \
      2   8
     / \  / \
    0  4 7  9
LCA(2,8)=6 ; LCA(2,4)=2 ; LCA(0,4)=2
```

> In a BST you don't need to search both subtrees — the values tell you which way to go. O(h) time, O(1) space.

---

## Pattern

```
PATTERN: BST value comparison — walk toward the split point
```

Whenever you see: *"LCA in a BST"* → from the root, if both `p` and `q` are smaller, go left; if both larger, go right; the first node where they diverge (one ≤ node ≤ other) is the LCA.

---

## Understand with a diagram

```
cur = root
LCA(2,8) at 6: 2 < 6 and 8 > 6 → split here → 6 is LCA
LCA(0,4) at 6: both < 6 → go left to 2
            at 2: 0 < 2 and 4 > 2 → split → 2 is LCA
```

The LCA is the unique node whose value sits *between* p and q.

---

## Brute Force → Better → Optimal

### 🔴 General-tree DFS — O(n)
Ignore BST ordering, use the post-order method. Works but slower.

### 🟢 Optimal — BST walk — O(h)
Compare values and descend once per level; no backtracking. O(h) — O(log n) for balanced.

---

## Pseudocode (Optimal)

```
function lca_bst(root, p, q):
    cur = root
    while cur:
        if p.val < cur.val and q.val < cur.val:
            cur = cur.left          ← both smaller → go left
        elif p.val > cur.val and q.val > cur.val:
            cur = cur.right         ← both larger → go right
        else:
            return cur              ← split point = LCA
```

---

## Python Code

```python
class TreeNode:
    def __init__(self, val, left=None, right=None):
        self.val = val; self.left = left; self.right = right

def lca_bst(root, p, q):
    cur = root
    while cur:
        if p.val < cur.val and q.val < cur.val:
            cur = cur.left
        elif p.val > cur.val and q.val > cur.val:
            cur = cur.right
        else:
            return cur          # they diverge here (or one equals cur)


# Build BST and test
n = {v: TreeNode(v) for v in [6,2,8,0,4,7,9]}
n[6].left, n[6].right = n[2], n[8]
n[2].left, n[2].right = n[0], n[4]
n[8].left, n[8].right = n[7], n[9]
print(lca_bst(n[6], n[2], n[8]).val)   # 6
print(lca_bst(n[6], n[0], n[4]).val)   # 2
```

---

## Complexity

| | Time | Space |
|---|---|---|
| BST walk | O(h) | O(1) |

---

## Edge Cases to remember

- **Use `<` and `>`, handle equality as split** — if either p or q equals cur, cur is an ancestor of the other → return cur.
- **O(1) space iteratively** — no recursion needed.
- **Assume both nodes exist** in the BST.
- **h = O(n) for a skewed BST**; O(log n) balanced.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | LCA in Binary Tree (Day 6 Q1) | General DFS |
| 2 | Search in a BST (LC 700) | Value-guided walk |
| 3 | Insert into a BST (LC 701) | Walk to leaf |
| 4 | Validate BST (LC 98) | Range check |
| 5 | Ceil/Floor in BST | Guided walk |
| 6 | Closest value in BST (LC 270) | Track closest while walking |
| 7 | Range sum of BST (LC 938) | Prune by value |
| 8 | Two Sum in BST (Q5) | BST + set |

---

## The ONE trick to remember

```
"BOTH SMALLER → LEFT; BOTH LARGER → RIGHT; SPLIT → LCA."
```

The BST order tells you the direction without exploring both sides. The LCA is the first node whose value lies between p and q (inclusive). O(h), no recursion.

> Memory hook: "Walk down until the two targets sit on opposite sides of you."

---
