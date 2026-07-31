# Day 4 — Linked List · Q5 · Flatten a Linked List

---

## What is this question actually asking?

Each node has a `next` pointer (to the right) and a `bottom` pointer (to a sorted sub-list below). Every sub-list is sorted. **Flatten** everything into one sorted `bottom`-linked list.

```
5 → 10 → 19 → 28
|    |    |    |
7    20   22   35
|         |    |
8         50   40
|              |
30             45

→ 5→7→8→10→19→20→22→28→30→35→40→45→50   (via bottom)
```

> Tests **merging sorted lists** recursively down the `next` chain — like merge-K-lists, but pairwise from the right.

---

## Pattern

```
PATTERN: Recursively merge sorted sub-lists (right to left)
```

Whenever you see: *"flatten a multilevel sorted structure into one sorted list"* → merge two sorted `bottom`-lists at a time. Recurse to flatten `next` first, then merge the current column into it.

---

## Understand with a diagram

```
Flatten from the RIGHT:
  flatten(head) = merge(head, flatten(head.next))

merge(a, b): standard two-pointer merge on the BOTTOM pointer,
             producing one sorted vertical chain.

So the rightmost column merges leftward into a single sorted bottom-list.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — collect all, sort, rebuild — O(N log N)
Gather every value, sort, relink. Ignores that columns are already sorted.

### 🟢 Optimal — pairwise merge of sorted lists — O(N × K)
Since each column is sorted, merge them like merge-two-sorted-lists. Recurse right, merge left. (N nodes, K columns.)

---

## Pseudocode (Optimal)

```
function flatten(head):
    if head is None or head.next is None: return head
    head.next = flatten(head.next)         ← flatten the rest first
    head = merge(head, head.next)          ← merge this column into it
    return head

function merge(a, b):        # merge on BOTTOM pointer, both sorted
    dummy = Node(0); tail = dummy
    while a and b:
        if a.val <= b.val: tail.bottom = a; a = a.bottom
        else: tail.bottom = b; b = b.bottom
        tail = tail.bottom
    tail.bottom = a if a else b
    return dummy.bottom
```

---

## Python Code

```python
class Node:
    def __init__(self, val, nxt=None, bottom=None):
        self.val = val
        self.next = nxt
        self.bottom = bottom

def merge(a: Node, b: Node) -> Node:
    dummy = Node(0)
    tail = dummy
    while a and b:
        if a.val <= b.val:
            tail.bottom = a; a = a.bottom
        else:
            tail.bottom = b; b = b.bottom
        tail = tail.bottom
    tail.bottom = a if a else b
    return dummy.bottom

def flatten(head: Node) -> Node:
    if head is None or head.next is None:
        return head
    head.next = flatten(head.next)     # flatten everything to the right
    head = merge(head, head.next)      # merge current column into it
    return head


# Build the example and test (bottom chain)
def col(vals):
    dummy = Node(0)
    t = dummy
    for v in vals:
        t.bottom = Node(v); t = t.bottom
    return dummy.bottom

c1 = col([5,7,8,30]); c2 = col([10,20]); c3 = col([19,22,50]); c4 = col([28,35,40,45])
c1.next, c2.next, c3.next = c2, c3, c4
res = flatten(c1)
out = []
while res:
    out.append(res.val); res = res.bottom
print(out)   # [5,7,8,10,19,20,22,28,30,35,40,45,50]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Recursive merge | O(N × K) | O(K) recursion depth |

(N = total nodes, K = number of columns)

---

## Edge Cases to remember

- **Merge on `bottom`, not `next`** — the flattened list uses the `bottom` pointer; `next` is consumed.
- **Recurse right first**, then merge — so you always merge into an already-flattened tail.
- **Single column / single node** → return as-is (base case).
- **Null-terminate** — the final `tail.bottom` picks up the non-empty remainder.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Merge Two Sorted Lists (LC 21) | The merge primitive |
| 2 | Merge K Sorted Lists (LC 23) | K-way merge |
| 3 | Flatten Multilevel Doubly List (LC 430) | DFS flatten |
| 4 | Sort a Linked List (merge sort, LC 148) | Merge |
| 5 | Merge sorted arrays (Day 12 Q6) | Merge mechanics |
| 6 | Flatten nested list (Day 11 Q2) | Recursion |
| 7 | Merge intervals after flatten | Merge |
| 8 | Combine sorted streams | Pairwise merge |

---

## The ONE trick to remember

```
"flatten(head) = merge(head, flatten(head.next)) — MERGE ON BOTTOM."
```

Recurse to flatten everything to the right into one sorted `bottom`-list, then merge the current column into it. Because every column is sorted, it's just repeated merge-two-sorted-lists.

> Memory hook: "Flatten the right, then zip this column into it — merge, merge, merge."

---
