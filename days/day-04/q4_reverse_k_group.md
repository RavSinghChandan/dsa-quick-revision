# Day 4 — Linked List · Q4 · Reverse Linked List in Groups of K

---

## What is this question actually asking?

Reverse every consecutive block of `k` nodes. If the last block has **fewer than k** nodes, leave it as-is.

```
1→2→3→4→5, k=2 → 2→1→4→3→5
1→2→3→4→5, k=3 → 3→2→1→4→5
```

> The hardest of the "must-know" LL problems. Tests careful pointer surgery: reverse a segment, then re-link segments together.

---

## Pattern

```
PATTERN: Segment reversal with a dummy node + group-tail linking
```

Whenever you see: *"reverse the list in chunks of k"* → for each group, first check that k nodes remain; reverse those k, then connect the previous group's tail to the new head and remember the new tail for the next round.

---

## Understand with a diagram

```
dummy→1→2→3→4→5, k=2

group_prev = dummy
count k ahead: are there 2 nodes? yes → reverse [1,2] → 2→1
  dummy.next = 2 (new head), 1.next = 3 (next group)
  group_prev = 1 (the new tail)
next group [3,4] → reverse → 4→3, link 1→4, 3→5
[5] alone (< k) → leave
result: 2→1→4→3→5
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — copy to array, reverse chunks — O(n) space
Collect values, reverse each block of k in the array, rebuild.

### 🟢 Optimal — in-place group reversal — O(n) time, O(1) space
Use a dummy head. For each group: verify k nodes exist, reverse them by relinking, then stitch to the previous group.

---

## Pseudocode (Optimal)

```
function reverseKGroup(head, k):
    dummy = Node(0); dummy.next = head
    group_prev = dummy
    while True:
        kth = get kth node after group_prev        ← check enough nodes
        if not kth: break
        group_next = kth.next
        # reverse nodes between group_prev.next .. kth
        prev, curr = group_next, group_prev.next
        while curr != group_next:
            nxt = curr.next; curr.next = prev; prev = curr; curr = nxt
        # relink
        new_tail = group_prev.next
        group_prev.next = kth        ← kth is new head of the group
        group_prev = new_tail        ← old head is now the tail
    return dummy.next
```

---

## Python Code

```python
class Node:
    def __init__(self, val, nxt=None):
        self.val = val; self.next = nxt

def reverse_k_group(head: Node, k: int) -> Node:
    dummy = Node(0)
    dummy.next = head
    group_prev = dummy

    def kth_from(node, k):
        while node and k > 0:
            node = node.next
            k -= 1
        return node

    while True:
        kth = kth_from(group_prev, k)
        if not kth:
            break                          # fewer than k left → stop
        group_next = kth.next

        # reverse the k nodes
        prev, curr = group_next, group_prev.next
        while curr is not group_next:
            nxt = curr.next
            curr.next = prev
            prev = curr
            curr = nxt

        # relink: group_prev.next was the head, now becomes the tail
        new_tail = group_prev.next
        group_prev.next = kth
        group_prev = new_tail

    return dummy.next


def build(vals):
    dummy = t = Node(0)
    for v in vals: t.next = Node(v); t = t.next
    return dummy.next
def to_list(h):
    out=[]
    while h: out.append(h.val); h=h.next
    return out

print(to_list(reverse_k_group(build([1,2,3,4,5]), 2)))  # [2,1,4,3,5]
print(to_list(reverse_k_group(build([1,2,3,4,5]), 3)))  # [3,2,1,4,5]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| In-place | O(n) | O(1) |

---

## Edge Cases to remember

- **Last group < k** — leave it unreversed; the `kth_from` returning None is the signal to stop.
- **Dummy head** — simplifies linking the very first group without special-casing.
- **`new_tail` before overwriting** — capture `group_prev.next` (the old head → future tail) before you relink.
- **k = 1** → unchanged list.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Reverse Linked List (LC 206) | Base reversal |
| 2 | Reverse Nodes in k-Group (LC 25) | This exact problem |
| 3 | Reverse Linked List II (LC 92) | Reverse a sub-range |
| 4 | Swap Nodes in Pairs (LC 24) | k = 2 |
| 5 | Rotate List (LC 61) | Pointer relinking |
| 6 | Reorder List (LC 143) | Segment surgery |
| 7 | Reverse in groups (leave remainder) | Same |
| 8 | Odd-even list (LC 328) | Relinking |

---

## The ONE trick to remember

```
"CHECK k NODES EXIST, REVERSE THE SEGMENT, STITCH prev→newHead, prev=oldHead."
```

Use a dummy. Each round: confirm k nodes remain (else stop), reverse them, then link the previous group's tail to the reversed head and set the old head as the next `group_prev` (the tail).

> Memory hook: "Reverse a block, sew it to the previous block, the old front becomes the new back."

---
