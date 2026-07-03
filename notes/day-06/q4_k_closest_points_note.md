# Q4 — K Closest Points to Origin (2-min card) — LC 973

**Find k points closest to (0,0). Distance = sqrt(x²+y²), compare x²+y² to skip sqrt.**

**Pattern:** Max-Heap of size k — keep k closest; evict farthest when heap overflows

```
points=[[3,3],[5,-1],[-2,4]]  k=2
dist²:   18     26      20

push (-18,3,3):  heap=[(-18,3,3)]
push (-26,5,-1): heap=[(-26,5,-1),(-18,3,3)]
push (-20,-2,4): size=3>2 → pop min=(-26,5,-1) [farthest!]
                 heap=[(-20,-2,4),(-18,3,3)]

Return [[3,3],[-2,4]] ✓
```

**The trick:** "Negate distance → min-heap acts as max-heap; pop removes farthest; k closest survive."

```python
import heapq
heap = []
for x, y in points:
    heapq.heappush(heap, (-(x*x+y*y), x, y))
    if len(heap) > k: heapq.heappop(heap)
return [[x,y] for _,x,y in heap]
```

**Complexity:** Time O(n log k) | Space O(k)

**Same pattern solves:** Top K Frequent (LC 347), Kth Largest (LC 215), K Pairs Smallest Sums (LC 373), Max Performance Team (LC 1383), Smallest Range K Lists (LC 632), High Five (LC 1086), Closest BST Value (LC 270).
