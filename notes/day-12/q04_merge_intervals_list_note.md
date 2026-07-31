# ⚡ Quick Note · Day 12 · Q4 · Merge Intervals [LISTS]
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Merge overlapping [start,end] ranges into fewer ranges."

## Pattern
`Sort by start + linear sweep merging into the last kept interval`

## Visual in your head
```
sort by start → result=[first]
next.start <= last.end? → extend last.end=max(...)
else → append new
```

## The trick (say it out loud)
> "Sort by start. If next starts before last ends, stretch the last; else append a fresh one."

## Code skeleton
```python
intervals.sort(key=lambda x:x[0]); res=[intervals[0]]
for s,e in intervals[1:]:
    if s<=res[-1][1]: res[-1][1]=max(res[-1][1],e)
    else: res.append([s,e])
```

## Complexity
- Time O(n log n) · Space O(n)

## This trick solves more
Insert Interval (57) · Non-overlapping (435) · Meeting Rooms II (253) · Interval intersections (986) · Min Platforms
