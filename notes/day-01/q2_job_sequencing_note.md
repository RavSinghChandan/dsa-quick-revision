# Q2 — Job Sequencing with Deadlines (2-min card)

**Schedule jobs with deadlines to maximize total profit — each job takes 1 unit of time.**

**Pattern:** Greedy — sort by profit descending, assign each job to the latest free slot before its deadline.

```
Jobs sorted by profit:
J3(profit=35,dead=3)  J1(profit=20,dead=2)  J4(profit=18,dead=1)  J2(profit=15,dead=2)

Slot array: [_, _, _]  (indices 1..3)
J3 dead=3 → slot[3] free → place  [_, _, J3]
J1 dead=2 → slot[2] free → place  [_, J1, J3]
J4 dead=1 → slot[1] free → place  [J4, J1, J3]
J2 dead=2 → slot[2] taken, slot[1] taken → skip
Result profit = 18+20+35 = 73
```

**The trick:** "Latest available slot before deadline wins" — fill from the right so earlier slots stay open for lower-profit jobs.

```python
jobs.sort(key=lambda x: -x[1])        # sort by profit desc
slot = [-1] * (max_deadline + 1)
for job_id, deadline, profit in jobs:
    for t in range(deadline, 0, -1):   # find latest free slot
        if slot[t] == -1:
            slot[t] = job_id; break
```

**Complexity:** Time O(n² worst) or O(n log n) with DSU | Space O(n)

**Same pattern solves:** Task scheduler, weighted job scheduling, course scheduling with deadlines, CPU burst scheduling, exam timetabling, project selection with deadlines, bandwidth allocation, activity-profit maximization.
