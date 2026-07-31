# ⚡ Quick Note · Day 2 — Binary Search · Q1 · Search Rotated II
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Search target in a rotated sorted array that may have duplicates."

## Pattern
`Modified binary search + duplicate guard`

## Visual in your head
```
nums[lo]==nums[mid]==nums[hi] → lo++, hi-- (shrink)
left sorted (nums[lo]<=nums[mid]) → target in [lo,mid)? hi=mid-1 else lo=mid+1
else right sorted → mirror
```

## The trick (say it out loud)
> "One half is always sorted — search it. If both ends equal mid, shrink both."

## Code skeleton
```python
if nums[lo]==nums[mid]==nums[hi]: lo+=1; hi-=1
elif nums[lo]<=nums[mid]:
    if nums[lo]<=t<nums[mid]: hi=mid-1
    else: lo=mid+1
else: ...mirror...
```

## Complexity
- Time O(log n) avg, O(n) worst (dups) · Space O(1)

## This trick solves more
Search Rotated I (33) · Find Min Rotated (153/154) · Peak Element (162) · First/Last Position (34)
