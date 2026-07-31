# Day 4 — Linked List · Q1 · Sort a Linked List of 0's, 1's and 2's

---

## What is this question actually asking?

Sort a linked list containing only `0`, `1`, `2` values — **without** collecting into an array.

```
0→1→2→0→2→1→0  →  0→0→0→1→1→2→2
```

> The linked-list version of Dutch Flag. Best done by **splitting into three chains** (0s, 1s, 2s) and stitching them — O(n), one pass.

---

## Pattern

```
PATTERN: Three dummy heads — distribute nodes, then link the chains
```

Whenever you see: *"sort a linked list with a few distinct values"* → make a dummy head per value, append each node to its chain, then connect `zero → one → two`. No random access needed.

---

## Understand with a diagram

```
Walk the list; append each node to its bucket chain:

zeroD → 0 → 0 → 0
oneD  → 1 → 1
twoD  → 2 → 2

Stitch: zero tail → one head ; one tail → two head ; two tail → None
Result: 0→0→0→1→1→2→2
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — count & overwrite — O(n), two passes
Count 0s/1s/2s, then rewrite node values. Works but mutates values, two passes.

### 🟢 Optimal — three chains — O(n), one pass, O(1) extra
Distribute nodes into three lists by pointer, then concatenate. Rearranges links (not values), single pass.

---

## Pseudocode (Optimal)

```
function sort012(head):
    z0 = t0 = dummy0
    z1 = t1 = dummy1
    z2 = t2 = dummy2
    node = head
    while node:
        if node.val == 0: t0.next = node; t0 = node
        elif node.val == 1: t1.next = node; t1 = node
        else: t2.next = node; t2 = node
        node = node.next
    # stitch, skipping empty chains
    t2.next = None
    t1.next = dummy2.next
    t0.next = (dummy1.next or dummy2.next)
    return dummy0.next
```

---

## Python Code

```python
class Node:
    def __init__(self, val, nxt=None):
        self.val = val
        self.next = nxt

def sort_012_list(head: Node) -> Node:
    d0 = t0 = Node(0)
    d1 = t1 = Node(0)
    d2 = t2 = Node(0)

    node = head
    while node:
        if node.val == 0:
            t0.next = node; t0 = node
        elif node.val == 1:
            t1.next = node; t1 = node
        else:
            t2.next = node; t2 = node
        node = node.next

    t2.next = None
    t1.next = d2.next
    t0.next = d1.next if d1.next else d2.next
    return d0.next


# helpers + test
def build(vals):
    dummy = t = Node(0)
    for v in vals: t.next = Node(v); t = t.next
    return dummy.next
def to_list(h):
    out = []
    while h: out.append(h.val); h = h.next
    return out

print(to_list(sort_012_list(build([0,1,2,0,2,1,0]))))  # [0,0,0,1,1,2,2]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Three chains | O(n) | O(1) (rewires existing nodes) |

---

## Edge Cases to remember

- **Empty chains** — if there are no 1s, `t0.next` must skip to the 2s chain; guard with `d1.next if d1.next else d2.next`.
- **Terminate the tail** — set `t2.next = None`, or you create a cycle.
- **Preserve nodes, not values** — the optimal rewires `next` pointers; the count-and-overwrite version changes values instead.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Partition List (LC 86) | Two chains around pivot |
| 2 | Odd Even Linked List (LC 328) | Two chains |
| 3 | Sort array 0/1/2 (Day 1 Q2) | Dutch flag |
| 4 | Segregate even/odd nodes | Two chains |
| 5 | Merge two sorted lists | Stitch chains |
| 6 | Group nodes by key | Bucket chains |
| 7 | Rearrange list by condition | Split + join |
| 8 | Separate positives/negatives | Two chains |

---

## The ONE trick to remember

```
"THREE DUMMY HEADS: DISTRIBUTE NODES, THEN STITCH z→o→t."
```

One pass drops each node into its 0/1/2 chain by pointer. Concatenate the three chains (skipping empty ones) and null-terminate. O(n), O(1), no array.

> Memory hook: "Three buckets, one pass, then tie them end to end."

---
