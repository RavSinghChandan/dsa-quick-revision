# Q4 — Middle of Linked List (2-min card) — LC 876

**Return the middle node (second middle if even length).**

**Pattern:** Slow/Fast Pointers — fast moves 2x; when fast stops, slow is at middle

```
Odd:  1→2→3→4→5
slow: 1→2→3   fast: 1→3→5 (fast.next=None → stop)  → return slow=3 ✓

Even: 1→2→3→4→5→6
slow: 1→2→3→4  fast: 1→3→5→None → stop  → return slow=4 ✓
```

**The trick:** "Fast runs twice as fast — when it stops, slow is at the middle."

```python
slow = fast = head
while fast and fast.next:
    slow = slow.next
    fast = fast.next.next
return slow
```

**Complexity:** Time O(n) | Space O(1)

**Same pattern solves:** Palindrome LL (LC 234), Reorder List (LC 143), Detect Cycle (LC 141), Cycle Start (LC 142), Sort List (LC 148), Remove Nth from End (LC 19), Odd Even LL (LC 328).
