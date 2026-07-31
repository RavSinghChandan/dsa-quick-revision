# Day 6 — Binary Tree · Q3 · Construct Binary Tree from Preorder & Inorder

---

## What is this question actually asking?

Rebuild the unique binary tree given its **preorder** and **inorder** traversals.

```
preorder = [3,9,20,15,7]
inorder  = [9,3,15,20,7]
→        3
        / \
       9  20
          / \
         15  7
```

> A canonical recursion. Preorder gives you the **root**; inorder tells you which nodes fall **left vs right** of that root.

---

## Pattern

```
PATTERN: Recursion — preorder gives root, inorder splits left/right subtrees
```

Whenever you see: *"build a tree from two traversals"* → the first preorder element is the root; find it in inorder; everything left of it is the left subtree, everything right is the right subtree. Recurse. Use an index map for O(1) root lookup.

---

## Understand with a diagram

```
preorder[0] = 3 → root
inorder: [9 | 3 | 15,20,7]
          ^left    ^right (of root 3)

left subtree  ← preorder next 1 element, inorder [9]
right subtree ← preorder rest, inorder [15,20,7]

recurse. A pre_idx pointer walks preorder as we build (root, then left, then right).
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — search inorder each time — O(n²)
Linear scan to find the root in inorder at every recursion.

### 🟢 Optimal — hash map of inorder indices — O(n)
Precompute `value → index in inorder` for O(1) root location. Walk preorder with a moving pointer; recurse building left then right.

---

## Pseudocode (Optimal)

```
function build(preorder, inorder):
    idx = {val: i for i, val in enumerate(inorder)}
    self.pre = 0
    return helper(0, len(inorder)-1)

function helper(in_lo, in_hi):
    if in_lo > in_hi: return None
    root_val = preorder[self.pre]; self.pre += 1
    root = Node(root_val)
    mid = idx[root_val]
    root.left  = helper(in_lo, mid-1)      ← build LEFT first (preorder order)
    root.right = helper(mid+1, in_hi)
    return root
```

---

## Python Code

```python
class TreeNode:
    def __init__(self, val, left=None, right=None):
        self.val = val; self.left = left; self.right = right

def build_tree(preorder: list[int], inorder: list[int]) -> TreeNode:
    idx = {v: i for i, v in enumerate(inorder)}   # O(1) root lookup
    pre = [0]                                     # mutable pointer into preorder

    def helper(lo: int, hi: int):
        if lo > hi:
            return None
        root_val = preorder[pre[0]]
        pre[0] += 1
        root = TreeNode(root_val)
        mid = idx[root_val]
        root.left = helper(lo, mid - 1)           # LEFT before RIGHT
        root.right = helper(mid + 1, hi)
        return root

    return helper(0, len(inorder) - 1)


# Test (verify by inorder traversal)
root = build_tree([3,9,20,15,7], [9,3,15,20,7])
def inorder(n, out):
    if n: inorder(n.left, out); out.append(n.val); inorder(n.right, out)
out = []; inorder(root, out)
print(out)          # [9, 3, 15, 20, 7]
print(root.val)     # 3
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(n) |
| Hash map | O(n) | O(n) |

---

## Edge Cases to remember

- **Build LEFT before RIGHT** — preorder is root→left→right, so the moving preorder pointer must consume the left subtree first.
- **Hash map for inorder indices** — turns the O(n) search into O(1); without it it's O(n²).
- **Postorder+Inorder variant** → root is the *last* postorder element; walk postorder backward, build RIGHT before LEFT.
- **Duplicate values** break the inorder-index assumption (assume unique).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Build from Inorder + Postorder (LC 106) | Root = last postorder |
| 2 | Build from Preorder + Postorder (LC 889) | Ambiguous, one valid tree |
| 3 | Serialize/Deserialize tree (LC 297) | Preorder rebuild |
| 4 | Construct BST from preorder (LC 1008) | Bounds recursion |
| 5 | Verify preorder serialization (LC 331) | Slot counting |
| 6 | Recover a tree from traversal | Depth markers |
| 7 | Flatten/rebuild subtree | Recursion |
| 8 | Clone a tree | DFS |

---

## The ONE trick to remember

```
"PREORDER[0] = ROOT; ITS INORDER INDEX SPLITS LEFT | RIGHT. RECURSE (map for O(1))."
```

Take the root from the front of preorder, locate it in inorder (via a precomputed index map), and everything to its left/right in inorder forms the two subtrees. Build left first as the preorder pointer advances.

> Memory hook: "Preorder names the boss; inorder shows who's on the boss's left and right."

---
