# Q5 — Merge K Sorted Arrays

## 1. What is this question actually asking?

You have k sorted arrays (each sorted in ascending order). Merge all of them into a single sorted array. For example, [[1,4,7],[2,5,8],[3,6,9]] should become [1,2,3,4,5,6,7,8,9]. You need to do this efficiently — not just concatenate and sort.

## 2. Pattern

Min-Heap — always extract the smallest current element from all arrays by maintaining one element from each array in a heap

## 3. Understand with a Diagram

```
Arrays:
  A0: [1, 4, 7]
  A1: [2, 5, 8]
  A2: [3, 6, 9]

Initialize min-heap with first element of each array:
  heap = [(1, 0, 0), (2, 1, 0), (3, 2, 0)]
  each entry = (value, array_index, element_index)

Heap state (min at top):
       (1,0,0)
      /        \
  (2,1,0)  (3,2,0)

Step 1: pop (1,0,0) → result=[1]
  Next from A0: (4,0,1) → push
  heap=[(2,1,0),(4,0,1),(3,2,0)]

Step 2: pop (2,1,0) → result=[1,2]
  Next from A1: (5,1,1) → push
  heap=[(3,2,0),(4,0,1),(5,1,1)]

Step 3: pop (3,2,0) → result=[1,2,3]
  Next from A2: (6,2,1) → push
  heap=[(4,0,1),(5,1,1),(6,2,1)]

Step 4: pop (4,0,1) → result=[1,2,3,4]
  Next from A0: (7,0,2) → push

Step 5: pop (5,1,1) → result=[1,2,3,4,5]
  Next from A1: (8,1,2)

Step 6: pop (6,2,1) → result=[1,2,3,4,5,6]
  Next from A2: (9,2,2)

Step 7: pop (7,0,2) → result=[...,7], A0 exhausted
Step 8: pop (8,1,2) → result=[...,8], A1 exhausted
Step 9: pop (9,2,2) → result=[...,9], A2 exhausted
heap empty → done!

Result: [1,2,3,4,5,6,7,8,9] ✓

Heap always has at most k elements (one per array).
```

## 4. Brute Force → Better → Optimal

### Brute Force — Concatenate and sort
**Idea:** Flatten all arrays into one, sort it.

```
result = []
for arr in arrays:
    result.extend(arr)
result.sort()
return result
```

O(N log N) where N = total elements. Simple but doesn't leverage the sorted structure.

### Better — Sequential merge
**Idea:** Merge arrays pairwise, like merge sort. Merge first two, then merge result with third, etc.

```
result = arrays[0]
for i from 1 to k-1:
    result = merge(result, arrays[i])
```

Each merge is O(current_size + arrays[i].size). Total O(N*k) in worst case.

### Optimal — Min-Heap
**Idea:** Initialize heap with (first_value, array_index, element_index) for each array. Each time you extract the minimum, push the next element from the same array.

```
heap = []
for i, arr in enumerate(arrays):
    if arr: heappush(heap, (arr[0], i, 0))

result = []
while heap:
    val, arr_i, elem_i = heappop(heap)
    result.append(val)
    if elem_i + 1 < len(arrays[arr_i]):
        next_val = arrays[arr_i][elem_i + 1]
        heappush(heap, (next_val, arr_i, elem_i + 1))

return result
```

O(N log k) time, O(k) extra space (heap size ≤ k).

## 5. Pseudocode (Optimal)

```
function mergeKSortedArrays(arrays[][]):
    heap = min-heap
    
    for i from 0 to k-1:
        if arrays[i] not empty:
            push (arrays[i][0], i, 0) to heap
    
    result = []
    
    while heap not empty:
        (val, arr_idx, elem_idx) = pop from heap
        result.append(val)
        
        next_idx = elem_idx + 1
        if next_idx < len(arrays[arr_idx]):
            push (arrays[arr_idx][next_idx], arr_idx, next_idx) to heap
    
    return result
```

## 6. Python Code

```python
import heapq


def merge_k_sorted_arrays(arrays):
    heap = []
    
    # Initialize with first element from each array
    for i, arr in enumerate(arrays):
        if arr:
            heapq.heappush(heap, (arr[0], i, 0))
    
    result = []
    
    while heap:
        val, arr_i, elem_i = heapq.heappop(heap)
        result.append(val)
        
        next_i = elem_i + 1
        if next_i < len(arrays[arr_i]):
            heapq.heappush(heap, (arrays[arr_i][next_i], arr_i, next_i))
    
    return result


# Test 1 — standard example
print(merge_k_sorted_arrays([[1,4,7],[2,5,8],[3,6,9]]))
# [1, 2, 3, 4, 5, 6, 7, 8, 9]

# Test 2 — different lengths
print(merge_k_sorted_arrays([[1,2],[3,4,5,6],[7]]))
# [1, 2, 3, 4, 5, 6, 7]

# Test 3 — k=1
print(merge_k_sorted_arrays([[1,2,3]]))
# [1, 2, 3]

# Test 4 — empty arrays mixed in
print(merge_k_sorted_arrays([[1,3],[],[2,4]]))
# [1, 2, 3, 4]

# Test 5 — single elements
print(merge_k_sorted_arrays([[5],[1],[3],[2],[4]]))
# [1, 2, 3, 4, 5]

# Test 6 — overlapping values
print(merge_k_sorted_arrays([[1,1,1],[1,1,1]]))
# [1, 1, 1, 1, 1, 1]
```

## 7. Complexity Table

| Approach          | Time       | Space  | Notes                               |
|-------------------|------------|--------|-------------------------------------|
| Concatenate+sort  | O(N log N) | O(N)   | N = total elements                  |
| Sequential merge  | O(N*k)     | O(N)   | k merges, each grows in size        |
| Min-Heap          | O(N log k) | O(k)   | Heap holds k elements at most       |

## 8. Edge Cases to Remember

- Empty array list — return []
- Some arrays are empty — skip them during initialization
- All arrays have single element — works fine
- k = 1 — heap has one entry, degenerates to just returning that array
- Arrays with duplicate values — tuples (val, arr_i, elem_i) are compared left to right; arr_i breaks ties if values are equal (deterministic)
- Very large k — heap operations are still O(log k)

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Merge K Sorted Lists                  | Same with linked lists — push (head.val, head)      | LC 23      |
| Merge Two Sorted Arrays               | Two-pointer merge, no heap needed                   | LC 88      |
| Find Median from Data Stream          | Two heaps (max for lower half, min for upper half)  | LC 295     |
| Smallest Range Covering K Lists       | Sliding window with heap to track min               | LC 632     |
| Kth Smallest in Sorted Matrix         | Heap-based exploration                              | LC 378     |
| K Pairs with Smallest Sums            | Min-heap for pair generation                        | LC 373     |
| Sort Characters by Frequency         | Heap for sorting by count                           | LC 451     |
| Ugly Number II                        | Min-heap or DP with multiple pointers               | LC 264     |

## 10. The ONE Trick to Remember

**"Heap holds one pointer per array — always extract the global minimum, then advance that array's pointer."**

You need the minimum across k sorted arrays at each step. Keep exactly one representative from each array in a min-heap. When you pop the minimum, push the next element from the same array. The heap size never grows beyond k. Total work: N pops and N pushes, each O(log k) → O(N log k). The same pattern extends to merging k sorted linked lists.
