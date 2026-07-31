# ⚡ Quick Note · Day 5 — Stack & Greedy · Q3 · Sum of Subarray Ranges
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Sum of (max−min) over ALL subarrays."

## Pattern
`Σmax − Σmin via monotonic-stack contribution`

## Visual in your head
```
range = max - min → sum splits: Σmax - Σmin
each element: count subarrays it's the max/min of
contribution = value * leftCount * rightCount
```

## The trick (say it out loud)
> "Σrange = Σmax − Σmin. Each value contributes value × leftCount × rightCount via prev/next boundaries."

## Code skeleton
```python
# for min: while stack and nums[top]>nums[i]:
#   mid=pop; left=mid-(stack[-1] or -1); right=i-mid
#   total += nums[mid]*left*right
# answer = sumOfMax - sumOfMin
```

## Complexity
- Brute O(n²) · Stack O(n)

## This trick solves more
Subarray Minimums (907) · Subarray Ranges (2104) · Histogram · Maximal Rectangle (85) · Wizard Strength (2281)
