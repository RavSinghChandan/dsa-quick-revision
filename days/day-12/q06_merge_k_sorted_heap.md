# Day 12 · Q6 · Merge K Sorted Lists / Streams (Min-Heap)  ·  [HEAPS]

---

## What is this question actually asking?

You have K already-sorted lists (or streams). Merge them into **one sorted list**.

```
[[1,4,5],[1,3,4],[2,6]] → [1,1,2,3,4,4,5,6]
```

> Real AI use: merging sorted shards, combining ranked results from K retrievers, k-way merge in external sort. Tests the **min-heap of "current heads"** idea.

---

## Pattern

```
PATTERN: Min-Heap holding one "frontier" element from each list (k-way merge)
```

Whenever you see: *"merge K sorted things"* → put the **front element of each list** in a min-heap. Repeatedly pop the global smallest, output it, and push the **next** element from the list it came from.

---

## Understand with a diagram

```
lists: A[1,4,5] B[1,3,4] C[2,6]
heap holds one head per list: (val, which list, index)

heap: (1,A) (1,B) (2,C)
pop 1(A) → out[1] → push A's next 4 → heap (1,B)(2,C)(4,A)
pop 1(B) → out[1,1] → push B's 3    → heap (2,C)(3,B)(4,A)
pop 2(C) → out[1,1,2] → push C's 6  → ...
... → [1,1,2,3,4,4,5,6]
```

Only K items ever sit in the heap → memory is O(K), not O(total).

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — concatenate then sort — O(N log N)
Dump everything into one list and sort. Ignores that inputs are already sorted; O(N log N) where N = total elements.

### 🟢 Optimal — Min-heap k-way merge — O(N log K)
Heap only ever holds K elements (one per list). Each of the N outputs costs one pop + one push = O(log K). Beats the naive sort when K ≪ N, and works on **streams** you can't fully load.

---

## Pseudocode (Optimal)

```
function merge_k(lists):
    heap = min-heap
    for i, lst in enumerate(lists):
        if lst not empty:
            push (lst[0], i, 0)          ← (value, list index, elem index)
    out = []
    while heap not empty:
        val, i, j = pop smallest
        out.append(val)
        if j+1 < len(lists[i]):
            push (lists[i][j+1], i, j+1)  ← next from same list
    return out
```

---

## Python Code

```python
import heapq

def merge_k_sorted(lists: list[list[int]]) -> list[int]:
    heap = []
    for i, lst in enumerate(lists):
        if lst:                                   # seed with each list's head
            heapq.heappush(heap, (lst[0], i, 0))  # (value, list idx, elem idx)

    out = []
    while heap:
        val, i, j = heapq.heappop(heap)
        out.append(val)
        if j + 1 < len(lists[i]):
            heapq.heappush(heap, (lists[i][j + 1], i, j + 1))  # push next
    return out


# Test
print(merge_k_sorted([[1,4,5],[1,3,4],[2,6]]))
# [1, 1, 2, 3, 4, 4, 5, 6]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Concat + sort | O(N log N) | O(N) |
| Heap k-way merge | O(N log K) | O(K) heap |

(N = total elements, K = number of lists)

---

## Edge Cases to remember

- **Empty lists among inputs** → skip them when seeding (don't push).
- **All lists empty** → return `[]`.
- **Tie values** → the tuple `(value, i, j)` breaks ties by list index, avoiding comparison errors when values are equal (crucial if elements aren't comparable beyond value).
- **Streams** → same idea; pull the next item from the source you popped from.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Merge K Sorted Lists (LC 23) | Exact, linked-list version |
| 2 | Smallest Range Covering K Lists (LC 632) | Heap of heads + track max |
| 3 | Kth Smallest in Sorted Matrix (LC 378) | Rows = sorted lists |
| 4 | Merge sorted file shards (external sort) | k-way merge |
| 5 | Combine ranked retriever results | Heap of heads |
| 6 | Ugly Number II / super ugly | Heap frontier |
| 7 | Find K pairs with smallest sums (LC 373) | Heap frontier |
| 8 | Kth largest (Q5) | Same heap toolkit |

---

## The ONE trick to remember

```
"HEAP HOLDS ONE HEAD PER LIST. POP SMALLEST, PUSH ITS SUCCESSOR."
```

The heap only ever contains K "frontier" elements. Each pop emits the next global minimum; you refill from the same list it came from. O(N log K) time, O(K) memory — perfect for many sorted sources or streams.

> Memory hook: "K queues, always grab the smallest front, then advance that queue."

---
