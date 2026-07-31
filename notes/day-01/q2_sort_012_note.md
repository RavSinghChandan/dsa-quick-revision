# ⚡ Quick Note · Day 1 — Arrays · Q2 · Sort 0s 1s 2s
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Sort an array of only 0/1/2 in one pass, in place."

## Pattern
`Dutch National Flag — three pointers (low, mid, high)`

## Visual in your head
```
[0s | 1s | unknown(mid..high) | 2s]
0 → swap low, low++ mid++
1 → mid++
2 → swap high, high--   (don't move mid)
```

## The trick (say it out loud)
> "low/mid/high. 0 swap-low, 1 skip, 2 swap-high and DON'T advance mid."

## Code skeleton
```python
while mid<=high:
    if a[mid]==0: a[low],a[mid]=a[mid],a[low]; low+=1; mid+=1
    elif a[mid]==1: mid+=1
    else: a[mid],a[high]=a[high],a[mid]; high-=1
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
Sort Colors (75) · Move Zeroes · 3-way quicksort partition · Segregate even/odd
