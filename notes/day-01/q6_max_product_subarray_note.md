# ⚡ Quick Note · Day 1 — Arrays · Q6 · Max Product Subarray
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Largest product of any contiguous subarray."

## Pattern
`Kadane variant — track running max AND min`

## Visual in your head
```
x<0 → swap(cur_max, cur_min)
cur_max = max(x, cur_max*x)
cur_min = min(x, cur_min*x)
best = max(best, cur_max)
```

## The trick (say it out loud)
> "Track max AND min. A negative flips their roles — min*negative can be the new max."

## Code skeleton
```python
if x<0: cur_max,cur_min=cur_min,cur_max
cur_max=max(x,cur_max*x); cur_min=min(x,cur_min*x)
best=max(best,cur_max)
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
Kadane sum · Product Except Self · Subarray Product < K · Max sum after one deletion
