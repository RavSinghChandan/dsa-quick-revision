# Day 7 — BST & Heap · Q6 · Heapify Algorithm (Build a Heap)

---

## What is this question actually asking?

Turn an arbitrary array into a valid **heap** (max-heap or min-heap) **in place**, in O(n).

```
[3,1,6,5,2,4] → build max-heap → [6,5,4,1,2,3]  (parent ≥ children)
```

> Tests the "sift-down from the last parent" build, and the surprising **O(n)** (not O(n log n)) complexity of bottom-up heapify.

---

## Pattern

```
PATTERN: Bottom-up heapify — sift-down each non-leaf, last parent first
```

Whenever you see: *"build a heap / heapify an array"* → start from the **last non-leaf** node (`n//2 − 1`) and sift each one down to its correct place, moving toward the root. Leaves are already valid heaps of size 1.

---

## Understand with a diagram

```
Array as a complete binary tree (0-indexed):
   parent(i) = (i-1)//2 ; children = 2i+1, 2i+2

Start at last parent index = n//2 - 1, go UP to 0.
sift_down(i): swap i with its larger child while it's smaller; repeat downward.

Why O(n)? Most nodes are near the leaves and sift down only a little; the sum
Σ (nodes at height h) × h converges to O(n).
```

---

## Brute Force → Better → Optimal

### 🔴 Insert one-by-one — O(n log n)
Push n elements into an empty heap; each push sifts up O(log n).

### 🟢 Optimal — bottom-up heapify — O(n)
Sift-down from the last parent to the root. Provably O(n).

---

## Pseudocode (Optimal)

```
function build_max_heap(a):
    n = len(a)
    for i from n//2 - 1 down to 0:
        sift_down(a, i, n)

function sift_down(a, i, n):
    while True:
        largest = i; l = 2i+1; r = 2i+2
        if l < n and a[l] > a[largest]: largest = l
        if r < n and a[r] > a[largest]: largest = r
        if largest == i: break
        swap(a[i], a[largest]); i = largest
```

---

## Python Code

```python
def sift_down(a: list[int], i: int, n: int) -> None:
    while True:
        largest = i
        l, r = 2 * i + 1, 2 * i + 2
        if l < n and a[l] > a[largest]:
            largest = l
        if r < n and a[r] > a[largest]:
            largest = r
        if largest == i:
            break
        a[i], a[largest] = a[largest], a[i]
        i = largest

def build_max_heap(a: list[int]) -> None:
    n = len(a)
    for i in range(n // 2 - 1, -1, -1):     # last non-leaf → root
        sift_down(a, i, n)


# Test — verify heap property
def is_max_heap(a):
    n = len(a)
    for i in range(n):
        for c in (2 * i + 1, 2 * i + 2):
            if c < n and a[c] > a[i]:
                return False
    return True

arr = [3,1,6,5,2,4]
build_max_heap(arr)
print(arr[0] == max([3,1,6,5,2,4]))   # True — root is the max
print(is_max_heap(arr))               # True
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Insert one-by-one | O(n log n) | O(1) |
| Bottom-up heapify | O(n) | O(1) |

---

## Edge Cases to remember

- **Start at `n//2 − 1`** — the last non-leaf; everything after is a leaf (already a valid heap).
- **Sift DOWN, not up** — bottom-up build sifts down; the O(n) proof depends on it.
- **Min-heap** → flip comparisons (`<` instead of `>`), track `smallest`.
- **`largest == i` early exit** — stop when no swap is needed.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Heap Sort (Q7) | Build heap then extract |
| 2 | Kth largest element (Day 12 Q5) | Heap |
| 3 | Kth largest in stream (Q8) | Heap |
| 4 | Top K frequent (LC 347) | Heap |
| 5 | Merge K sorted lists (Day 12 Q6) | Heap |
| 6 | Sort a nearly-sorted array (LC 912) | Heap window |
| 7 | Median from data stream (LC 295) | Two heaps |
| 8 | Reorganize string (LC 767) | Max-heap |

---

## The ONE trick to remember

```
"SIFT-DOWN FROM THE LAST PARENT (n//2−1) UP TO THE ROOT → O(n) BUILD."
```

Leaves are trivially valid heaps. Fix each internal node by sifting it down, starting from the deepest parent and moving toward the root. Bottom-up sift-down builds the heap in O(n), not O(n log n).

> Memory hook: "Fix the parents from the bottom up — leaves are already fine."

---
