# Day 4 — Linked List · Q2 · Check if Linked List is a Palindrome

---

## What is this question actually asking?

Return whether the linked list reads the same forwards and backwards — ideally in **O(n) time, O(1) space**.

```
1→2→2→1  → True
1→2→3    → False
```

> Tests combining two LL primitives: **find middle (slow/fast)** + **reverse second half**, then compare.

---

## Pattern

```
PATTERN: Slow/Fast to midpoint + reverse second half + two-pointer compare
```

Whenever you see: *"palindrome / compare front-to-back of a list in O(1) space"* → find the middle with slow/fast pointers, reverse the second half in place, walk both halves inward comparing values.

---

## Understand with a diagram

```
1→2→2→1
slow/fast → slow stops at middle (start of 2nd half)

reverse 2nd half:  ...→ 1→2   becomes   2→1  (head2)
compare head vs head2 value by value:
  1==1, 2==2 → palindrome ✓
(optionally re-reverse to restore the list)
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — copy to array — O(n) time, O(n) space
Dump values into a list, check `arr == arr[::-1]`. Simple but O(n) space.

### 🟢 Optimal — mid + reverse half — O(n) time, O(1) space
Find middle, reverse the second half, compare. Restore afterward if the list must stay intact.

---

## Pseudocode (Optimal)

```
function isPalindrome(head):
    # 1. find middle
    slow = fast = head
    while fast and fast.next:
        slow = slow.next; fast = fast.next.next
    # 2. reverse second half
    second = reverse(slow)
    # 3. compare
    p1, p2 = head, second
    ok = True
    while p2:
        if p1.val != p2.val: ok = False; break
        p1 = p1.next; p2 = p2.next
    return ok
```

---

## Python Code

```python
class Node:
    def __init__(self, val, nxt=None):
        self.val = val; self.next = nxt

def is_palindrome(head: Node) -> bool:
    # 1. middle via slow/fast
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

    # 2. reverse the second half
    prev = None
    while slow:
        slow.next, prev, slow = prev, slow, slow.next
    # prev now heads the reversed second half

    # 3. compare halves
    left, right = head, prev
    while right:                       # right (2nd half) is <= 1st half
        if left.val != right.val:
            return False
        left = left.next
        right = right.next
    return True


def build(vals):
    dummy = t = Node(0)
    for v in vals: t.next = Node(v); t = t.next
    return dummy.next

print(is_palindrome(build([1,2,2,1])))   # True
print(is_palindrome(build([1,2,3])))     # False
print(is_palindrome(build([1])))         # True
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Array copy | O(n) | O(n) |
| Mid + reverse | O(n) | O(1) |

---

## Edge Cases to remember

- **Odd length** — the middle node belongs to neither half; comparing only while `right` is non-null handles it (2nd half is ≤ 1st).
- **Single node / empty** → palindrome (True).
- **Restore the list** — if the caller needs it unchanged, re-reverse the second half after comparing.
- **Slow/fast loop** `while fast and fast.next` positions slow correctly for both parities.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Reverse Linked List (LC 206) | The reverse primitive |
| 2 | Middle of Linked List (LC 876) | Slow/fast |
| 3 | Reorder List (LC 143) | Mid + reverse + merge |
| 4 | Fold/zip a linked list | Mid + reverse |
| 5 | Palindrome check of a string | Two pointer |
| 6 | Detect cycle (Q3) | Slow/fast |
| 7 | Delete middle node | Slow/fast |
| 8 | Max twin sum (LC 2130) | Mid + reverse |

---

## The ONE trick to remember

```
"FIND MIDDLE (slow/fast), REVERSE 2ND HALF, COMPARE INWARD."
```

Two O(1)-space primitives combined: slow/fast finds the midpoint, reverse flips the back half, then walk both halves in step comparing values. Re-reverse to restore if needed.

> Memory hook: "Fold the list in half at the middle and see if the halves match."

---
