# Q5 — LRU Cache (2-min card) — LC 146

**O(1) get and put with LRU eviction when over capacity.**

**Pattern:** HashMap + Doubly Linked List — map for O(1) lookup; DLL for O(1) move-to-head and evict-from-tail

```
capacity=2
put(1,1): head↔[1:1]↔tail
put(2,2): head↔[2:2]↔[1:1]↔tail
get(1):   move 1 to head → head↔[1:1]↔[2:2]↔tail → return 1
put(3,3): capacity exceeded → evict tail.prev=[2:2]
          head↔[3:3]↔[1:1]↔tail
get(2):   not in map → -1
```

**The trick:** "HashMap tells you where the node is; DLL lets you move it to head in O(1) — two structures, one cache."

```python
def get(self, key):
    if key not in self.map: return -1
    self._move_to_head(self.map[key]); return self.map[key].val

def put(self, key, val):
    if key in self.map:
        self.map[key].val = val; self._move_to_head(self.map[key])
    else:
        node = Node(key, val); self.map[key] = node; self._add_to_head(node)
        if len(self.map) > self.capacity:
            lru = self.tail.prev; self._remove(lru); del self.map[lru.key]
```

**Complexity:** get O(1) | put O(1) | Space O(capacity)

**Same pattern solves:** LFU Cache (LC 460), Design HashMap (LC 706), All O'one DS (LC 432), Insert Delete GetRandom (LC 380), Design Twitter (LC 355), Snapshot Array (LC 1146), Time Key-Value Store (LC 981).
