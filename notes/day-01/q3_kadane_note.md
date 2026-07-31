# ⚡ Quick Note · Day 1 — Arrays · Q3 · Kadane's Algorithm
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Largest sum of any contiguous subarray."

## Pattern
`Kadane — running sum, reset when negative`

## Visual in your head
```
cur = max(x, cur+x)     ← extend or restart
best = max(best, cur)
negative running sum → drop it
```

## The trick (say it out loud)
> "cur = max(x, cur+x). A negative prefix can only hurt — restart. Init best = first element."

## Code skeleton
```python
best=cur=nums[0]
for x in nums[1:]:
    cur=max(x,cur+x); best=max(best,cur)
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
Max Product Subarray · Circular max subarray · Buy/Sell I · Max sum rectangle 2D · House Robber
