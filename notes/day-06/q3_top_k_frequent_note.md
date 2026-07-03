# Q3 — Top K Frequent Elements (2-min card) — LC 347

**Return the k most frequently occurring elements from an array.**

**Pattern:** Min-Heap of size k — maintain k-largest by frequency; pop minimum when heap exceeds k

```
nums=[1,1,1,2,2,3]  k=2
freq={1:3, 2:2, 3:1}

push (3,1): heap=[(3,1)]
push (2,2): heap=[(2,2),(3,1)]
push (1,3): heap size=3>2 → pop min=(1,3) → heap=[(2,2),(3,1)]

Result: [2, 1] ✓  (the 2 most frequent)
```

**The trick:** "Min-heap of size k — push frequency, pop when overflow; survivors are top-k."

```python
import heapq
from collections import Counter
freq = Counter(nums); heap = []
for val, cnt in freq.items():
    heapq.heappush(heap, (cnt, val))
    if len(heap) > k: heapq.heappop(heap)
return [val for cnt, val in heap]
```

**Complexity:** Time O(n log k) | Space O(n+k)  [Bucket sort gives O(n)]

**Same pattern solves:** K Closest Points (LC 973), Kth Largest (LC 215), Top K Words (LC 692), Sort by Frequency (LC 451), Find K Pairs Smallest Sums (LC 373), Merge K Sorted Lists (LC 23), Reorganize String (LC 767).
