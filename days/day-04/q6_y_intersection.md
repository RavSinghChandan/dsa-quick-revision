# Day 4 — Linked List · Q6 · Intersection Point of Two (Y-Shaped) Linked Lists

---

## What is this question actually asking?

Two lists merge at some node and share the tail (a "Y" shape). Return the **first shared node** (or None).

```
A: 4→1↘
        8→4→5
B: 5→6→1↗
→ intersection is the node with value 8
```

> Tests the elegant **two-pointer switch** trick that cancels out the length difference — O(m+n), O(1).

---

## Pattern

```
PATTERN: Two pointers switching heads to equalize path length
```

Whenever you see: *"find where two lists meet"* → advance `pa` on A then B, `pb` on B then A. Both travel `lenA + lenB` before meeting → they align at the intersection (or both hit None together).

---

## Understand with a diagram

```
lenA = a + c   (c = shared tail length)
lenB = b + c

pa walks A then B: a + c + b
pb walks B then A: b + c + a
→ both have walked (a + b + c) steps → they meet at the FIRST shared node.

If no intersection, both reach None at the same time → return None.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — compare all pairs — O(m·n)
For each node in A, scan B. Slow.

### 🟡 Better — hash set of A's nodes — O(m+n) time, O(m) space
Store A's nodes; first of B found in the set is the answer.

### 🟢 Optimal — two-pointer switch — O(m+n), O(1)
Switch each pointer to the other head at the end; they meet at the intersection.

---

## Pseudocode (Optimal)

```
function intersection(headA, headB):
    if not headA or not headB: return None
    pa, pb = headA, headB
    while pa != pb:
        pa = pa.next if pa else headB       ← switch to B at the end
        pb = pb.next if pb else headA       ← switch to A at the end
    return pa    # either the shared node, or None
```

---

## Python Code

```python
class Node:
    def __init__(self, val, nxt=None):
        self.val = val; self.next = nxt

def get_intersection(headA: Node, headB: Node):
    if not headA or not headB:
        return None
    pa, pb = headA, headB
    while pa is not pb:
        pa = pa.next if pa else headB      # after A's end, jump to B
        pb = pb.next if pb else headA      # after B's end, jump to A
    return pa                              # shared node or None


# Build Y and test
shared = Node(8); shared.next = Node(4); shared.next.next = Node(5)
a = Node(4); a.next = Node(1); a.next.next = shared
b = Node(5); b.next = Node(6); b.next.next = Node(1); b.next.next.next = shared
print(get_intersection(a, b).val)   # 8

x = Node(1); y = Node(2)
print(get_intersection(x, y))       # None
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Hash set | O(m+n) | O(m) |
| Two-pointer switch | O(m+n) | O(1) |

---

## Edge Cases to remember

- **No intersection** — both pointers become None simultaneously → loop ends with `pa is pb is None` → return None. (The `if pa else headB` handles the None-switch.)
- **Compare nodes by identity (`is`)**, not value — different nodes can share a value.
- **Equal-length lists** → meet on the first common node directly.
- **One list empty** → return None early.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Intersection of Two Linked Lists (LC 160) | This exact problem |
| 2 | Cycle start (Q3) | Pointer-math trick |
| 3 | Middle of list (LC 876) | Two pointers |
| 4 | Nth from end | Gap pointers |
| 5 | Merge point of two paths | Length equalize |
| 6 | Compare two lists' tails | Alignment |
| 7 | Find common suffix (arrays) | Two pointer |
| 8 | Align two sequences | Offset by length diff |

---

## The ONE trick to remember

```
"SWITCH HEADS: pa→A then B, pb→B then A. THEY MEET AT THE INTERSECTION."
```

By having each pointer traverse both lists, both cover `lenA + lenB` steps and cancel the length difference — meeting exactly at the first shared node, or at None together if there's no intersection.

> Memory hook: "Walk your road then theirs; they walk theirs then yours — you'll meet at the fork."

---
