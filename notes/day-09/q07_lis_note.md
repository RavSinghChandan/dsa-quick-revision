# ⚡ Quick Note · Day 9 — DP · Q7 · Longest Increasing Subsequence
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Length of the longest strictly increasing subsequence."

## Pattern
`DP O(n²)  OR  patience + binary search O(n log n)`

## Visual in your head
```
tails[k] = smallest tail of an LIS of length k+1
for x: pos=bisect_left(tails,x)
  pos==len → append (extend); else tails[pos]=x (improve)
answer=len(tails)
```

## The trick (say it out loud)
> "Keep smallest tail per length. bisect_left: extend if at the end, else replace. Length of tails = LIS."

## Code skeleton
```python
for x in nums:
    pos=bisect_left(tails,x)
    if pos==len(tails): tails.append(x)
    else: tails[pos]=x
```

## Complexity
- DP O(n²) · Patience O(n log n) · Space O(n)

## This trick solves more
LIS (300) · Russian Dolls (354) · Number of LIS (673) · Longest Chain (646) · Box Stacking
