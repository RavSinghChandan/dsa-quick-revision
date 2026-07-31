# ⚡ Quick Note · Day 12 · Q6 · Merge K Sorted [HEAPS]
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Merge K sorted lists/streams into one sorted list."

## Pattern
`Min-heap of one 'head' per list (k-way merge)`

## Visual in your head
```
heap = (head of each list) as (val, list_i, idx)
pop smallest → output → push next from that list
heap size stays K → O(K) memory
```

## The trick (say it out loud)
> "Heap holds one head per list. Pop the smallest, push its successor from the same list."

## Code skeleton
```python
for i,l in enumerate(lists):
    if l: heapq.heappush(h,(l[0],i,0))
val,i,j=heapq.heappop(h)
if j+1<len(lists[i]): heapq.heappush(h,(lists[i][j+1],i,j+1))
```

## Complexity
- Time O(N log K) · Space O(K)

## This trick solves more
Merge K Lists (23) · Smallest Range K Lists (632) · Kth Smallest Matrix (378) · External sort · Combine retrievers
