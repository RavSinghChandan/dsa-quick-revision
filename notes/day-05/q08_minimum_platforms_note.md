# ⚡ Quick Note · Day 5 — Stack & Greedy · Q8 · Minimum Platforms
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Min platforms so no train waits (max simultaneous trains)."

## Pattern
`Sort arr & dep SEPARATELY + two-pointer sweep`

## Visual in your head
```
sort arr, sort dep
if arr[i]<=dep[j]: platforms++, i++
else: platforms--, j++
track max
```

## The trick (say it out loud)
> "Sort arrivals and departures separately. Arrival before departure → +1 platform. Peak = answer."

## Code skeleton
```python
arr.sort(); dep.sort(); i=j=p=best=0
while i<n:
    if arr[i]<=dep[j]: p+=1; i+=1
    else: p-=1; j+=1
    best=max(best,p)
```

## Complexity
- Time O(n log n) · Space O(1)

## This trick solves more
Meeting Rooms II (253) · Car Pooling (1094) · Merge Intervals · Min Arrows (452) · Non-overlapping (435)
