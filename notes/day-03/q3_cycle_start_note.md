# Q3 — Find Starting Point of Cycle (2-min card) — LC 142

**Find the node where the cycle begins in a linked list. Return None if no cycle.**

**Pattern:** Floyd's Extended — Phase 1: detect meeting point; Phase 2: reset slow to head, advance both by 1

```
List: 0→1→2→3→4→5→6→7→(back to 3)
Phase 1: slow=0,fast=0 → meet at node 5 (inside cycle)
Phase 2: slow=head(0), fast=5
  0→1, 5→6 | 1→2, 6→7 | 2→3, 7→3 ← MEET at 3 = cycle start ✓

Math: F (head→start) = n*C - K (dist from meet to start)
So both pointers travel same distance to reach cycle start.
```

**The trick:** "After meeting, reset one to head, advance both by 1 — they meet at cycle start."

```python
slow = fast = head
while fast and fast.next:          # Phase 1
    slow = slow.next; fast = fast.next.next
    if slow is fast: break
else: return None                  # no cycle
slow = head                        # Phase 2
while slow is not fast:
    slow = slow.next; fast = fast.next
return slow
```

**Complexity:** Time O(n) | Space O(1)

**Same pattern solves:** Detect Cycle (LC 141), Find Duplicate Number (LC 287), Happy Number (LC 202), Middle of LL (LC 876), Intersection Two LLs (LC 160), Palindrome LL (LC 234), Reorder List (LC 143).
