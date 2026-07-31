# ⚡ Quick Note · Day 5 — Stack & Greedy · Q1 · Next Greater Element
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "For each element, the next bigger element to its right (else -1)."

## Pattern
`Monotonic decreasing stack (of indices)`

## Visual in your head
```
stack of indices, values decreasing
new x: while nums[top]<x → ans[pop]=x
push i ; leftovers stay -1
```

## The trick (say it out loud)
> "Decreasing stack of indices. A bigger element pops and resolves all smaller ones waiting."

## Code skeleton
```python
for i,x in enumerate(nums):
    while stack and nums[stack[-1]]<x: ans[stack.pop()]=x
    stack.append(i)
```

## Complexity
- Time O(n) · Space O(n)

## This trick solves more
NGE I/II (496/503) · Daily Temperatures (739) · Stock Span (901) · Histogram · Subarray Minimums (907)
