# ⚡ Quick Note · Day 8 — Strings & Graphs · Q9 · Alien Dictionary
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Deduce alphabet order from words sorted in that alphabet."

## Pattern
`Precedence graph from adjacent words → topological sort`

## Visual in your head
```
adjacent (w1,w2): first differing char → edge c1→c2
invalid: w1 longer & w2 is prefix → ""
topo sort letters; cycle → ""
```

## The trick (say it out loud)
> "First diff of each adjacent word pair = an edge. Topo-sort. Cycle or bad-prefix ⇒ empty string."

## Code skeleton
```python
for a,b in zip(w1,w2):
    if a!=b: adj[a].add(b); indeg[b]+=1; break
# Kahn topo; if len(order)<letters → ""
```

## Complexity
- Time O(total chars) · Space O(letters+edges)

## This trick solves more
Course Schedule II (210) · Topo Sort · Sequence Reconstruction (444) · Verify Alien Dict (953)
