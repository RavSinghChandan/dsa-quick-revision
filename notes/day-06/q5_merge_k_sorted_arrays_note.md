# Q5 — Merge K Sorted Arrays (2-min card)

**Merge k sorted arrays into one sorted array efficiently.**

**Pattern:** Min-Heap — one pointer per array in heap; always extract global minimum, advance that pointer

```
A0=[1,4,7]  A1=[2,5,8]  A2=[3,6,9]

heap=[(1,0,0),(2,1,0),(3,2,0)]

pop (1,0,0)→result=[1], push (4,0,1)
pop (2,1,0)→result=[1,2], push (5,1,1)
pop (3,2,0)→result=[1,2,3], push (6,2,1)
...continues...

Result: [1,2,3,4,5,6,7,8,9] ✓
Heap size ≤ k at all times
```

**The trick:** "Heap holds one element per array — pop global min, push next from same array."

```python
import heapq
heap = [(arr[0],i,0) for i,arr in enumerate(arrays) if arr]
heapq.heapify(heap); result = []
while heap:
    val, ai, ei = heapq.heappop(heap); result.append(val)
    if ei+1 < len(arrays[ai]):
        heapq.heappush(heap, (arrays[ai][ei+1], ai, ei+1))
return result
```

**Complexity:** Time O(N log k) | Space O(k)  [N=total elements]

**Same pattern solves:** Merge K Sorted Lists (LC 23), Find Median Stream (LC 295), Smallest Range K Lists (LC 632), Kth Smallest Matrix (LC 378), K Pairs Smallest Sums (LC 373), Ugly Number II (LC 264), Merge Two Sorted Arrays (LC 88).
