# Day 7 — BST & Heap · Q2 · Inorder Successor and Predecessor in a BST

---

## What is this question actually asking?

For a target value in a BST, find its **inorder successor** (next larger) and **predecessor** (next smaller) — i.e., neighbors in sorted order.

```
BST inorder: 2 5 8 10 12
target 8 → predecessor 5, successor 10
```

> Uses BST ordering to find neighbors in O(h) without a full traversal. Tests the "track candidate while walking" pattern.

---

## Pattern

```
PATTERN: BST guided walk — track last "just bigger" / "just smaller" candidate
```

Whenever you see: *"successor / predecessor / ceil / floor in a BST"* → walk down; each time you go left, the current node is a **successor candidate**; each time you go right, it's a **predecessor candidate**. Update as you descend.

---

## Understand with a diagram

```
Successor of target = smallest value > target:
  cur = root
  if cur.val > target: succ = cur; go left   ← cur could be the successor, look for smaller
  else: go right

Predecessor = largest value < target:
  if cur.val < target: pred = cur; go right   ← cur could be predecessor, look for bigger
  else: go left
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — full inorder into a list — O(n)
Traverse, find target's neighbors. Simple, O(n) space/time.

### 🟢 Optimal — two guided walks — O(h)
One walk finds the successor, one finds the predecessor, each tracking a candidate. O(h) time, O(1) space.

---

## Pseudocode (Optimal)

```
function successor(root, target):
    succ = None; cur = root
    while cur:
        if cur.val > target:
            succ = cur; cur = cur.left      ← candidate, seek smaller-but-bigger
        else:
            cur = cur.right
    return succ

function predecessor(root, target):
    pred = None; cur = root
    while cur:
        if cur.val < target:
            pred = cur; cur = cur.right      ← candidate, seek bigger-but-smaller
        else:
            cur = cur.left
    return pred
```

---

## Python Code

```python
class TreeNode:
    def __init__(self, val, left=None, right=None):
        self.val = val; self.left = left; self.right = right

def inorder_successor(root, target):
    succ = None
    cur = root
    while cur:
        if cur.val > target:
            succ = cur           # candidate successor; look for a closer one
            cur = cur.left
        else:
            cur = cur.right
    return succ

def inorder_predecessor(root, target):
    pred = None
    cur = root
    while cur:
        if cur.val < target:
            pred = cur           # candidate predecessor
            cur = cur.right
        else:
            cur = cur.left
    return pred


# Build BST and test  (inorder: 2 5 8 10 12)
n = {v: TreeNode(v) for v in [10,5,12,2,8]}
n[10].left, n[10].right = n[5], n[12]
n[5].left, n[5].right = n[2], n[8]
print(inorder_successor(n[10], 8).val)     # 10
print(inorder_predecessor(n[10], 8).val)   # 5
print(inorder_successor(n[10], 12))        # None (largest)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Full inorder | O(n) | O(n) |
| Guided walk | O(h) | O(1) |

---

## Edge Cases to remember

- **Successor of the max / predecessor of the min** → None.
- **Target need not exist** — the guided walk still finds the correct neighbor (this doubles as ceil/floor).
- **Node-based variant with parent pointers** — successor = leftmost of right subtree, else climb to first "left-turn" ancestor.
- **Strictly greater / smaller** — use `>` and `<` (not `>=`) so equal values aren't returned as their own successor.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Inorder Successor in BST (LC 285) | This |
| 2 | Ceil / Floor in BST | Same guided walk |
| 3 | Closest BST value (LC 270) | Track closest |
| 4 | Search in BST (LC 700) | Guided walk |
| 5 | BST Iterator (LC 173) | Inorder streaming |
| 6 | Kth smallest in BST (LC 230) | Inorder count |
| 7 | Two Sum in BST (Q5) | Ordered traversal |
| 8 | Range queries in BST | Prune walk |

---

## The ONE trick to remember

```
"SUCCESSOR: go LEFT recording candidates when cur > target. PREDECESSOR: mirror."
```

Descend the BST. For the successor, whenever a node is greater than the target, it's a candidate — remember it and go left to find something closer. Predecessor is the mirror (go right on smaller nodes). O(h), O(1).

> Memory hook: "Successor hides on the left turns; predecessor on the right turns."

---
