# ⚡ Quick Note · Day 4 — Linked List · Q2 · Palindrome LL
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Is the linked list a palindrome? O(1) space."

## Pattern
`Slow/fast middle + reverse 2nd half + compare`

## Visual in your head
```
slow/fast → middle
reverse second half → head2
compare head vs head2 inward
```

## The trick (say it out loud)
> "Find middle (slow/fast), reverse the second half, compare both halves inward."

## Code skeleton
```python
while fast and fast.next: slow=slow.next; fast=fast.next.next
prev=None
while slow: slow.next,prev,slow=prev,slow,slow.next
while prev:
    if left.val!=prev.val: return False
    left=left.next; prev=prev.next
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
Reverse LL (206) · Middle of LL (876) · Reorder List (143) · Max Twin Sum (2130)
