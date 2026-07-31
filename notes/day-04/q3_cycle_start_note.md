# ⚡ Quick Note · Day 4 — Linked List · Q3 · Cycle Start
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Find the node where the cycle begins (or None)."

## Pattern
`Floyd's slow/fast + reset one to head`

## Visual in your head
```
slow(1x), fast(2x) meet in loop
reset p=head; step p & slow by 1
they meet at the cycle START
```

## The trick (say it out loud)
> "Meet with slow/fast, reset one pointer to head, step both by one — they meet at the start."

## Code skeleton
```python
while fast and fast.next:
    slow=slow.next; fast=fast.next.next
    if slow is fast:
        p=head
        while p is not slow: p=p.next; slow=slow.next
        return p
return None
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
Cycle detect (141) · Find Duplicate (287) · Happy Number (202) · Circular Array Loop (457)
