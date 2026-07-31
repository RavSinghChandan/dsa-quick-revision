# ⚡ Quick Note · Day 7 — BST & Heap · Q7 · Heap Sort
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Sort in place, O(n log n), O(1) space, using a heap."

## Pattern
`Build max-heap → extract-max into the tail n times`

## Visual in your head
```
build max-heap (O(n))
for end from n-1 to 1:
  swap(a[0], a[end])       ← max to final slot
  sift_down(a, 0, end)     ← reheapify shrunk range
```

## The trick (say it out loud)
> "Build a max-heap, swap root to the end, shrink, sift-down. Sorted tail grows from the back."

## Code skeleton
```python
for i in range(n//2-1,-1,-1): sift_down(a,i,n)
for end in range(n-1,0,-1):
    a[0],a[end]=a[end],a[0]; sift_down(a,0,end)
```

## Complexity
- Time O(n log n) worst · Space O(1) · not stable

## This trick solves more
Heapify · Kth Largest · k-sorted array · Top K · Merge K Lists · Median Stream (295)
