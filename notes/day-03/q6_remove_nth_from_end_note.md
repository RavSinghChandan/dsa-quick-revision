# Q6 — Remove Nth Node from End (2-min card) — LC 19

**Remove the nth node from the end of a linked list. One pass.**

**Pattern:** Two Pointers with gap of n+1 — dummy head handles head removal

```
List: dummy→1→2→3→4→5   n=2

Advance fast n+1=3 steps: fast at node 3
dummy  1  2  3  4  5
  S         F

Advance both until fast=None:
  S→1, F→4  |  S→2, F→5  |  S→3, F=None → stop

slow=3, slow.next=4 (the 2nd from end)
slow.next = slow.next.next = 5

Result: dummy→1→2→3→5 ✓
```

**The trick:** "Gap of n+1: slow lands one before the target node, so slow.next = slow.next.next deletes it."

```python
dummy = ListNode(0); dummy.next = head
slow = fast = dummy
for _ in range(n+1): fast = fast.next
while fast: slow = slow.next; fast = fast.next
slow.next = slow.next.next
return dummy.next
```

**Complexity:** Time O(n) | Space O(1)

**Same pattern solves:** Middle of LL (LC 876), Detect Cycle (LC 141), Cycle Start (LC 142), Remove LL Elements (LC 203), Rotate List (LC 61), Reorder List (LC 143), Intersection Two LLs (LC 160).
