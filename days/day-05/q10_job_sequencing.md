# Day 5 — Stack, Queue & Greedy · Q10 · Job Sequencing Problem

---

## What is this question actually asking?

Each job has a **deadline** and a **profit**; each takes 1 unit of time and only one job runs at a time. Schedule jobs to **maximize total profit** (a job earns its profit only if finished by its deadline).

```
jobs = [(id, deadline, profit)]
[(1,4,20),(2,1,10),(3,1,40),(4,1,30)] → profit 60, jobs done 2
```

> Greedy: take the **most profitable** jobs first, and schedule each in the **latest free slot** before its deadline.

---

## Pattern

```
PATTERN: Greedy (sort by profit desc) + latest-free-slot assignment
```

Whenever you see: *"maximize profit with deadlines, unit jobs"* → sort jobs by profit descending; for each, place it in the latest empty time slot ≤ its deadline (keeps earlier slots open for tighter-deadline jobs).

---

## Understand with a diagram

```
sort by profit desc: (3,1,40)(4,1,30)(1,4,20)(2,1,10)
slots[1..maxDeadline], all empty

(3,d1,p40): latest free slot <=1 → slot1 → profit 40
(4,d1,p30): slot1 taken, no slot <=1 free → skip
(1,d4,p20): latest free <=4 → slot4 → profit 60
(2,d1,p10): no slot <=1 → skip
total = 60, jobs done = 2
```

Placing in the *latest* slot preserves flexibility for jobs with tighter deadlines.

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all orderings — O(n!)
Try every schedule.

### 🟢 Greedy — sort by profit + slot array — O(n²) worst (O(n·d))
Sort desc by profit; scan back from each deadline for a free slot. Simple and standard.

### 🟢 Optimal — greedy + DSU for slot lookup — O(n log n)
Use a union-find to jump to the latest free slot in near-O(1). Same greedy, faster slot search.

---

## Pseudocode (Greedy + slot array)

```
function job_sequencing(jobs):
    sort jobs by profit descending
    max_deadline = max(d for job in jobs)
    slot = [free] * (max_deadline + 1)     ← 1-indexed
    total = 0; count = 0
    for (id, deadline, profit) in jobs:
        for t in range(min(deadline, max_deadline), 0, -1):
            if slot[t] is free:
                slot[t] = id; total += profit; count++; break
    return count, total
```

---

## Python Code

```python
def job_sequencing(jobs: list[tuple]) -> tuple[int, int]:
    # jobs: (id, deadline, profit)
    jobs.sort(key=lambda j: j[2], reverse=True)        # by profit desc
    max_deadline = max(d for _, d, _ in jobs)
    slot = [None] * (max_deadline + 1)                 # slot[1..max_deadline]

    count = total = 0
    for jid, deadline, profit in jobs:
        for t in range(min(deadline, max_deadline), 0, -1):  # latest free slot
            if slot[t] is None:
                slot[t] = jid
                count += 1
                total += profit
                break
    return count, total


# Test
jobs = [(1,4,20),(2,1,10),(3,1,40),(4,1,30)]
print(job_sequencing(jobs))   # (2, 60)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Greedy + slot array | O(n log n + n·d) | O(d) |
| Greedy + DSU | O(n log n) | O(d) |

---

## Edge Cases to remember

- **Sort by profit DESC** — the greedy driver; take richer jobs first.
- **Latest free slot ≤ deadline** — scanning backward keeps earlier slots for tight deadlines; scanning forward is wrong.
- **1-indexed slots** (deadline = time unit) — off-by-one is a common bug.
- **DSU speedup** — `find(deadline)` returns the latest free slot; union it to `slot−1` after use.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Task Scheduler (LC 621) | Greedy + heap |
| 2 | Course Schedule III (LC 630) | Deadline greedy + heap |
| 3 | Maximum Profit in Job Scheduling (LC 1235) | DP + binary search |
| 4 | N Meetings (Q9) | Interval greedy |
| 5 | Minimum Refuel Stops (LC 871) | Greedy + heap |
| 6 | Assign cookies (LC 455) | Greedy match |
| 7 | Two City Scheduling (LC 1029) | Greedy by delta |
| 8 | Reduce array size (LC 1338) | Greedy sort |

---

## The ONE trick to remember

```
"SORT BY PROFIT DESC; PLACE EACH JOB IN THE LATEST FREE SLOT ≤ ITS DEADLINE."
```

Grab the most profitable jobs first, and schedule each as late as possible (nearest its deadline) so earlier slots stay open for jobs with tighter deadlines. A DSU makes the latest-free-slot lookup near O(1).

> Memory hook: "Richest first, and park it as late as its deadline allows."

---
