# Day 6 — Binary Tree · Q4 · Minimum Time to Burn the Binary Tree

---

## What is this question actually asking?

Fire starts at a **target** node and spreads to adjacent nodes (children AND parent) each second. Find the **time to burn the whole tree**.

```
Fire spreads up to the parent and down to both children every second.
Answer = the farthest distance from the target to any node.
```

> The clever reframe: turn the tree into an **undirected graph** (add parent links), then it's just **BFS from the target** — the last level burned is the answer.

---

## Pattern

```
PATTERN: Build parent pointers → BFS (multi-directional) from target
```

Whenever you see: *"spread/infection/distance in a tree that moves to parent AND children"* → record each node's parent, then BFS outward from the source. Fire reaches all three neighbors (parent, left, right) each step.

---

## Understand with a diagram

```
Step 1: DFS to record parent[node] for every node, and find the target.
Step 2: BFS from target. Each second, burn all unburned neighbors:
        node.left, node.right, parent[node].

time = number of BFS levels - 1  (levels needed to reach the farthest node)
```

The tree becomes an undirected graph where each node has ≤ 3 neighbors.

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — recompute distances repeatedly — O(n²)
For each node, compute distance to target separately.

### 🟢 Optimal — parent map + BFS — O(n)
One pass to map parents (and find target), one BFS to find the maximum distance. Linear.

---

## Pseudocode (Optimal)

```
function burn_time(root, target_val):
    parent = {}
    target = None
    # 1. BFS/DFS to fill parent and find target node
    fill parents; locate target
    # 2. BFS from target
    visited = {target}
    queue = [target]; time = -1
    while queue:
        for each node in current level:
            for nb in (node.left, node.right, parent[node]):
                if nb and nb not in visited:
                    visited.add(nb); queue.push(nb)
        time += 1
    return time
```

---

## Python Code

```python
from collections import deque

class TreeNode:
    def __init__(self, val, left=None, right=None):
        self.val = val; self.left = left; self.right = right

def min_burn_time(root, target_val: int) -> int:
    # 1. map parents + find target
    parent = {}
    target = None
    q = deque([root])
    while q:
        node = q.popleft()
        if node.val == target_val:
            target = node
        for child in (node.left, node.right):
            if child:
                parent[child] = node
                q.append(child)

    # 2. BFS outward from target
    visited = {target}
    q = deque([target])
    time = -1
    while q:
        for _ in range(len(q)):
            node = q.popleft()
            for nb in (node.left, node.right, parent.get(node)):
                if nb and nb not in visited:
                    visited.add(nb)
                    q.append(nb)
        time += 1                       # one second per level
    return time


# Test
n = {v: TreeNode(v) for v in [1,2,3,4,5,6,7]}
n[1].left, n[1].right = n[2], n[3]
n[2].left, n[2].right = n[4], n[5]
n[3].left, n[3].right = n[6], n[7]
print(min_burn_time(n[1], 2))   # 3  (2→1→3→6/7)
print(min_burn_time(n[1], 4))   # 4
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Parent map + BFS | O(n) | O(n) |

---

## Edge Cases to remember

- **Add parent links** — the fire spreads UP too; without parents you only burn downward.
- **`time = -1` init** — because the target's own level is second 0; incrementing after each level gives the farthest distance.
- **Root as target** → answer is the tree height.
- **Single node** → 0 seconds.
- **Level-by-level BFS** (`for _ in range(len(q))`) counts seconds correctly.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | All Nodes Distance K (LC 863) | Parent map + BFS |
| 2 | Amount of Time to Infect Tree (LC 2385) | Same problem |
| 3 | Rotting Oranges (LC 994) | Multi-source BFS grid |
| 4 | Shortest path in tree | BFS |
| 5 | Max distance between two nodes (diameter) | DFS |
| 6 | Distance between two nodes | LCA + depth |
| 7 | Tree → graph problems | Add parent edges |
| 8 | Burning/flood in grid | BFS levels |

---

## The ONE trick to remember

```
"ADD PARENT LINKS → TREE BECOMES A GRAPH → BFS FROM TARGET; LEVELS = TIME."
```

Fire moving to the parent means the tree is really undirected. Record parents, then BFS from the burning node; the number of BFS levels to reach the farthest node is the burn time.

> Memory hook: "Let it burn up and down — that's just BFS on a graph with parent edges."

---
