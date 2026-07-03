# Q5 — Level Order Traversal (2-min card) — LC 102

**Return values level by level as list of lists.**

**Pattern:** BFS — snapshot queue size to mark level boundaries

```
Tree:        3
            / \
           9   20
              /  \
             15   7

queue=[3] → process 1: [3], enqueue 9,20
queue=[9,20] → process 2: [9,20], enqueue 15,7
queue=[15,7] → process 2: [15,7]
Result: [[3],[9,20],[15,7]] ✓
```

**The trick:** "Record len(queue) before the inner loop — that's how many nodes belong to this level."

```python
from collections import deque
queue, result = deque([root]), []
while queue:
    level = []
    for _ in range(len(queue)):   # snapshot size = current level
        node = queue.popleft()
        level.append(node.val)
        if node.left:  queue.append(node.left)
        if node.right: queue.append(node.right)
    result.append(level)
return result
```

**Complexity:** Time O(n) | Space O(n)

**Same pattern solves:** Zigzag Level Order (LC 103), Right Side View (LC 199), Avg of Levels (LC 637), Largest Value per Row (LC 515), Max Width Tree (LC 662), N-ary Level Order (LC 429), Min Depth (LC 111), Cousins (LC 993).
