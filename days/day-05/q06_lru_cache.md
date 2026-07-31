# Day 5 — Stack, Queue & Greedy · Q6 · LRU Cache

---

## What is this question actually asking?

Design a cache with capacity `C` supporting `get(key)` and `put(key, value)` in **O(1)**. When full, evict the **Least Recently Used** entry.

```
put(1,1) put(2,2) get(1)=1 put(3,3)→evicts 2 get(2)=-1 ...
```

> The classic system-design-flavored DSA question. Tests: **hash map + doubly linked list** for O(1) access *and* O(1) recency reorder.

---

## Pattern

```
PATTERN: Hash Map (key → node) + Doubly Linked List (recency order)
```

Whenever you see: *"O(1) get/put with eviction by recency/frequency"* → a hash map gives O(1) lookup; a doubly linked list keeps usage order so you can move a node to the front and evict from the back in O(1).

---

## Understand with a diagram

```
DLL:  head(MRU) ⇄ ... ⇄ tail(LRU)
map:  key → node

get(k):  node = map[k]; move node to front; return value
put(k,v): if exists → update + move front
          else → new node at front; if over capacity → remove tail node + map entry
```

The DLL gives O(1) removal (we have the node) and O(1) insert-at-front.

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — array/list — O(n) per op
Scanning or shifting to reorder recency is O(n).

### 🟢 Optimal — hash map + DLL — O(1)
Map for lookup, DLL for order. In Python, `collections.OrderedDict` (or `dict` + `move_to_end`) gives this for free.

---

## Pseudocode (Optimal)

```
class LRUCache(capacity):
    map = {}
    dll with dummy head & tail

    get(key):
        if key not in map: return -1
        move node to front
        return node.value

    put(key, value):
        if key in map: update value; move to front; return
        if size == capacity: evict tail (remove from dll + map)
        insert new node at front; map[key] = node
```

---

## Python Code

```python
class Node:
    def __init__(self, key=0, val=0):
        self.key, self.val = key, val
        self.prev = self.next = None

class LRUCache:
    def __init__(self, capacity: int):
        self.cap = capacity
        self.map = {}
        self.head = Node()          # MRU sentinel
        self.tail = Node()          # LRU sentinel
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev

    def _add_front(self, node):
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node

    def get(self, key: int) -> int:
        if key not in self.map:
            return -1
        node = self.map[key]
        self._remove(node)
        self._add_front(node)       # mark most-recently used
        return node.val

    def put(self, key: int, value: int) -> None:
        if key in self.map:
            self._remove(self.map[key])
        node = Node(key, value)
        self.map[key] = node
        self._add_front(node)
        if len(self.map) > self.cap:
            lru = self.tail.prev    # evict least-recently used
            self._remove(lru)
            del self.map[lru.key]


# Test
c = LRUCache(2)
c.put(1,1); c.put(2,2)
print(c.get(1))   # 1
c.put(3,3)        # evicts key 2
print(c.get(2))   # -1
c.put(4,4)        # evicts key 1
print(c.get(1))   # -1
print(c.get(3))   # 3
print(c.get(4))   # 4
```

> Pythonic shortcut: subclass `OrderedDict`, use `move_to_end(key)` on access and `popitem(last=False)` to evict.

---

## Complexity

| | Time | Space |
|---|---|---|
| get / put | O(1) | O(capacity) |

---

## Edge Cases to remember

- **Sentinel head & tail** — avoids null checks when adding/removing at the ends.
- **Update existing key** — remove the old node first, then re-add at front (also refreshes recency).
- **Evict AFTER insert** — insert new, then if over capacity remove `tail.prev`.
- **Delete from BOTH** map and DLL on eviction, or you leak.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | LFU Cache (LC 460) | Map + freq buckets of DLLs |
| 2 | Design HashMap (LC 706) | Hashing |
| 3 | Insert Delete GetRandom O(1) (LC 380) | Map + array |
| 4 | All O`one Data Structure (LC 432) | Map + DLL |
| 5 | Design Twitter feed | Map + heap |
| 6 | Time-based key-value store (LC 981) | Map + binary search |
| 7 | Design Browser History | DLL / stack |
| 8 | Snapshot array | Map + versions |

---

## The ONE trick to remember

```
"HASH MAP FOR O(1) LOOKUP + DOUBLY LINKED LIST FOR O(1) RECENCY REORDER."
```

The map finds a node instantly; the DLL lets you unlink it and move it to the front in O(1) (you already hold the node). Evict from the tail. Use sentinel head/tail to avoid edge cases.

> Memory hook: "Map to find it, list to rank it — front is fresh, back gets kicked."

---
