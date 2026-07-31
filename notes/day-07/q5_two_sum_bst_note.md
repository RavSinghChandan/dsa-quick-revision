# ⚡ Quick Note · Day 7 — BST & Heap · Q5 · Two Sum in BST
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Is there a pair of BST nodes summing to k?"

## Pattern
`Hash set complement (any tree) OR two pointers on sorted inorder`

## Visual in your head
```
set: dfs; if (k-val) in seen → True; add val
2ptr: inorder ascending; l,r inward
  sum<k → l++, sum>k → r--, ==k → True
```

## The trick (say it out loud)
> "Hash set complement like array Two Sum, or two-pointer inward on the sorted inorder."

## Code skeleton
```python
if (k-node.val) in seen: return True
seen.add(node.val)
return dfs(left) or dfs(right)
```

## Complexity
- Set O(n)/O(n) · Two-ptr O(n)/O(h)

## This trick solves more
Two Sum (Day 12) · Two Sum IV BST (653) · Two Sum II sorted (167) · BST Iterator (173)
