# ⚡ Quick Note · Day 4 — Linked List · Q1 · Sort 0/1/2 LL
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Sort a linked list of only 0/1/2 without an array."

## Pattern
`Three dummy heads: distribute nodes, then stitch chains`

## Visual in your head
```
walk once → bucket each node into zero/one/two chain
stitch: zero→one→two, null-terminate tail
skip empty chains when linking
```

## The trick (say it out loud)
> "Three dummy heads. One pass to distribute, then tie z→o→t. Skip empty chains, null the tail."

## Code skeleton
```python
if v==0: t0.next=node; t0=node ...
t2.next=None
t1.next=d2.next
t0.next=d1.next or d2.next
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
Partition List (86) · Odd Even LL (328) · Segregate even/odd nodes · Merge sorted lists
