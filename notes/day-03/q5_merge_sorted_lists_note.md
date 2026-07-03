# Q5 — Merge Two Sorted Linked Lists (2-min card) — LC 21

**Merge two sorted linked lists in-place into one sorted list.**

**Pattern:** Dummy head + two-pointer merge

```
l1: 1→2→4     l2: 1→3→4

dummy → curr
l1.val=1 <= l2.val=1 → attach l1: dummy→1, l1=2
l2.val=1 < l1.val=2  → attach l2: →1, l2=3
l1.val=2 <= l2.val=3 → attach l1: →2, l1=4
l2.val=3 < l1.val=4  → attach l2: →3, l2=4
l1.val=4 <= l2.val=4 → attach l1: →4, l1=None
l1=None → append l2: →4

Result: 1→1→2→3→4→4 ✓
```

**The trick:** "Dummy head + pick smaller head each step + attach leftover tail."

```python
dummy = curr = ListNode(0)
while l1 and l2:
    if l1.val <= l2.val: curr.next = l1; l1 = l1.next
    else: curr.next = l2; l2 = l2.next
    curr = curr.next
curr.next = l1 or l2
return dummy.next
```

**Complexity:** Time O(n+m) | Space O(1)

**Same pattern solves:** Merge K Sorted Lists (LC 23), Sort List (LC 148), Merge Sorted Array (LC 88), Add Two Numbers (LC 2), Remove Dups Sorted (LC 83), Intersection Two LLs (LC 160), Palindrome LL (LC 234).
