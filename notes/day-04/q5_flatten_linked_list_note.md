# ⚡ Quick Note · Day 4 — Linked List · Q5 · Flatten LL
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Flatten next/bottom sorted sub-lists into one sorted bottom-list."

## Pattern
`Recursively merge sorted sub-lists (right to left) on bottom pointer`

## Visual in your head
```
flatten(head) = merge(head, flatten(head.next))
merge on BOTTOM pointer (both sorted)
```

## The trick (say it out loud)
> "Flatten the right first, then merge this column into it. Merge on BOTTOM, not next."

## Code skeleton
```python
if not head or not head.next: return head
head.next=flatten(head.next)
return merge(head,head.next)   # merge via .bottom
```

## Complexity
- Time O(N×K) · Space O(K) recursion

## This trick solves more
Merge Two Sorted (21) · Merge K Lists (23) · Flatten Multilevel (430) · Sort List (148)
