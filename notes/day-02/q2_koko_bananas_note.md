# ⚡ Quick Note · Day 2 — Binary Search · Q2 · Koko Bananas
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Min eating speed to finish all piles within h hours."

## Pattern
`Binary search on the answer (monotonic feasibility)`

## Visual in your head
```
speed in [1, max(piles)]
hours = Σ ceil(pile/speed)
hours<=h → feasible → hi=mid (slower)
else → lo=mid+1 (faster)
```

## The trick (say it out loud)
> "Binary-search the speed. feasible(speed) = Σceil(pile/speed) <= h is monotonic."

## Code skeleton
```python
while lo<hi:
    mid=(lo+hi)//2
    if sum((p+mid-1)//mid for p in piles)<=h: hi=mid
    else: lo=mid+1
return lo
```

## Complexity
- Time O(n log max) · Space O(1)

## This trick solves more
Aggressive Cows · Split Array Largest Sum (410) · Painter's · Ship in D Days (1011) · Smallest Divisor (1283)
