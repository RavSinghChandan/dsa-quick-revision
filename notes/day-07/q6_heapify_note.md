# ⚡ Quick Note · Day 7 — BST & Heap · Q6 · Heapify
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Turn an array into a heap in place, in O(n)."

## Pattern
`Bottom-up heapify — sift-down from last parent`

## Visual in your head
```
parent(i)=(i-1)//2, children=2i+1,2i+2
for i from n//2-1 down to 0: sift_down(i)
leaves already valid → O(n) total
```

## The trick (say it out loud)
> "Sift-down from the last parent (n//2−1) up to the root. Bottom-up build is O(n), not O(n log n)."

## Code skeleton
```python
for i in range(n//2-1,-1,-1): sift_down(a,i,n)
# sift_down: largest among i,2i+1,2i+2; swap down until stable
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
Heap Sort · Kth Largest · Top K Frequent (347) · Merge K Lists · Median Stream (295)
