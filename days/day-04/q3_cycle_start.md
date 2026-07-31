# Day 4 — Linked List · Q3 · Find the Starting Point of a Cycle

---

## What is this question actually asking?

If the linked list has a cycle, return the **node where the cycle begins**; else `None`.

```
3→2→0→-4→(back to 2)   → returns the node with value 2
```

> Floyd's Tortoise & Hare, part two. Tests the neat math: after the pointers meet, moving one pointer to the head and stepping both by one lands them at the cycle's start.

---

## Pattern

```
PATTERN: Floyd's Cycle Detection (slow/fast) + head-reset to find entry
```

Whenever you see: *"find where a cycle starts / detect loop entry"* → slow (1 step) and fast (2 steps) meet inside the loop; then move one pointer back to `head` and advance both by 1 — they meet at the cycle start.

---

## Understand with a diagram

```
head ──L──▶ START ──▶ ... ──▶ (loop of length C)
slow & fast meet at some point inside the loop.

Math: distance head→START = L.  At meeting, slow travelled L + a.
It turns out: L ≡ (C − a) mod C.  So:
  reset one pointer to head; step both by 1;
  after L steps, both stand at START.

3→2→0→-4→(2)   meet inside loop → reset → both reach node '2' = start
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — hash set of visited — O(n) time, O(n) space
Walk, storing visited nodes; first repeat is the start.

### 🟢 Optimal — Floyd's — O(n) time, O(1) space
Detect meeting with slow/fast; reset one to head; step both by 1 to the entry.

---

## Pseudocode (Optimal)

```
function cycle_start(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:                 ← cycle detected
            p = head
            while p != slow:
                p = p.next; slow = slow.next
            return p                      ← the start node
    return None                          ← no cycle
```

---

## Python Code

```python
class Node:
    def __init__(self, val, nxt=None):
        self.val = val; self.next = nxt

def detect_cycle_start(head: Node):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:                  # meeting point inside loop
            p = head
            while p is not slow:
                p = p.next
                slow = slow.next
            return p                      # cycle entry
    return None                           # no cycle


# Test
a = Node(3); b = Node(2); c = Node(0); d = Node(-4)
a.next, b.next, c.next, d.next = b, c, d, b   # -4 loops back to b (value 2)
print(detect_cycle_start(a).val)   # 2

x = Node(1); x.next = Node(2)
print(detect_cycle_start(x))       # None
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Hash set | O(n) | O(n) |
| Floyd's | O(n) | O(1) |

---

## Edge Cases to remember

- **No cycle** → the `while fast and fast.next` loop exits → return None.
- **Cycle to head** — start is the head itself; the reset loop returns immediately.
- **`is` identity, not `==` value** — compare nodes, not values (values may repeat).
- **Single node self-loop** → detected; start is that node.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Linked List Cycle (LC 141) | Detect only |
| 2 | Find the Duplicate Number (LC 287) | Cycle on index mapping |
| 3 | Happy Number (LC 202) | Cycle in sequence |
| 4 | Middle of list (LC 876) | Slow/fast |
| 5 | Intersection of two lists (Q6) | Pointer math |
| 6 | Detect cycle length | Count in loop |
| 7 | Circular array loop (LC 457) | Floyd on array |
| 8 | Palindrome LL (Q2) | Slow/fast |

---

## The ONE trick to remember

```
"MEET WITH slow/fast, RESET ONE TO HEAD, STEP BOTH BY 1 → CYCLE START."
```

Slow and fast meet inside the loop. The distance from head to the start equals the distance from the meeting point to the start (mod loop length) — so resetting one pointer to head and stepping both by one converges at the entry.

> Memory hook: "They meet in the loop; from head, walk together to where the loop begins."

---
