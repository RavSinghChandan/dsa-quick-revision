# Q6 — Zigzag Level Order Traversal (2-min card) — LC 103

**Level order traversal but alternate L→R and R→L each level.**

**Pattern:** BFS + direction flag — collect level normally, reverse if R→L, toggle flag

```
Tree:   3
       / \
      9   20
         /  \
        15   7

L→R: level=[3]    → append [3]
R→L: level=[9,20] → reverse → [20,9]
L→R: level=[15,7] → append [15,7]

Result: [[3],[20,9],[15,7]] ✓
```

**The trick:** "Standard BFS — just reverse alternate levels before adding to result."

```python
queue, result, l2r = deque([root]), [], True
while queue:
    level = []
    for _ in range(len(queue)):
        node = queue.popleft()
        level.append(node.val)
        if node.left:  queue.append(node.left)
        if node.right: queue.append(node.right)
    result.append(level if l2r else level[::-1])
    l2r = not l2r
return result
```

**Complexity:** Time O(n) | Space O(n)

**Same pattern solves:** Level Order (LC 102), Right Side View (LC 199), Avg of Levels (LC 637), Largest Value per Row (LC 515), Spiral Matrix (LC 54), Reverse Level Order (GFG), Max Depth BFS (LC 104), N-ary Zigzag.
