# Q5 — LRU Cache (LeetCode 146)

## 1. What is this question actually asking?

Design a data structure for a Least Recently Used (LRU) cache. It has a fixed capacity. Support two operations: get(key) — return the value if key exists, -1 otherwise; and put(key, value) — insert or update the key-value pair. If inserting a new key would exceed capacity, evict the least recently used key first. Both operations must run in O(1). "Recently used" means either accessed via get or updated/inserted via put.

## 2. Pattern

HashMap + Doubly Linked List — O(1) lookup via hash map, O(1) insertion/deletion at any position via doubly linked list; most recent at head, least recent at tail

## 3. Understand with a Diagram

```
Capacity = 2

Initial: dummy_head ↔ dummy_tail

put(1,1): Insert node(1,1) at head
  dummy_head ↔ [1:1] ↔ dummy_tail
  map: {1: node(1,1)}

put(2,2): Insert node(2,2) at head
  dummy_head ↔ [2:2] ↔ [1:1] ↔ dummy_tail
  map: {1: node(1,1), 2: node(2,2)}

get(1):   Key 1 found → move node(1,1) to head → return 1
  dummy_head ↔ [1:1] ↔ [2:2] ↔ dummy_tail
  (1 is now most recently used)

put(3,3): Capacity exceeded → evict LRU (tail = node 2)
  Remove node(2,2), insert node(3,3) at head
  dummy_head ↔ [3:3] ↔ [1:1] ↔ dummy_tail
  map: {1: node(1,1), 3: node(3,3)}

get(2):   Key 2 not in map → return -1

Structure:
  HashMap:    key → node reference (O(1) lookup)
  DLL:        head=most recent, tail=least recent
              constant-time move-to-head (just relink neighbors)
              constant-time evict (remove tail.prev)

Doubly Linked List node:
  [prev | key | val | next]

Dummy nodes simplify edge cases:
  head.next = most recently used
  tail.prev = least recently used (to evict)
```

## 4. Brute Force → Better → Optimal

### Brute Force — Array/List + tracking
**Idea:** Maintain a list of (key, value) pairs. On access, mark as recently used. On eviction, find and remove the oldest.

```
on get(key): scan list for key O(n), move to end O(n)
on put(key,val): scan for key O(n), add/update, evict first if needed
```

Why slow: O(n) per operation.

### Better — OrderedDict (Python shortcut)
**Idea:** Python's OrderedDict maintains insertion order. Move to end on access, pop oldest on eviction.

```
from collections import OrderedDict

get: move_to_end; return value
put: move_to_end or insert; if over capacity, pop first item
```

O(1) per operation. Valid for interviews but uses a built-in abstraction.

### Optimal — Manual HashMap + Doubly Linked List
**Idea:** HashMap maps keys to DLL nodes for O(1) lookup. DLL gives O(1) insertion at head and removal from any position (with node reference). Most-recent = head, least-recent = tail.

```
class Node: key, val, prev, next
class LRUCache:
    capacity
    map: key → Node
    head, tail: dummy boundary nodes (head.next = MRU, tail.prev = LRU)

get(key):
    if key not in map: return -1
    move_to_head(map[key])
    return map[key].val

put(key, val):
    if key in map:
        map[key].val = val
        move_to_head(map[key])
    else:
        node = Node(key, val)
        map[key] = node
        add_to_head(node)
        if len(map) > capacity:
            lru = tail.prev
            remove(lru)
            del map[lru.key]

add_to_head(node):
    node.next = head.next
    node.prev = head
    head.next.prev = node
    head.next = node

remove(node):
    node.prev.next = node.next
    node.next.prev = node.prev

move_to_head(node):
    remove(node)
    add_to_head(node)
```

## 5. Pseudocode (Optimal)

