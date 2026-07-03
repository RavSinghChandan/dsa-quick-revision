# Q3 — Top K Frequent Elements (LeetCode 347)

## 1. What is this question actually asking?

Given an array of integers and an integer k, return the k most frequently occurring elements. The order of the result doesn't matter. For example, in [1,1,1,2,2,3] with k=2, return [1,2] because 1 appears 3 times (most frequent) and 2 appears 2 times (second most frequent).

## 2. Pattern

Min-Heap of size k — count frequencies, maintain a min-heap of size k; the heap always holds the k elements with highest frequencies

## 3. Understand with a Diagram

```
nums = [1,1,1,2,2,3], k=2

Step 1: Count frequencies
  freq = {1:3, 2:2, 3:1}

Step 2: Min-heap of size k=2 (by frequency)
  Heap state: (freq, val)
  
  Process (1,3): heap=[(3,1)] size=1 <= k
  Process (2,2): heap=[(2,2),(3,1)] size=2 <= k
  Process (3,1): heap full! 1 < 2 (min of heap)
    1 < heap.top=2 → pop (2,2), push (1,3)?
    No: we push and then pop the minimum if size > k.
    Push (1,3): heap=[(1,3),(2,2),(3,1)] size=3 > k
    Pop min=(1,3) → heap=[(2,2),(3,1)]
    
  Wait — let me reclarify: we push (frequency, element):
  (3,1): heap=[(3,1)]
  (2,2): heap=[(2,2),(3,1)]  ← min-heap: min is (2,2)
  (1,3): push (1,3) → heap=[(1,3),(3,1),(2,2)]
         size=3 > k=2, pop min=(1,3) → heap=[(2,2),(3,1)]
  
  Final heap: [(2,2), (3,1)]
  Return values: [2, 1] ← top k frequent elements ✓

Min-heap visualization:
After processing all elements, heap contains k elements with highest frequencies.
Min of the heap = smallest among the top k (used to decide evictions).

      (2,2)          ← min, smallest of top-k frequencies
      /
  (3,1)              ← larger frequency, stays in heap
```

## 4. Brute Force → Better → Optimal

### Brute Force — Sort by frequency
**Idea:** Count frequencies, sort elements by frequency, return top k.

```
freq = Counter(nums)
sorted_elements = sorted(freq.keys(), key=lambda x: -freq[x])
return sorted_elements[:k]
```

O(n log n) time. Simple, works, but not optimal.

### Better — Bucket Sort
**Idea:** Create an array of buckets where index = frequency. Each bucket is a list of elements with that frequency. Read from highest bucket downward collecting k elements.

```
freq = Counter(nums)
buckets = [[] for _ in range(len(nums)+1)]
for val, count in freq.items():
    buckets[count].append(val)

result = []
for i in range(len(buckets)-1, 0, -1):
    for val in buckets[i]:
        result.append(val)
        if len(result) == k: return result
```

O(n) time, O(n) space.

### Optimal for interviews — Min-Heap of size k
**Idea:** Count frequencies. Push elements into a min-heap keyed by frequency. When heap exceeds size k, pop the minimum (least frequent). After processing all, the heap contains the k most frequent elements.

```
freq = Counter(nums)
heap = []

for val, count in freq.items():
    heappush(heap, (count, val))
    if len(heap) > k:
        heappop(heap)

return [val for count, val in heap]
```

O(n log k) time, O(n+k) space.

**Trace:**
```
freq = {1:3, 2:2, 3:1}, k=2

(3,1) → heap=[(3,1)], size=1
(2,2) → heap=[(2,2),(3,1)], size=2
(1,3) → push: heap=[(1,3),(3,1),(2,2)], size=3>2, pop min=(1,3)
         heap=[(2,2),(3,1)]

Return [2,1] ✓
```

## 5. Pseudocode (Optimal)

```
function topKFrequent(nums[], k):
    freq = count frequencies of nums
    heap = min-heap []
    
    for (val, count) in freq.items():
        push (count, val) to heap
        if len(heap) > k:
            pop from heap (removes least frequent)
    
    return [val for (count, val) in heap]
```

## 6. Python Code

```python
import heapq
from collections import Counter


def topKFrequent(nums, k):
    freq = Counter(nums)
    heap = []
    
    for val, count in freq.items():
        heapq.heappush(heap, (count, val))
        if len(heap) > k:
            heapq.heappop(heap)
    
    return [val for count, val in heap]


# Bucket sort alternative (O(n)):
def topKFrequent_bucket(nums, k):
    freq = Counter(nums)
    buckets = [[] for _ in range(len(nums) + 1)]
    for val, count in freq.items():
        buckets[count].append(val)
    
    result = []
    for i in range(len(buckets)-1, 0, -1):
        for val in buckets[i]:
            result.append(val)
            if len(result) == k:
                return result
    return result


# Test 1 — LeetCode example 1
print(sorted(topKFrequent([1,1,1,2,2,3], 2)))    # [1, 2]

# Test 2 — LeetCode example 2
print(topKFrequent([1], 1))                       # [1]

# Test 3 — all same frequency
print(sorted(topKFrequent([1,2,3,4,5], 3)))       # any 3 of [1,2,3,4,5]

# Test 4 — k equals unique count
print(sorted(topKFrequent([1,1,2,2,3,3], 3)))     # [1, 2, 3]

# Verify bucket sort gives same result
print(sorted(topKFrequent_bucket([1,1,1,2,2,3], 2)))  # [1, 2]
```

## 7. Complexity Table

| Approach          | Time       | Space  | Notes                               |
|-------------------|------------|--------|-------------------------------------|
| Sort by frequency | O(n log n) | O(n)   | Sort all unique elements            |
| Min-Heap size k   | O(n log k) | O(n+k) | Optimal for large n, small k        |
| Bucket Sort       | O(n)       | O(n)   | Best time, when max freq ≤ n        |

## 8. Edge Cases to Remember

- k = 1 — return the single most frequent element
- k = number of unique elements — return all unique elements
- All elements appear once — any k elements are valid
- Single element array — return [nums[0]]
- Tie in frequency — any valid subset is acceptable (problem guarantees unique answer in LC 347)
- Negative numbers — Counter works fine with negative integers

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| K Closest Points to Origin            | Min-heap/max-heap for k closest                     | LC 973     |
| Kth Largest Element in Array          | Min-heap of size k                                  | LC 215     |
| Sort Characters By Frequency         | Count + sort by frequency                           | LC 451     |
| Top K Frequent Words                  | Same with strings, break ties alphabetically        | LC 692     |
| Find K Pairs with Smallest Sums       | Min-heap with pairs                                 | LC 373     |
| Merge K Sorted Lists                  | Min-heap for merging                                | LC 23      |
| Kth Smallest in Sorted Matrix         | Min-heap or binary search                           | LC 378     |
| Reorganize String                     | Max-heap by frequency to place chars                | LC 767     |

## 10. The ONE Trick to Remember

**"Min-heap of size k — push (frequency, val), pop when size > k — leftover is your answer."**

A min-heap of size k keeps the k largest frequencies. When a new element arrives, push it. If heap size exceeds k, pop the minimum (the smallest frequency among the top-k candidates). At the end, only the k elements with the highest frequencies remain in the heap. This generalizes to any "top k by some metric" problem.
