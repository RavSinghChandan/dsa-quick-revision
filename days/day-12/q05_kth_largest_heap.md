# Day 12 · Q5 · Kth Largest Element (Min-Heap of size K)  ·  [HEAPS]

---

## What is this question actually asking?

Find the **Kth largest** element in a list (not the Kth *distinct* — the Kth in sorted order).

```
nums = [3,2,1,5,6,4], k = 2  → 2nd largest = 5
```

> The canonical heap question. Tests the counter-intuitive core idea: to keep the K **largest**, you use a **MIN**-heap of size K.

---

## Pattern

```
PATTERN: Min-Heap of fixed size K  (the "Top-K" pattern)
```

Whenever you see: *"Kth largest / K most X / top K"* → keep a **min-heap capped at size K**. The smallest of your K best sits at the top, ready to be evicted when a bigger one arrives. The root is your answer.

---

## Understand with a diagram

```
k=2, keep the 2 largest in a MIN-heap (smallest of the two on top):

3 → [3]
2 → [2,3]                 (size 2)
1 → 1 < top(2)? yes → skip
5 → 5 > top(2) → pop 2, push 5 → [3,5]
6 → 6 > top(3) → pop 3, push 6 → [5,6]
4 → 4 < top(5)? yes → skip
heap = [5,6], top(min) = 5  ← the 2nd largest ✅
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — sort all — O(n log n)
`sorted(nums)[-k]`. Simple and often accepted, but sorts everything even for k=1.

### 🟢 Optimal — Min-Heap size K — O(n log K)
Push each number; if the heap exceeds K, pop the smallest. Only the K largest survive, and the root (min of those) is the Kth largest. Great when n is huge and K small, or data streams in.

---

## Pseudocode (Optimal)

```
function kth_largest(nums, k):
    heap = min-heap
    for num in nums:
        push num
        if size(heap) > k:
            pop smallest
    return top(heap)         ← smallest of the K largest = Kth largest
```

---

## Python Code

```python
import heapq

def kth_largest(nums: list[int], k: int) -> int:
    heap = []                        # min-heap
    for num in nums:
        heapq.heappush(heap, num)
        if len(heap) > k:
            heapq.heappop(heap)      # drop the smallest
    return heap[0]                   # root = Kth largest

# One-liner using the library
def kth_largest_lib(nums, k):
    return heapq.nlargest(k, nums)[-1]


# Test
print(kth_largest([3,2,1,5,6,4], 2))   # 5
print(kth_largest([1], 1))             # 1
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Sort | O(n log n) | O(1)/O(n) |
| Min-Heap size K | O(n log K) | O(K) |
| Quickselect (avg) | O(n) | O(1) |

---

## Edge Cases to remember

- **MIN-heap for Kth LARGEST** — this is the trap; say it out loud. (Max-heap of size K would be for Kth *smallest*.)
- **k = 1** → the maximum; **k = n** → the minimum.
- **Duplicates count** — Kth largest is by position, not distinct value.
- **Streaming input** → the heap version shines; you never store all n.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Top K Frequent Elements (LC 347) | Heap on counts |
| 2 | K Closest Points to Origin (LC 973) | Heap on distance |
| 3 | Kth Smallest in Sorted Matrix (LC 378) | Heap |
| 4 | Find Median from Data Stream (LC 295) | Two heaps |
| 5 | Token frequency Top-K (Day 11 Q4) | Same |
| 6 | Merge K Sorted Lists (Q6) | Min-heap of heads |
| 7 | Reorganize / task scheduler | Max-heap |
| 8 | Sliding window maximum | Monotonic deque / heap |

---

## The ONE trick to remember

```
"Kth LARGEST → MIN-heap of size K. Root = answer."
```

Keep only K elements; whenever a bigger one arrives, evict the smallest (heap root). What remains are the K largest, and their minimum (the root) is exactly the Kth largest. O(n log K), no full sort.

> Memory hook: "Guard the podium of K — kick out the weakest each time."

---