```
class LRUCache(capacity):
    map = {}
    head = dummy node
    tail = dummy node
    head.next = tail
    tail.prev = head
    
    get(key):
        if key not in map: return -1
        move_to_head(map[key])
        return map[key].val
    
    put(key, val):
        if key in map:
            map[key].val = val
            move_to_head(map[key])
        else:
            node = new Node(key, val)
            map[key] = node
            add_to_head(node)
            if len(map) > capacity:
                lru = tail.prev
                remove(lru)
                del map[lru.key]
    
    add_to_head(node):
        node.next = head.next; node.prev = head
        head.next.prev = node; head.next = node
    
    remove(node):
        node.prev.next = node.next; node.next.prev = node.prev
    
    move_to_head(node):
        remove(node); add_to_head(node)
```

## 6. Python Code

```python
class Node:
    def __init__(self, key=0, val=0):
        self.key = key
        self.val = val
        self.prev = self.next = None


class LRUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.map = {}
        self.head = Node()  # dummy
        self.tail = Node()  # dummy
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def _add_to_head(self, node):
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node
    
    def _remove(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev
    
    def _move_to_head(self, node):
        self._remove(node)
        self._add_to_head(node)
    
    def get(self, key):
        if key not in self.map:
            return -1
        self._move_to_head(self.map[key])
        return self.map[key].val
    
    def put(self, key, val):
        if key in self.map:
            self.map[key].val = val
            self._move_to_head(self.map[key])
        else:
            node = Node(key, val)
            self.map[key] = node
            self._add_to_head(node)
            if len(self.map) > self.capacity:
                lru = self.tail.prev
                self._remove(lru)
                del self.map[lru.key]


# Test 1 — LeetCode example
cache = LRUCache(2)
cache.put(1, 1)
cache.put(2, 2)
print(cache.get(1))    # 1
cache.put(3, 3)        # evicts 2
print(cache.get(2))    # -1
cache.put(4, 4)        # evicts 1
print(cache.get(1))    # -1
print(cache.get(3))    # 3
print(cache.get(4))    # 4

# Test 2 — capacity 1
cache2 = LRUCache(1)
cache2.put(1, 1)
print(cache2.get(1))   # 1
cache2.put(2, 2)
print(cache2.get(1))   # -1 (evicted)
print(cache2.get(2))   # 2
```

## 7. Complexity Table

| Approach                | get    | put    | Space | Notes                        |
|-------------------------|--------|--------|-------|------------------------------|
| Array + scan            | O(n)   | O(n)   | O(n)  | Linear search                |
| OrderedDict             | O(1)   | O(1)   | O(n)  | Python built-in              |
| HashMap + DLL (optimal) | O(1)   | O(1)   | O(n)  | Manual, fully explicit       |

## 8. Edge Cases to Remember

- get on nonexistent key — return -1
- put with key that already exists — update value, move to head (don't add new node)
- Capacity 1 — every put evicts the previous entry (unless same key)
- Eviction targets tail.prev, not tail (tail is dummy)
- After removal, delete from both the DLL and the hashmap
- move_to_head = remove + add_to_head — two separate operations

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| LFU Cache                             | Least Frequently Used — need frequency tracking     | LC 460     |
| Design HashMap                        | Basic hashmap without collision handling tricks     | LC 706     |
| Design Linked List                    | DLL implementation practice                         | LC 707     |
| All O`one Data Structure              | O(1) inc/dec/max/min — DLL + HashMap                | LC 432     |
| Insert Delete GetRandom O(1)          | HashMap + array for O(1) random access              | LC 380     |
| Time Based Key-Value Store            | HashMap with sorted version history                 | LC 981     |
| Design Twitter                        | HashMap + ordered timeline                          | LC 355     |
| Snapshot Array                        | HashMap per key for version history                 | LC 1146    |

## 10. The ONE Trick to Remember

**"HashMap gives you O(1) lookup; doubly linked list gives you O(1) move-to-front and evict-from-tail."**

You need two data structures working together: the hash map tells you where the node is in O(1), and the doubly linked list lets you reorder nodes in O(1) because you have direct pointers to neighbors. Dummy head and tail eliminate all edge cases for empty list or single element. Always think: "node reference in map → find in O(1), then DLL ops in O(1)."
