# ⚡ Quick Note · Day 5 — Stack & Greedy · Q4 · Trapping Rain Water
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "How much rainwater is trapped between bars."

## Pattern
`Two pointers with running leftMax/rightMax`

## Visual in your head
```
water[i] = min(leftMax, rightMax) - h[i]
if h[l]<=h[r]: leftMax=max(..); water+=leftMax-h[l]; l++
else:          rightMax=max(..); water+=rightMax-h[r]; r--
```

## The trick (say it out loud)
> "Water = min(leftMax, rightMax) − h. Two pointers — always advance the SHORTER wall."

## Code skeleton
```python
while l<r:
    if h[l]<=h[r]: lm=max(lm,h[l]); water+=lm-h[l]; l+=1
    else: rm=max(rm,h[r]); water+=rm-h[r]; r-=1
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
Rain Water II (407) · Container Most Water (11) · Histogram · Product Except Self
