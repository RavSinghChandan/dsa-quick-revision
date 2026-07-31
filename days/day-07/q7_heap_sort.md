# Day 7 — BST & Heap · Q7 · Heap Sort

---

## What is this question actually asking?

Sort an array **in place** using a heap — **O(n log n)** time, **O(1)** extra space.

```
[3,1,6,5,2,4] → [1,2,3,4,5,6]
```

> Builds on heapify (Q6). Build a max-heap, then repeatedly swap the max (root) to the end and shrink the heap.

---

## Pattern

```
PATTERN: Build max-heap, then extract-max n times into the tail
```

Whenever you see: *"sort in O(1) extra space with a heap"* → build a max-heap in O(n), then swap the root (largest) with the last unsorted element, shrink the heap by one, and sift the new root down. The sorted region grows from the back.

---

## Understand with a diagram

```
1. build_max_heap → largest at index 0

2. for end from n-1 down to 1:
     swap(a[0], a[end])       ← largest goes to its final position
     sift_down(a, 0, end)     ← restore heap on the shrunk range [0, end)

heap shrinks, sorted tail grows:
[6,5,4,1,2,3] → swap 6↔3 → [3,5,4,1,2 | 6] → sift → ... → [1,2,3,4,5,6]
```

---

## Brute Force → Better → Optimal

### 🟡 Other sorts — O(n log n) but O(n) space (merge sort) or unstable pivots (quicksort)
Heap sort's edge is **guaranteed O(n log n)** worst case AND **O(1)** space.

### 🟢 Heap sort — O(n log n) time, O(1) space
Build heap O(n) + n extractions × O(log n) sift = O(n log n). In place.

---

## Pseudocode (Optimal)

```
function heap_sort(a):
    n = len(a)
    build_max_heap(a)                 ← O(n)
    for end from n-1 down to 1:
        swap(a[0], a[end])            ← max to its final slot
        sift_down(a, 0, end)          ← reheapify range [0, end)
```

---

## Python Code

```python
def sift_down(a: list[int], i: int, n: int) -> None:
    while True:
        largest = i
        l, r = 2 * i + 1, 2 * i + 2
        if l < n and a[l] > a[largest]: largest = l
        if r < n and a[r] > a[largest]: largest = r
        if largest == i: break
        a[i], a[largest] = a[largest], a[i]
        i = largest

def heap_sort(a: list[int]) -> None:
    n = len(a)
    # 1. build max-heap
    for i in range(n // 2 - 1, -1, -1):
        sift_down(a, i, n)
    # 2. extract max to the end, shrink heap
    for end in range(n - 1, 0, -1):
        a[0], a[end] = a[end], a[0]      # largest to final position
        sift_down(a, 0, end)             # reheapify the remaining prefix


# Test
arr = [3,1,6,5,2,4]
heap_sort(arr)
print(arr)   # [1, 2, 3, 4, 5, 6]

import random
r = random.sample(range(100), 20)
heap_sort(r)
print(r == sorted(r))   # True
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Heap sort | O(n log n) worst | O(1) |

---

## Edge Cases to remember

- **Max-heap for ascending sort** — the largest goes to the back each round. (Min-heap → descending.)
- **Sift-down over the SHRINKING range** — pass `end` as the new heap size so the sorted tail is untouched.
- **Not stable** — equal elements may reorder; note if stability matters.
- **Already sorted / all equal** → still O(n log n) (no early exit like insertion sort).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Heapify (Q6) | The build step |
| 2 | Kth largest (Day 12 Q5) | Partial heap sort |
| 3 | Sort a nearly-sorted / k-sorted array | Min-heap window |
| 4 | Sort colors (Day 1 Q2) | In-place sort |
| 5 | Top K elements | Heap |
| 6 | Merge K sorted lists (Day 12 Q6) | Heap |
| 7 | Find median (LC 295) | Heaps |
| 8 | External sort | Heap merge |

---

## The ONE trick to remember

```
"BUILD MAX-HEAP; SWAP ROOT→END; SHRINK; SIFT-DOWN. REPEAT."
```

The max-heap puts the largest at index 0. Swap it to the array's end (its final resting place), shrink the heap by one, and sift the new root down. The sorted section grows from the back. O(n log n), O(1) space.

> Memory hook: "Pop the biggest to the back, fix the heap, repeat — the tail fills up sorted."

---
