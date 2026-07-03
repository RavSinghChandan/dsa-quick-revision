# Q2 — Diameter of Binary Tree (2-min card) — LC 543

**Longest path between any two nodes (measured in edges).**

**Pattern:** DFS Post-order — at each node, diameter through it = left_height + right_height; track global max

```
      1
     / \
    2   3
   / \
  4   5

height(4)=1, height(5)=1
height(2): update max=1+1=2; return 2
height(3)=1
height(1): update max=max(2, 2+1)=3; return 3

Answer = 3  (path: 4→2→1→3)
```

**The trick:** "Return height to parent, but update max_diameter = left + right at each node."

```python
max_d = [0]
def height(node):
    if not node: return 0
    l, r = height(node.left), height(node.right)
    max_d[0] = max(max_d[0], l+r)
    return 1 + max(l, r)
height(root); return max_d[0]
```

**Complexity:** Time O(n) | Space O(h)

**Same pattern solves:** Height of Tree (LC 104), Diameter N-ary Tree (LC 1522), Longest Univalue Path (LC 687), Max Path Sum (LC 124), Balanced Tree (LC 110), Path Sum II (LC 113), Min Depth (LC 111).
