# ⚡ Quick Note · Day 8 — Strings & Graphs · Q13 · DSU (Union-Find)
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Track disjoint sets: find(x), union(x,y) in near O(1)."

## Pattern
`Union-Find: path compression + union by rank/size`

## Visual in your head
```
parent forest; find → root (set id)
path compression flattens on find
union by rank: small tree under large
same root ⇒ connected (union returns False = cycle)
```

## The trick (say it out loud)
> "find with path compression + union by rank = near O(1). Same root means connected."

## Code skeleton
```python
def find(x):
    while parent[x]!=x: parent[x]=parent[parent[x]]; x=parent[x]
    return x
def union(x,y):
    rx,ry=find(x),find(y)
    if rx==ry: return False
    # attach by rank
```

## Complexity
- find/union O(α(n)) · Space O(n)

## This trick solves more
Provinces (547) · Connected Components (323) · Redundant Connection (684) · Kruskal MST · Accounts Merge (721)
