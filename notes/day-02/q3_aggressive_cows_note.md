# ⚡ Quick Note · Day 2 — Binary Search · Q3 · Aggressive Cows
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Place k cows so the minimum gap between them is as large as possible."

## Pattern
`Binary search on answer (maximize min, greedy check)`

## Visual in your head
```
sort stalls. distance d in [1, max-min]
can_place(d): greedily seat cows >= d apart, count>=k?
feasible → go bigger (lo=d+1) else smaller
```

## The trick (say it out loud)
> "Maximize the min → binary-search the gap. Greedy place; on success go bigger."

## Code skeleton
```python
def can(d):
    cnt=1; last=stalls[0]
    for s in stalls[1:]:
        if s-last>=d: cnt+=1; last=s
    return cnt>=k
# feasible → ans=d, lo=d+1 ; else hi=d-1
```

## Complexity
- Time O(n log(max-min)) · Space O(1)

## This trick solves more
Koko (min version) · Magnetic Force Balls (1552) · Split Array Largest Sum (410) · Divide Chocolate (1231)
