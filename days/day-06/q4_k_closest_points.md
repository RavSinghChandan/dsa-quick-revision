# Q4 — K Closest Points to Origin (LeetCode 973)

## 1. What is this question actually asking?

Given a list of 2D points and an integer k, find the k points closest to the origin (0, 0). Distance is measured as Euclidean distance: sqrt(x² + y²). Since we're only comparing distances, we can skip the square root and compare squared distances instead. Return the k points in any order.

## 2. Pattern

Max-Heap of size k — maintain the k closest points; if a new point is closer than the farthest point in the heap, replace it

## 3. Understand with a Diagram

```
Points: [[1,3],[-2,2]]  k=1

Distances²:
  [1,3]:  1+9 = 10
  [-2,2]: 4+4 = 8  ← closer

Answer: [[-2,2]]

Max-heap approach (size k=1):
  Store (-dist², x, y) — negate to turn min-heap into max-heap

  Push (-10, 1, 3): heap=[(-10,1,3)]
  Push (-8, -2, 2): heap size will be 2 > k=1
    But first push, then pop the MAX (which is the FARTHEST):
    Push (-8,-2,2): heap=[(-10,1,3),(-8,-2,2)] size=2>1
    Pop max (least negative, i.e., farthest): pop (-8,-2,2)? 
    
    Wait: max-heap stores (-dist), so the largest value in heap
    is the LEAST negative = the smallest distance?
    
Let me re-clarify:
  Python has min-heap. To simulate max-heap, negate.
  We want to evict the FARTHEST point when heap > k.
  So we want a max-heap by distance.
  Use (-dist², ...) in min-heap → smallest value = most negative = farthest.
  Pop the "max of distances" = pop the top of this negated min-heap.

  Push (-10,1,3): heap=[(-10,1,3)]
  Push (-8,-2,2): heap=[(-10,1,3),(-8,-2,2)]
  Size=2>k=1: pop min of negated = most negative = (-10,1,3)
              That's the FARTHEST point! ✓
  Heap=[(- 8,-2,2)]
  
  Return [(-2,2)] ✓

Another example: points=[[3,3],[5,-1],[-2,4]], k=2

dist²: 18, 26, 20
max-heap (negate for min-heap):
Push (-18,3,3): heap=[(-18,3,3)]
Push (-26,5,-1): heap=[(-26,5,-1),(-18,3,3)], size=2, ok
Push (-20,-2,4): push then size=3>2
  Push: heap=[(-26,5,-1),(-18,3,3),(-20,-2,4)]
  Pop min (most negative = farthest): pop (-26,5,-1)
  heap=[(-20,-2,4),(-18,3,3)]

Return [(3,3), (-2,4)] → the 2 closest ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — Sort all by distance
**Idea:** Compute distance for all points, sort, return first k.

```
points.sort(key=lambda p: p[0]**2 + p[1]**2)
return points[:k]
```

O(n log n) time. Simple and often sufficient.

### Better — QuickSelect
**Idea:** Use partition to find kth smallest distance in O(n) average time.

O(n) average, O(n²) worst case.

### Optimal for this problem — Max-Heap of size k
**Idea:** Maintain a max-heap of size k. For each point, if the heap isn't full yet or the point is closer than the farthest in the heap, add it (and evict the farthest if over capacity).

```
import heapq

heap = []  // max-heap via negated distances

for x, y in points:
    dist_sq = x*x + y*y
    heapq.heappush(heap, (-dist_sq, x, y))
    if len(heap) > k:
        heapq.heappop(heap)  // remove farthest

return [(x, y) for (_, x, y) in heap]
```

O(n log k) time, O(k) space.

## 5. Pseudocode (Optimal)

```
function kClosest(points[], k):
    heap = max-heap (use negated distance for min-heap)
    
    for each point (x, y) in points:
        dist_sq = x*x + y*y
        push (-dist_sq, x, y) to heap
        if len(heap) > k:
            pop from heap  // removes farthest point
    
    return [(x,y) for (_, x, y) in heap]
```

## 6. Python Code

```python
import heapq


def kClosest(points, k):
    heap = []  # max-heap via negation
    
    for x, y in points:
        dist_sq = x * x + y * y
        heapq.heappush(heap, (-dist_sq, x, y))
        if len(heap) > k:
            heapq.heappop(heap)
    
    return [[x, y] for (_, x, y) in heap]


# Alternative: simple sort (good for interviews)
def kClosest_sort(points, k):
    points.sort(key=lambda p: p[0]**2 + p[1]**2)
    return points[:k]


# Test 1 — LeetCode example 1
print(kClosest([[1,3],[-2,2]], 1))        # [[-2,2]]

# Test 2 — LeetCode example 2
result = kClosest([[3,3],[5,-1],[-2,4]], 2)
print(sorted(result))                      # [[-2, 4], [3, 3]]

# Test 3 — k equals total points
print(kClosest([[1,0],[0,1]], 2))          # [[1,0],[0,1]] in any order

# Test 4 — origin in points
print(kClosest([[0,0],[1,1],[2,2]], 1))    # [[0,0]]

# Verify sort version
print(kClosest_sort([[1,3],[-2,2]], 1))   # [[-2,2]]
```

## 7. Complexity Table

| Approach          | Time       | Space | Notes                               |
|-------------------|------------|-------|-------------------------------------|
| Sort              | O(n log n) | O(1)  | Simple, often good enough           |
| QuickSelect       | O(n) avg   | O(1)  | In-place, O(n²) worst               |
| Max-Heap size k   | O(n log k) | O(k)  | Good when k << n                    |

## 8. Edge Cases to Remember

- k = 1 — return the single closest point
- k = n — return all points
- Points at origin (0,0) — distance is 0, always in top k
- Tie in distance — problem says any valid k points is acceptable
- Large coordinates — use squared distance to avoid floating point; Python handles big integers natively
- Single point — return it if k >= 1

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Top K Frequent Elements               | Max-heap of size k for frequencies                 | LC 347     |
| Kth Largest Element                   | Min-heap of size k or QuickSelect                   | LC 215     |
| Find K Pairs with Smallest Sums       | Min-heap for pair sums                              | LC 373     |
| Closest Binary Search Tree Value      | BST property for closest value                      | LC 270     |
| Kth Closest Points in 3D Space        | Same with 3 dimensions                              | —          |
| Maximum Performance of a Team         | Heap + sorting by efficiency                        | LC 1383    |
| Smallest Range Covering K Lists       | Heap-based sliding window                           | LC 632     |
| High Five                             | Top 5 scores per student using heap                 | LC 1086    |

## 10. The ONE Trick to Remember

**"Max-heap of size k — push negated distance, pop when size > k — closest k survive."**

You want to keep the k closest. A max-heap always lets you see and evict the farthest easily. In Python, negate the distance to use the min-heap as a max-heap. When you add a point and the heap grows beyond k, pop the top — which is the most negative in the min-heap = the LARGEST original distance = the FARTHEST point. Only the k closest remain.
