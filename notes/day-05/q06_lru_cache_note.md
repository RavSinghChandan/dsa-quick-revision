# ⚡ Quick Note · Day 5 — Stack & Greedy · Q6 · LRU Cache
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "O(1) get/put cache; evict least-recently-used when full."

## Pattern
`Hash map (key→node) + Doubly Linked List (recency)`

## Visual in your head
```
head(MRU) ⇄ ... ⇄ tail(LRU)
get: move node to front
put: add front; over cap → remove tail.prev + map entry
```

## The trick (say it out loud)
> "Map for O(1) lookup, DLL for O(1) reorder. Front = fresh, tail = evict. Sentinel head/tail."

## Code skeleton
```python
get: node=map[k]; remove(node); add_front(node); return node.val
put: if k in map: remove; add_front(new); map[k]=new
     if len>cap: lru=tail.prev; remove(lru); del map[lru.key]
```

## Complexity
- get/put O(1) · Space O(capacity)

## This trick solves more
LFU (460) · GetRandom O(1) (380) · All O`one (432) · Time KV store (981) · Browser History
(Pythonic: OrderedDict.move_to_end / popitem(last=False))
