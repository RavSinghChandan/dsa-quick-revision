# Day 7 — BST & Heap · Q3 · Correct a BST with Two Nodes Swapped

---

## What is this question actually asking?

Exactly **two nodes** of a BST were swapped by mistake. Restore the BST by fixing them — **without changing the tree's structure** (swap values back).

```
Inorder should be sorted. Two swaps break it:
  1 3 2 4   → the 3 and 2 are swapped → fix to 1 2 3 4
```

> Uses the key BST fact: **inorder traversal must be strictly increasing.** Find the two "out of order" points and swap them back.

---

## Pattern

```
PATTERN: Inorder traversal — detect the two dips, swap them
```

Whenever you see: *"recover / two nodes swapped in a BST"* → do an inorder walk tracking the previous node. A violation (`prev.val > cur.val`) marks a swapped node. There are **one or two** violations; identify `first` and `second`, then swap their values.

---

## Understand with a diagram

```
inorder of a valid BST is sorted. With two swaps you see dips:

Adjacent swap (1 violation):  1 [3 2] 4
   first = 3 (the bigger one at the dip), second = 2 (the smaller)

Non-adjacent swap (2 violations): 1 [6] 3 4 [2] 5   (6 and 2 swapped)
   first  = node at the FIRST dip's larger side (6)
   second = node at the LAST dip's smaller side (2)

Swap first.val and second.val → BST restored.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — inorder to array, sort, compare — O(n) time, O(n) space
Collect values, find the two differing positions, swap. Works, O(n) space.

### 🟢 Optimal — inorder with prev pointer — O(n) time, O(h) space (O(1) with Morris)
Detect violations during a single inorder pass; track `first`, `middle`, `last`. Swap `first` and `last` (or `first`/`middle` if adjacent).

---

## Pseudocode (Optimal)

```
function recover(root):
    first = second = prev = None
    inorder(root):
        for each node in inorder:
            if prev and prev.val > node.val:      ← violation
                if first is None: first = prev    ← first dip: take the bigger
                second = node                     ← always the smaller at latest dip
            prev = node
    swap first.val and second.val
```

---

## Python Code

```python
class TreeNode:
    def __init__(self, val, left=None, right=None):
        self.val = val; self.left = left; self.right = right

def recover_bst(root) -> None:
    first = second = prev = None

    def inorder(node):
        nonlocal first, second, prev
        if not node:
            return
        inorder(node.left)
        if prev and prev.val > node.val:      # order violation
            if first is None:
                first = prev                  # bigger node of the first dip
            second = node                     # smaller node of the latest dip
        prev = node
        inorder(node.right)

    inorder(root)
    if first and second:
        first.val, second.val = second.val, first.val   # swap back


# Test:  build BST then swap two node values, recover
n = {i: TreeNode(i) for i in [1,2,3,4]}
n[3].left = n[1]; n[3].right = n[4]; n[1].right = n[2]  # inorder 1 2 3 4
n[3].val, n[2].val = n[2].val, n[3].val                 # swap → broken
recover_bst(n[3])
out = []
def ino(x):
    if x: ino(x.left); out.append(x.val); ino(x.right)
ino(n[3])
print(out)   # [1, 2, 3, 4]  (restored)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Inorder + prev | O(n) | O(h) (O(1) with Morris) |

---

## Edge Cases to remember

- **One vs two violations** — adjacent swaps give ONE dip (`second = node` at that dip); non-adjacent give TWO (`first` at the first dip, `second` at the last). The `if first is None` logic handles both.
- **Take `prev` as `first`** — the larger element of the first violation is the one to move.
- **Swap VALUES, not nodes** — structure must stay the same.
- **Morris inorder** → O(1) space version (Day 6 Q5).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Recover Binary Search Tree (LC 99) | This exact problem |
| 2 | Validate BST (LC 98) | Inorder monotonic |
| 3 | Kth smallest in BST (LC 230) | Inorder |
| 4 | Two sum in BST (Q5) | Ordered traversal |
| 5 | Morris inorder (Day 6 Q5) | O(1) traversal |
| 6 | Find mode in BST | Inorder counting |
| 7 | Minimum absolute diff in BST (LC 530) | Inorder adjacent |
| 8 | Convert BST to sorted list | Inorder |

---

## The ONE trick to remember

```
"INORDER MUST BE SORTED; FIND THE DIP(S): first=bigger at 1st, second=smaller at last. SWAP."
```

Walk inorder tracking `prev`. A `prev.val > cur.val` violation flags a swapped pair. With one violation the two are adjacent; with two, take the first dip's larger node and the second dip's smaller node. Swap their values.

> Memory hook: "The sorted line has one or two bumps — flatten them by swapping the culprits."

---
