# Day 01 · Q2 · Job Sequencing Problem

---

## What is this question actually asking?

You have a list of jobs. Each job has a deadline (it must finish by day X) and a profit (you earn this much if you complete it). You can do only one job per day. You want to maximize total profit.

Think of it like a freelancer with limited days — which jobs do you pick to earn the most money? You always want to pick the highest-paying jobs first, but they must fit within their deadlines.

The key insight: you don't have to do the job on day 1 — you can do it any day UP TO its deadline.

---

## Pattern

```
PATTERN: Greedy + Sorting (sort by profit descending, fill latest available slot)
```

---

## Understand with a diagram

```
Jobs:   J1    J2    J3    J4    J5
Profit: 20    15    10    5     1
Deadln:  2     2     1    3     3

Sort by profit (already sorted here).

Day slots: [_, _, _]   (days 1, 2, 3)

Step 1: J1 profit=20, deadline=2 → try slot day-2 → free → assign
        Slots: [_, J1, _]

Step 2: J2 profit=15, deadline=2 → try slot day-2 → taken → try day-1 → free → assign
        Slots: [J2, J1, _]

Step 3: J3 profit=10, deadline=1 → try slot day-1 → taken → no earlier slot → skip

Step 4: J4 profit=5, deadline=3 → try slot day-3 → free → assign
        Slots: [J2, J1, J4]

Step 5: J5 profit=1, deadline=3 → try day-3 → taken → try day-2 → taken → try day-1 → taken → skip

Result: Jobs [J1, J2, J4], Total profit = 20+15+5 = 40
```

---

## Brute Force → Better → Optimal

### Brute Force — O(n! or 2^n)

**Idea:** Try all subsets of jobs, check which ones fit within deadlines, pick highest profit.

```
for each subset of jobs:
    if all jobs in subset can be scheduled within deadlines:
        total = sum of profits in subset
        max_profit = max(max_profit, total)
```

**Why slow?** Exponential number of subsets. Completely impractical for n > 20.

---

### Better — O(n²) Greedy

**Idea:** Sort by profit (high to low). For each job, scan from its deadline backwards to find the latest free slot.

```
sort jobs by profit descending
slots = array of size (max_deadline) filled with -1

for each job in sorted order:
    for day = job.deadline down to 1:
        if slots[day] is free:
            slots[day] = job
            break
```

**Why O(n²)?** For each job (n), you scan up to deadline positions (up to n). Nested loop.

---

### Optimal — O(n log n) with Union-Find

**Idea:** Use Union-Find (Disjoint Set) to track "next free slot". When slot d is filled, union it with d-1 so future lookups skip directly to the next free slot.

```
sort jobs by profit descending
parent[i] = i   ← each slot is its own parent

function find(x):
    if parent[x] == x: return x
    parent[x] = find(parent[x])   ← path compression
    return parent[x]

for each job (profit p, deadline d):
    available_slot = find(d)
    if available_slot > 0:
        assign job to available_slot
        parent[available_slot] = available_slot - 1   ← next time, skip to d-1
        total_profit += p
```

**Why faster?** Each find() is nearly O(1) with path compression. Total: O(n log n) for sort + O(n α(n)) for union-find.

For interviews, the O(n²) greedy is usually acceptable and cleaner to explain.

---

## Pseudocode (Optimal - Interview friendly O(n²))

```
function jobSequencing(jobs):
    sort jobs by profit in descending order
    max_deadline = max(job.deadline for all jobs)
    slots = array of size (max_deadline + 1), all -1
    count = 0
    total_profit = 0

    for each job in sorted order:
        for day = job.deadline down to 1:
            if slots[day] == -1:
                slots[day] = job.id
                count += 1
                total_profit += job.profit
                break

    return count, total_profit
```

---

## Python Code

```python
def job_sequencing(jobs):
    # jobs is list of (job_id, deadline, profit)
    # Sort by profit descending
    jobs.sort(key=lambda x: x[2], reverse=True)
    
    max_deadline = max(job[1] for job in jobs)
    slots = [-1] * (max_deadline + 1)  # slots[1..max_deadline]
    
    count = 0
    total_profit = 0
    
    for job_id, deadline, profit in jobs:
        # Find latest free slot at or before deadline
        for day in range(deadline, 0, -1):
            if slots[day] == -1:
                slots[day] = job_id
                count += 1
                total_profit += profit
                break
    
    return count, total_profit


# Test
jobs = [
    ('J1', 2, 20),
    ('J2', 2, 15),
    ('J3', 1, 10),
    ('J4', 3, 5),
    ('J5', 3, 1),
]
count, profit = job_sequencing(jobs)
print(f"Jobs done: {count}, Total profit: {profit}")  # Jobs done: 3, Total profit: 40
```

---

## Complexity

| Approach | Time | Space |
|---|---|---|
| Brute Force (all subsets) | O(2^n) | O(n) |
| Greedy O(n²) | O(n log n + n²) = O(n²) | O(max_deadline) |
| Greedy + Union-Find | O(n log n) | O(max_deadline) |

---

## Edge Cases to remember

- All jobs have deadline = 1 → only the highest profit job can be done
- Multiple jobs with the same deadline → greedy handles it naturally (picks higher profit first)
- Deadline larger than number of jobs → extra slots are just unused
- Job with profit = 0 → still valid, but greedy naturally skips it (picks higher first)

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Task Scheduler (LeetCode 621) | Schedule tasks with cooldown |
| 2 | Meeting Rooms II (LeetCode 253) | Schedule overlapping meetings |
| 3 | Course Schedule (LeetCode 207) | Ordering with constraints |
| 4 | N Meetings in One Room | Activity selection greedy |
| 5 | Fractional Knapsack | Greedy by ratio |
| 6 | Minimum Cost to Hire K Workers (LeetCode 857) | Greedy + heap |
| 7 | Assign Cookies (LeetCode 455) | Greedy matching |
| 8 | Jump Game II (LeetCode 45) | Greedy reach extension |
| 9 | Non-overlapping Intervals (LeetCode 435) | Greedy interval removal |
| 10 | Minimum Number of Arrows (LeetCode 452) | Greedy interval cover |

---

## The ONE trick to remember

```
"SORT BY PROFIT, FILL FROM THE LATEST SLOT BACKWARDS"
```

Why? Because you always want to do the most profitable job. And you should do it as late as possible — this keeps earlier slots free for jobs with tighter deadlines.

> Memory hook: "Greedy freelancer — take the biggest paycheck, save early days for urgent work"

---
