# ⚡ Quick Note · Day 2 — Binary Search · Q4 · Median of 2 Sorted
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Median of two sorted arrays in O(log(min(m,n)))."

## Pattern
`Binary search the PARTITION of the smaller array`

## Visual in your head
```
cut A at i → cut B at j=half-i
L1=A[i-1] R1=A[i]  L2=B[j-1] R2=B[j]  (±inf at edges)
valid: L1<=R2 and L2<=R1
odd → max(L1,L2) ; even → (max(L1,L2)+min(R1,R2))/2
```

## The trick (say it out loud)
> "Binary-search the cut in the SMALLER array; B's cut is forced. Line up the boundary four."

## Code skeleton
```python
if L1<=R2 and L2<=R1: return median from boundaries
elif L1>R2: hi=i-1
else: lo=i+1
```

## Complexity
- Time O(log min(m,n)) · Space O(1)

## This trick solves more
Kth of two sorted · Split Array Largest Sum (410) · Median of matrix · Kth smallest pair distance
