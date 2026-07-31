# Day 6 — Binary Tree · Q2 · Bottom View of Binary Tree

---

## What is this question actually asking?

Looking at the tree **from below**, what nodes do you see? For each **horizontal distance** column, the **lowest** node (last one in top-to-bottom order).

```
       20
      /  \
    8      22
   / \    /  \
  5  3  4    25
      / \
     10  14
Bottom view → 5 10 4 14 25   (left to right by column)
```

> Tests **horizontal distance (HD) + level-order BFS**. Root HD = 0; left child HD−1, right child HD+1. Keep the last node seen per HD.

---

## Pattern

```
PATTERN: BFS level-order + Horizontal Distance map (overwrite per column)
```

Whenever you see: *"top/bottom view / vertical order"* → assign each node a horizontal distance; process level by level; for **bottom** view, keep the **last** node per HD (overwrite). Sort by HD to output left→right.

---

## Understand with a diagram

```
HD: root=0, left = HD-1, right = HD+1

BFS queue of (node, hd). map[hd] = node.val  (overwrite → lower level wins)

columns:  -2 -1  0  1  2
          5  10  4? ...  → last node in each column, left to right
```

Using BFS (not DFS) guarantees a deeper node overwrites a shallower one at the same HD.

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — DFS tracking (hd, level) — O(n log n)
Record deepest node per hd with level comparison; needs level bookkeeping.

### 🟢 Optimal — BFS + HD map — O(n)
Level-order traversal naturally visits shallower nodes first; overwriting the HD map leaves the bottom-most node per column. Sort keys at the end.

---

## Pseudocode (Optimal)

```
function bottom_view(root):
    if not root: return []
    map = {}                       ← hd → node value (last wins)
    queue = [(root, 0)]
    while queue:
        node, hd = queue.pop_front()
        map[hd] = node.val         ← overwrite → bottom-most in BFS order
        if node.left:  queue.push((node.left,  hd-1))
        if node.right: queue.push((node.right, hd+1))
    return [map[hd] for hd in sorted(map)]
```

---

## Python Code

```python
from collections import deque

class TreeNode:
    def __init__(self, val, left=None, right=None):
        self.val = val; self.left = left; self.right = right

def bottom_view(root) -> list[int]:
    if not root:
        return []
    hd_map = {}                         # horizontal distance -> value
    queue = deque([(root, 0)])
    while queue:
        node, hd = queue.popleft()
        hd_map[hd] = node.val           # last (deepest in BFS) wins
        if node.left:
            queue.append((node.left, hd - 1))
        if node.right:
            queue.append((node.right, hd + 1))
    return [hd_map[hd] for hd in sorted(hd_map)]


# Build tree and test
n = {v: TreeNode(v) for v in [20,8,22,5,3,4,25,10,14]}
n[20].left, n[20].right = n[8], n[22]
n[8].left,  n[8].right  = n[5], n[3]
n[22].left, n[22].right = n[4], n[25]
n[3].left,  n[3].right  = n[10], n[14]
print(bottom_view(n[20]))   # [5, 10, 4, 14, 25]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| BFS + map | O(n log n) (sort) | O(n) |

---

## Edge Cases to remember

- **BFS not DFS** — BFS guarantees deeper nodes overwrite shallower ones at the same HD. With DFS you must also compare levels.
- **Overwrite for BOTTOM view** — for **TOP** view, keep only the FIRST node per HD (don't overwrite).
- **Sort HD keys** to print left→right; a plain dict isn't ordered by key.
- **Ties at same HD and level** — right child (visited later in BFS) wins; standard convention.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Top View of Binary Tree | Keep first per HD |
| 2 | Vertical Order Traversal (LC 987) | HD + sort by level/value |
| 3 | Right/Left View (LC 199) | Last/first per level |
| 4 | Level Order Traversal (LC 102) | BFS |
| 5 | Boundary Traversal | View edges |
| 6 | Diagonal traversal | HD-like grouping |
| 7 | Print columns of a tree | HD map |
| 8 | Zigzag level order | BFS |

---

## The ONE trick to remember

```
"HD map + BFS: root=0, left−1 right+1; OVERWRITE per column → bottom-most wins."
```

Assign horizontal distances, traverse level by level, and keep the last node seen per HD. Sort HD keys to read columns left to right. Top view is the same but keeps the *first* per column.

> Memory hook: "Number the columns, drop a node into each — the last drop is what you see from below."

---
