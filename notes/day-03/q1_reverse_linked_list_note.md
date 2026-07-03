# Q1 — Reverse a Linked List (2-min card) — LC 206

**Reverse a singly linked list in-place. Return new head.**

**Pattern:** Three-pointer iterative reversal

```
1 → 2 → 3 → 4 → 5 → None

prev=None  curr=1
save next=2, flip 1.next=None, slide: prev=1, curr=2
save next=3, flip 2.next=1,    slide: prev=2, curr=3
save next=4, flip 3.next=2,    slide: prev=3, curr=4
save next=5, flip 4.next=3,    slide: prev=4, curr=5
save next=None, flip 5.next=4, slide: prev=5, curr=None

Return prev=5  →  5→4→3→2→1→None ✓
```

**The trick:** "Save next, flip arrow, slide both — three lines inside the loop."

```python
prev, curr = None, head
while curr:
    nxt = curr.next
    curr.next = prev
    prev, curr = curr, nxt
return prev
```

**Complexity:** Time O(n) | Space O(1)

**Same pattern solves:** Reverse LL II (LC 92), Palindrome LL (LC 234), Reverse k-Group (LC 25), Swap in Pairs (LC 24), Reorder List (LC 143), Rotate List (LC 61), Add Two Numbers (LC 2), Odd Even LL (LC 328).
