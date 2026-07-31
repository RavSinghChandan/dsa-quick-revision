# ⚡ Quick Note · Day 12 · Q5 · Kth Largest [HEAPS]
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Find the Kth largest element."

## Pattern
`Min-heap of size K (Top-K pattern)`

## Visual in your head
```
keep K biggest in a MIN-heap:
push num; if len>K: pop smallest
heap[0] = Kth largest
```

## The trick (say it out loud)
> "Kth LARGEST → MIN-heap of size K. Evict the smallest each time; root is the answer."

## Code skeleton
```python
h=[]
for x in nums:
    heapq.heappush(h,x)
    if len(h)>k: heapq.heappop(h)
return h[0]
```

## Complexity
- Time O(n log K) · Space O(K)  (Quickselect avg O(n))

## This trick solves more
Top-K Frequent (347) · K Closest Points (973) · Median from stream (295, two heaps) · Merge K lists · Token Top-K
