# ⚡ Quick Note · Day 5 — Stack & Greedy · Q5 · Largest Rectangle Histogram
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Largest rectangle area under a histogram."

## Pattern
`Monotonic increasing stack — width = prev-smaller → next-smaller`

## Visual in your head
```
increasing stack of indices
shorter bar arrives → pop, finalize:
  width = i - stack[-1] - 1 (or i if empty)
  area = heights[top] * width
sentinel 0 at end flushes
```

## The trick (say it out loud)
> "Each bar is a rectangle's height; width spans from the previous smaller to the next smaller bar."

## Code skeleton
```python
for i in range(n+1):
    h = heights[i] if i<n else 0
    while stack and heights[stack[-1]]>=h:
        top=stack.pop()
        w = i if not stack else i-stack[-1]-1
        best=max(best,heights[top]*w)
    stack.append(i)
```

## Complexity
- Time O(n) · Space O(n)

## This trick solves more
Maximal Rectangle (85) · Trapping Rain (Q4) · Subarray Minimums (907) · Stock Span (901)
