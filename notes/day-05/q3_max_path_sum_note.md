# Q3 — Maximum Path Sum in Binary Tree (2-min card) — LC 124

**Find the maximum sum path between any two nodes (can include negative values).**

**Pattern:** DFS Post-order — "gain" function updates global max, returns single direction to parent

```
    -10
    / \
   9   20
      /  \
     15   7

gain(9)=9, answer=9
gain(15)=15, gain(7)=7
gain(20): left=15, right=7
  answer = max(9, 20+15+7=42) = 42 ← max!
  return 20+15=35 (one direction up)
gain(-10): answer = max(42, -10+9+35=34) = 42
Answer = 42
```

**The trick:** "Update answer with left+node+right (bent path); return node+max(left,right) (one direction only)."

```python
max_s = [float('-inf')]
def gain(node):
    if not node: return 0
    l, r = max(0, gain(node.left)), max(0, gain(node.right))
    max_s[0] = max(max_s[0], node.val + l + r)
    return node.val + max(l, r)
gain(root); return max_s[0]
```

**Complexity:** Time O(n) | Space O(h)

**Same pattern solves:** Diameter (LC 543), Path Sum (LC 112), Path Sum II (LC 113), Longest Univalue Path (LC 687), Sum Root-to-Leaf Numbers (LC 129), House Robber III (LC 337), Binary Tree Cameras (LC 968).
