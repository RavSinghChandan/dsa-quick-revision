# ⚡ Quick Note · Day 4 — Linked List · Q6 · Y-Intersection
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Find the first shared node of two Y-shaped lists."

## Pattern
`Two pointers switching heads to equalize length`

## Visual in your head
```
pa: A then B   pb: B then A
both walk lenA+lenB → meet at intersection
no intersection → both hit None together
```

## The trick (say it out loud)
> "pa walks A then B, pb walks B then A. They meet at the intersection (or None together)."

## Code skeleton
```python
while pa is not pb:
    pa = pa.next if pa else headB
    pb = pb.next if pb else headA
return pa
```

## Complexity
- Time O(m+n) · Space O(1)

## This trick solves more
Intersection of Lists (160) · Cycle start (Q3) · Nth from end · Merge point of paths
