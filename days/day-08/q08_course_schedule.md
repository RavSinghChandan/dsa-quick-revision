# Day 8 — Strings & Graphs · Q8 · Course Schedule I

---

## What is this question actually asking?

Given `numCourses` and prerequisite pairs `[a, b]` (take `b` before `a`), can you **finish all courses**? (Equivalently: is the prerequisite graph **acyclic**?)

```
2, [[1,0]]        → True   (0 then 1)
2, [[1,0],[0,1]]  → False  (cycle)
```

> A pure **cycle detection in a directed graph** — solvable with Kahn's topological sort or DFS with a recursion-stack color.

---

## Pattern

```
PATTERN: Directed cycle detection (Kahn's topo count OR DFS 3-color)
```

Whenever you see: *"can all tasks be done / any circular dependency"* → build the dependency graph; a valid ordering exists iff there's no cycle. Kahn's: can you order all n? DFS: is there a back-edge to a node on the current recursion stack?

---

## Understand with a diagram

```
Kahn's: if topological sort orders all courses → no cycle → True.

DFS 3-color:
  WHITE (unvisited), GRAY (on current path), BLACK (done)
  visiting a GRAY node → back edge → CYCLE → False
  finish a node → mark BLACK
```

---

## Brute Force → Better → Optimal

### 🟢 Approach A — Kahn's topo sort — O(V + E)
Count nodes you can order; if `< numCourses`, a cycle blocks the rest → False.

### 🟢 Approach B — DFS with colors — O(V + E)
Detect a back-edge (revisiting a GRAY node). Same complexity.

---

## Pseudocode (Kahn's)

```
function can_finish(n, prereqs):
    build adj + indegree   (edge b → a for [a,b])
    queue = indegree-0 nodes
    seen = 0
    while queue:
        u = pop; seen++
        for v in adj[u]:
            indegree[v]--; if 0: queue.push(v)
    return seen == n
```

---

## Python Code

```python
from collections import deque

def can_finish(num_courses: int, prerequisites: list[list[int]]) -> bool:
    adj = [[] for _ in range(num_courses)]
    indeg = [0] * num_courses
    for a, b in prerequisites:          # must take b before a → edge b -> a
        adj[b].append(a)
        indeg[a] += 1

    q = deque([i for i in range(num_courses) if indeg[i] == 0])
    seen = 0
    while q:
        u = q.popleft()
        seen += 1
        for v in adj[u]:
            indeg[v] -= 1
            if indeg[v] == 0:
                q.append(v)
    return seen == num_courses          # ordered all → no cycle


# DFS 3-color version
def can_finish_dfs(n, prerequisites):
    adj = [[] for _ in range(n)]
    for a, b in prerequisites:
        adj[b].append(a)
    color = [0] * n                     # 0 white, 1 gray, 2 black
    def dfs(u):
        color[u] = 1                    # gray = on current path
        for v in adj[u]:
            if color[v] == 1:           # back edge → cycle
                return False
            if color[v] == 0 and not dfs(v):
                return False
        color[u] = 2
        return True
    return all(dfs(i) for i in range(n) if color[i] == 0)


# Test
print(can_finish(2, [[1,0]]))          # True
print(can_finish(2, [[1,0],[0,1]]))    # False
print(can_finish_dfs(2, [[1,0],[0,1]]))# False
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Kahn's / DFS | O(V + E) | O(V + E) |

---

## Edge Cases to remember

- **Edge direction** — `[a, b]` means b → a (b is prerequisite). Getting the direction backward inverts the answer.
- **No prerequisites** → always True.
- **DFS needs 3 colors** — GRAY distinguishes "on current path" (back edge = cycle) from BLACK "already fully explored" (cross edge, fine).
- **Self-loop** `[i, i]` → cycle → False.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Course Schedule II (LC 210) | Return the order |
| 2 | Topological Sort (Q7) | Same engine |
| 3 | Detect cycle in directed graph | DFS colors |
| 4 | Alien Dictionary (Q9) | Cycle → invalid |
| 5 | Find eventual safe states (LC 802) | Reverse cycle |
| 6 | Parallel courses (LC 1136) | Topo levels |
| 7 | Build system dependency check | Cycle detect |
| 8 | Deadlock detection | Cycle detect |

---

## The ONE trick to remember

```
"NO CYCLE ⇔ CAN FINISH. KAHN'S: ORDERED ALL n? DFS: GRAY REVISIT = CYCLE."
```

Finishing all courses is possible exactly when the prerequisite graph is acyclic. Kahn's counts how many you can order; DFS flags a back-edge to a node still on the recursion stack (GRAY).

> Memory hook: "A circular prerequisite is a deadlock — no cycle means you can finish."

---
