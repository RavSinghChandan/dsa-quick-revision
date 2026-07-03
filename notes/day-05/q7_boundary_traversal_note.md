# Q7 — Boundary Traversal of Binary Tree (2-min card)

**Print boundary nodes anticlockwise: left boundary (top-down) + leaves (L→R) + right boundary (bottom-up).**

**Pattern:** Three separate DFS/iterations — left boundary, all leaves, right boundary reversed

```
         20
        /  \
       8    22
      / \     \
     4   12    25
        /  \
       10   14

Root: [20]
Left boundary (non-leaves, top-down): [8] (4 is leaf → skip)
All leaves (L→R): [4, 10, 14, 25]
Right boundary (non-leaves, reversed): [22] (25 is leaf → skip)

Result: [20, 8, 4, 10, 14, 25, 22]
```

**The trick:** "Split into 3: left↓, leaves L→R, right↑ — skip leaves in boundary parts."

```python
# Left boundary: go left-first, skip leaves
node = root.left
while node:
    if not is_leaf(node): result.append(node.val)
    node = node.left if node.left else node.right
# Leaves: full DFS, add only leaves
# Right boundary: go right-first, collect, then reverse and add
```

**Complexity:** Time O(n) | Space O(n)

**Same pattern solves:** Level Order (LC 102), Right Side View (LC 199), Left Side View (GFG), Print All Paths (LC 113), Vertical Order (LC 987), Diagonal Traversal (GFG), Morris Traversal, All Nodes Dist K (LC 863).
