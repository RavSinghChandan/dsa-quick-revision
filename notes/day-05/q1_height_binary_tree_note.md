# Q1 — Height of Binary Tree (2-min card) — LC 104

**Find the maximum depth (number of nodes on longest root-to-leaf path).**

**Pattern:** DFS Post-order — height = 1 + max(left, right)

```
        3
       / \
      9   20
         /  \
        15   7

height(9) =1, height(15)=1, height(7)=1
height(20)=1+max(1,1)=2
height(3) =1+max(1,2)=3  ← Answer
```

**The trick:** "height = 1 + max(left, right); None returns 0."

```python
def maxDepth(root):
    if not root: return 0
    return 1 + max(maxDepth(root.left), maxDepth(root.right))
```

**Complexity:** Time O(n) | Space O(h) where h=height

**Same pattern solves:** Min Depth (LC 111), Diameter (LC 543), Balanced Tree (LC 110), Count Complete Nodes (LC 222), Path Sum (LC 112), Max Path Sum (LC 124), Sum Left Leaves (LC 404), Right Side View (LC 199).
