# Q2 — Detect Cycle in Linked List (2-min card) — LC 141

**Does a linked list have a cycle? Return True/False.**

**Pattern:** Floyd's Cycle Detection — slow moves 1 step, fast moves 2 steps

```
List: 1→2→3→4→5→6→(back to 3)

slow: 1→2→3→4→5→6→3→4
fast: 1→3→5→3→5→3→5→3 ... they meet at 3 ✓

No cycle → fast reaches None first → False
```

**The trick:** "Tortoise and hare: if they ever meet, there's a cycle — fast laps slow."

```python
slow = fast = head
while fast and fast.next:
    slow = slow.next
    fast = fast.next.next
    if slow is fast:   # same object, not just same value
        return True
return False
```

**Complexity:** Time O(n) | Space O(1)

**Same pattern solves:** Find Cycle Start (LC 142), Happy Number (LC 202), Find Duplicate Number (LC 287), Middle of LL (LC 876), Palindrome LL (LC 234), Intersection of Two LLs (LC 160), Remove Nth from End (LC 19).
