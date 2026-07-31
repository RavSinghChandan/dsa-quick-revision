# Day 8 — Strings & Graphs · Q13 · Disjoint Set Union (Union-Find)

---

## What is this question actually asking?

Design a structure that tracks a partition of elements into disjoint sets, supporting:
- `find(x)` → which set x belongs to (its representative)
- `union(x, y)` → merge the sets of x and y

...both in **near O(1)** (amortized α(n), inverse-Ackermann).

```
union(1,2), union(2,3), find(1)==find(3) → True ; find(1)==find(4) → False
```

> The backbone of connectivity problems: connected components, cycle detection in undirected graphs, and Kruskal's MST.

---

## Pattern

```
PATTERN: Union-Find with Path Compression + Union by Rank/Size
```

Whenever you see: *"are these connected / merge groups / count components / detect undirected cycle"* → use DSU. Path compression flattens trees on `find`; union by rank keeps trees shallow. Together → near-constant per operation.

---

## Understand with a diagram

```
parent[] forest. find(x) follows parent to the root (the set id).

Path compression: on find, point every node directly to the root.
   1→2→3→root  becomes  1→root, 2→root, 3→root

Union by rank/size: attach the smaller tree under the larger root.
   keeps depth ~ O(α(n)) ≈ constant.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — relabel a whole group on union — O(n) per union
Scan and reassign labels. Slow.

### 🟢 Optimal — DSU with both optimizations — O(α(n)) amortized
Path compression + union by rank make find/union effectively constant.

---

## Pseudocode (Optimal)

```
class DSU(n):
    parent = [0..n-1]
    rank   = [0]*n

    find(x):
        if parent[x] != x:
            parent[x] = find(parent[x])     ← path compression
        return parent[x]

    union(x, y):
        rx, ry = find(x), find(y)
        if rx == ry: return False           ← already same set (cycle)
        if rank[rx] < rank[ry]: rx, ry = ry, rx
        parent[ry] = rx
        if rank[rx] == rank[ry]: rank[rx]++
        return True
```

---

## Python Code

```python
class DSU:
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, x: int) -> int:
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]   # path halving
            x = self.parent[x]
        return x

    def union(self, x: int, y: int) -> bool:
        rx, ry = self.find(x), self.find(y)
        if rx == ry:
            return False                    # already connected (cycle if undirected)
        if self.rank[rx] < self.rank[ry]:
            rx, ry = ry, rx
        self.parent[ry] = rx                # attach smaller under larger
        if self.rank[rx] == self.rank[ry]:
            self.rank[rx] += 1
        return True


# Test
dsu = DSU(5)
dsu.union(1, 2); dsu.union(2, 3)
print(dsu.find(1) == dsu.find(3))   # True
print(dsu.find(1) == dsu.find(4))   # False
print(dsu.union(1, 3))              # False (already connected)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| find / union | O(α(n)) ≈ O(1) amortized | O(n) |

---

## Edge Cases to remember

- **Both optimizations matter** — path compression alone or union-by-rank alone is O(log n); together it's α(n).
- **`union` returns False when already connected** — that's how you detect a **cycle** in an undirected graph (an edge within one set).
- **Component count** — start at n, decrement on each successful union.
- **Union by size vs rank** — either works; size lets you query component sizes.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Number of Provinces (LC 547) | Count components |
| 2 | Number of Connected Components (LC 323) | DSU |
| 3 | Redundant Connection (LC 684) | Cycle edge |
| 4 | Kruskal's MST | Union edges by weight |
| 5 | Accounts Merge (LC 721) | Union by email |
| 6 | Graph Valid Tree (LC 261) | n-1 edges, no cycle |
| 7 | Most Stones Removed (LC 947) | Union rows/cols |
| 8 | Kosaraju alternative for undirected | Connectivity |

---

## The ONE trick to remember

```
"find WITH PATH COMPRESSION + union BY RANK → NEAR O(1). SAME ROOT = CONNECTED."
```

Each set is a tree with a representative root. `find` flattens the path to the root; `union` hangs the smaller tree under the larger. Two elements are connected iff they share a root; a union that finds them already joined signals a cycle.

> Memory hook: "Everyone points to the boss; merging joins two bosses under one."

---
