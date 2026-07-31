# Day 7 — BST & Heap · Q8 · Kth Largest Element in a Stream

---

## What is this question actually asking?

Design a class that, as numbers **stream in**, can always report the **Kth largest** element seen so far.

```
KthLargest(3, [4,5,8,2])
add(3) → 4 ; add(5) → 5 ; add(10) → 5 ; add(9) → 8 ; add(4) → 8
```

> The streaming twist on Kth-largest. A **min-heap of size K** maintains the K biggest so far — its root is the answer in O(1), each add O(log K).

---

## Pattern

```
PATTERN: Min-Heap of fixed size K (streaming Top-K)
```

Whenever you see: *"Kth largest as data arrives / online Top-K"* → keep a min-heap capped at size K. New value: push; if size exceeds K, pop the smallest. The root is always the Kth largest.

---

## Understand with a diagram

```
Keep the K largest in a MIN-heap (smallest of them on top = the Kth largest).

K=3, stream 4 5 8 2 → heap keeps 3 biggest: [4,5,8], root=4
add(3): 3 < root 4 → skip (heap unchanged) → Kth largest still 4
add(5): push → [4,5,5,8] size4 > 3 → pop 4 → [5,5,8] → root 5
```

The root (min of the K largest) is exactly the Kth largest overall.

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — sort on every query — O(n log n) per add
Re-sort the whole list each time. Slow for a stream.

### 🟢 Optimal — min-heap size K — O(log K) per add, O(1) query
Maintain only the K largest. Push/pop is O(log K); the root is the answer.

---

## Pseudocode (Optimal)

```
class KthLargest(k, nums):
    heap = min-heap
    for x in nums: add(x)

    add(val):
        push val onto heap
        if size(heap) > k: pop smallest
        return heap[0]     ← Kth largest
```

---

## Python Code

```python
import heapq

class KthLargest:
    def __init__(self, k: int, nums: list[int]):
        self.k = k
        self.heap = nums[:]              # min-heap of the k largest
        heapq.heapify(self.heap)
        while len(self.heap) > k:
            heapq.heappop(self.heap)

    def add(self, val: int) -> int:
        heapq.heappush(self.heap, val)
        if len(self.heap) > self.k:
            heapq.heappop(self.heap)     # drop the smallest
        return self.heap[0]              # root = Kth largest


# Test
kth = KthLargest(3, [4, 5, 8, 2])
print(kth.add(3))    # 4
print(kth.add(5))    # 5
print(kth.add(10))   # 5
print(kth.add(9))    # 8
print(kth.add(4))    # 8
```

---

## Complexity

| | Time | Space |
|---|---|---|
| add | O(log K) | O(K) |
| query (root) | O(1) | — |

---

## Edge Cases to remember

- **MIN-heap for Kth LARGEST** — the recurring trap; the smallest of the K biggest sits at the root.
- **Initial nums fewer than K** — heap may start with < K; once enough arrive, the root becomes valid.
- **Duplicates count by position**, not distinct value.
- **`heap[0]` is O(1)** — no pop needed to read the answer.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Kth Largest in an array (Day 12 Q5) | Static version |
| 2 | Kth Largest in a Stream (LC 703) | This problem |
| 3 | Top K Frequent (LC 347) | Heap on counts |
| 4 | Median from Data Stream (LC 295) | Two heaps |
| 5 | K Closest Points (LC 973) | Size-K heap |
| 6 | Sliding window median | Two heaps |
| 7 | Sort a k-sorted array | Min-heap window |
| 8 | Find K pairs smallest sums (LC 373) | Heap frontier |

---

## The ONE trick to remember

```
"MIN-HEAP OF SIZE K; PUSH-THEN-POP-IF-OVER-K; ROOT = Kth LARGEST."
```

Keep only the K largest in a min-heap. Each arrival: push, and if the heap grows past K, pop the smallest. The root — the minimum of the K largest — is the Kth largest so far. O(log K) per add.

> Memory hook: "Guard a podium of K; the shortest on the podium is your Kth-largest answer."

---
