# ⚡ Quick Note · Day 7 — BST & Heap · Q4 · Largest BST
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Size of the largest subtree that is a valid BST."

## Pattern
`Post-order DFS returning (min, max, size, isBST)`

## Visual in your head
```
node is BST iff L.isBST & R.isBST & L.max<val<R.min
if BST: size=L.size+R.size+1; update best
else: return invalid range, size=max(L,R)
```

## The trick (say it out loud)
> "Post-order return (min,max,size,isBST). Parent checks lmax < val < rmin. Track best size."

## Code skeleton
```python
if lbst and rbst and lmax<node.val<rmin:
    size=lsize+rsize+1; best=max(best,size)
    return (min(lmin,val),max(rmax,val),size,True)
return (-inf,inf,max(lsize,rsize),False)
```

## Complexity
- Time O(n) · Space O(h)

## This trick solves more
Validate BST (98) · Largest BST Subtree (333) · Diameter (543) · Max Path Sum (124) · Balanced (110)
