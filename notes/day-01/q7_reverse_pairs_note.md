# ⚡ Quick Note · Day 1 — Arrays · Q7 · Reverse Pairs
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Count pairs i<j with nums[i] > 2*nums[j]."

## Pattern
`Merge sort + counting (divide & conquer)`

## Visual in your head
```
sort both halves, both sorted →
for i in left: advance j in right while left[i]>2*right[j]
count += (j - start_right); then merge
```

## The trick (say it out loud)
> "Count cross-pairs during merge sort while both halves are sorted. j only moves forward → O(n) per merge."

## Code skeleton
```python
cnt = ms(lo,mid)+ms(mid+1,hi)
j=mid+1
for i in range(lo,mid+1):
    while j<=hi and a[i]>2*a[j]: j+=1
    cnt += j-(mid+1)
```

## Complexity
- Time O(n log n) · Space O(n)

## This trick solves more
Count Inversions · Count Smaller After Self (315) · Count Range Sum (327) · Reverse Pairs (493)
