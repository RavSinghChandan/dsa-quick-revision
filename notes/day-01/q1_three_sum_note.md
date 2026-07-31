# ⚡ Quick Note · Day 1 — Arrays · Q1 · 3 Sum
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "All unique triplets summing to 0."

## Pattern
`Sort + fix one + two pointer on the rest`

## Visual in your head
```
sort → for i: l=i+1, r=n-1
sum<0 → l++   sum>0 → r--   sum==0 → record + skip dups
```

## The trick (say it out loud)
> "Sort, fix the first, two-pointer the other two. Skip equal neighbors at i, l, r."

## Code skeleton
```python
nums.sort()
for i in range(n):
    if i and nums[i]==nums[i-1]: continue
    l,r=i+1,n-1
    while l<r: s=nums[i]+nums[l]+nums[r] ...
```

## Complexity
- Time O(n²) · Space O(1)

## This trick solves more
4 Sum · 3 Sum Closest · 3 Sum Smaller · Two Sum II · Container With Most Water
