# ⚡ Quick Note · Day 12 · Q3 · Move Zeroes [LISTS]
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Push all zeros to the end in place, keep order of the rest."

## Pattern
`Two pointer: slow write index + fast read index`

## Visual in your head
```
[0,1,0,3,12]  w=write, r=read
non-zero at r → nums[w]=nums[r]; w++
then fill w..end with 0  → [1,3,12,0,0]
```

## The trick (say it out loud)
> "Write index for keepers, read scans all, then pad the tail with zeros."

## Code skeleton
```python
w=0
for r in range(len(nums)):
    if nums[r]!=0: nums[w]=nums[r]; w+=1
for i in range(w,len(nums)): nums[i]=0
```

## Complexity
- Time O(n) · Space O(1) in place

## This trick solves more
Remove Element (27) · Remove Duplicates (26) · Sort Colors (75) · Compact valid rows · Filter in place
