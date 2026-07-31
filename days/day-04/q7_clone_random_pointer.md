# Day 4 — Linked List · Q7 · Clone a Linked List with Random Pointer

---

## What is this question actually asking?

Each node has `next` **and** a `random` pointer (to any node or null). Return a **deep copy** of the whole list.

```
1→2→3 with randoms pointing anywhere → an identical, independent copy
```

> Tests: how to copy `random` pointers correctly. The O(1)-space answer is the clever **interleaving** trick.

---

## Pattern

```
PATTERN: Interleave clone nodes (A→A'→B→B'), set randoms, then split
```

Whenever you see: *"deep copy with arbitrary/random pointers"* → either a **hash map** {original → copy}, or the O(1) trick: weave each copy right after its original so `orig.next.random = orig.random.next`.

---

## Understand with a diagram

```
Step 1 — interleave:  1 → 1' → 2 → 2' → 3 → 3'
         (each copy sits right after its original)

Step 2 — set randoms:  copy.random = orig.random.next
         because orig.random.next IS the clone of orig.random

Step 3 — split back into two lists: originals and clones
```

---

## Brute Force → Better → Optimal

### 🟡 Approach A — Hash map — O(n) time, O(n) space
Pass 1: create clone of each node, map `{orig: clone}`. Pass 2: set `clone.next = map[orig.next]`, `clone.random = map[orig.random]`. Simplest to write.

### 🟢 Approach B — Interleaving — O(n) time, O(1) space
Weave clones between originals; set randoms via `orig.next.random = orig.random.next`; unweave. No extra map.

---

## Pseudocode (Optimal — interleave)

```
function clone(head):
    if not head: return None
    # 1. interleave copies
    cur = head
    while cur:
        copy = Node(cur.val); copy.next = cur.next; cur.next = copy; cur = copy.next
    # 2. assign randoms
    cur = head
    while cur:
        if cur.random: cur.next.random = cur.random.next
        cur = cur.next.next
    # 3. split
    cur = head; clone_head = head.next
    while cur:
        copy = cur.next
        cur.next = copy.next
        copy.next = copy.next.next if copy.next else None
        cur = cur.next
    return clone_head
```

---

## Python Code

```python
class Node:
    def __init__(self, val, nxt=None, random=None):
        self.val = val; self.next = nxt; self.random = random

def clone_with_random(head: Node) -> Node:
    if not head:
        return None

    # 1. Interleave: A -> A' -> B -> B' ...
    cur = head
    while cur:
        copy = Node(cur.val)
        copy.next = cur.next
        cur.next = copy
        cur = copy.next

    # 2. Set random pointers on the clones
    cur = head
    while cur:
        if cur.random:
            cur.next.random = cur.random.next   # clone of cur.random
        cur = cur.next.next

    # 3. Detangle the two lists
    cur = head
    clone_head = head.next
    while cur:
        copy = cur.next
        cur.next = copy.next
        copy.next = copy.next.next if copy.next else None
        cur = cur.next
    return clone_head


# Test (verify structure via hash-map identity)
a, b, c = Node(1), Node(2), Node(3)
a.next, b.next = b, c
a.random, b.random, c.random = c, a, b   # randoms
cl = clone_with_random(a)
vals, rnds = [], []
n = cl
while n:
    vals.append(n.val)
    rnds.append(n.random.val if n.random else None)
    n = n.next
print(vals)   # [1, 2, 3]
print(rnds)   # [3, 1, 2]   (randoms preserved, independent copy)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Hash map | O(n) | O(n) |
| Interleaving | O(n) | O(1) |

---

## Edge Cases to remember

- **`random` can be null** — guard `if cur.random` before dereferencing.
- **Detangle carefully** — restore the original list's `next` too, or you leave both lists corrupted.
- **`orig.random.next` is the clone** — this identity only holds *because* clones are interleaved right after originals.
- **Empty list** → return None.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Copy List with Random Pointer (LC 138) | This exact problem |
| 2 | Clone a Graph (LC 133) | Hash map {orig: copy} |
| 3 | Deep copy a tree with parent ptrs | Map or interleave |
| 4 | Copy binary tree with random node | Same idea |
| 5 | Clone an N-ary tree | DFS + map |
| 6 | Serialize/deserialize with refs | Map ids |
| 7 | Duplicate DAG | Map |
| 8 | Copy doubly linked list | Interleave |

---

## The ONE trick to remember

```
"INTERLEAVE clone AFTER original → clone.random = orig.random.next → SPLIT."
```

Weaving each copy right after its original makes `orig.random.next` the clone of `orig.random` — so random pointers copy in one pass with no map. Then detangle the two lists (restoring both). Hash-map version is O(n) space but simpler.

> Memory hook: "Shadow each node with its twin, copy the randoms via the twin, then unzip."

---
