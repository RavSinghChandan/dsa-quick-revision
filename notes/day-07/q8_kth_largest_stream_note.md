# ⚡ Quick Note · Day 7 — BST & Heap · Q8 · Kth Largest in Stream
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Report Kth largest as numbers stream in."

## Pattern
`Min-heap of fixed size K (streaming Top-K)`

## Visual in your head
```
keep K largest in a MIN-heap
add x: push; if len>K pop smallest
root = Kth largest (O(1))
```

## The trick (say it out loud)
> "Min-heap of size K. Push then pop-if-over-K. Root is the Kth largest. O(log K) per add."

## Code skeleton
```python
def add(val):
    heapq.heappush(heap,val)
    if len(heap)>k: heapq.heappop(heap)
    return heap[0]
```

## Complexity
- add O(log K) · query O(1) · Space O(K)

## This trick solves more
Kth Largest array (Day 12) · Kth Largest Stream (703) · Top K Frequent (347) · Median Stream (295) · K Closest (973)
