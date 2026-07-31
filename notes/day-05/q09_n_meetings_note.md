# ⚡ Quick Note · Day 5 — Stack & Greedy · Q9 · N Meetings
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Max non-overlapping meetings in one room."

## Pattern
`Greedy activity selection — sort by END time`

## Visual in your head
```
sort by end
last_end=-inf
if start > last_end: take; last_end=end; count++
```

## The trick (say it out loud)
> "Sort by END time. Take the earliest-finishing compatible meeting — it leaves the most room."

## Code skeleton
```python
for s,e in sorted(zip(start,end),key=lambda m:m[1]):
    if s>last_end: count+=1; last_end=e
```

## Complexity
- Time O(n log n) · Space O(n)

## This trick solves more
Non-overlapping Intervals (435) · Min Arrows (452) · Meeting Rooms I (252) · Pair Chain (646) · Job Sequencing
