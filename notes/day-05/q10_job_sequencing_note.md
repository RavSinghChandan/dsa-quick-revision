# ⚡ Quick Note · Day 5 — Stack & Greedy · Q10 · Job Sequencing
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Unit jobs with deadline+profit; maximize profit."

## Pattern
`Greedy: sort by profit desc + latest-free-slot`

## Visual in your head
```
sort by profit desc
slot[1..maxDeadline]
each job: find latest free slot <= deadline → place, add profit
```

## The trick (say it out loud)
> "Richest jobs first; place each in the latest free slot ≤ its deadline (keeps early slots free)."

## Code skeleton
```python
jobs.sort(key=lambda j:j[2],reverse=True)
for id,d,p in jobs:
    for t in range(min(d,maxD),0,-1):
        if slot[t] is None: slot[t]=id; total+=p; break
```

## Complexity
- Greedy+array O(n·d) · Greedy+DSU O(n log n) · Space O(d)

## This trick solves more
Task Scheduler (621) · Course Schedule III (630) · Job Scheduling (1235) · N Meetings · Assign Cookies (455)
