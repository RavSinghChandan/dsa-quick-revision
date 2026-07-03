# Day 01 · Q1 · Minimum Number of Platforms Required for a Railway Station

---

## What is this question actually asking?

You have a railway station. Many trains arrive and depart.
You are given two arrays:
- `arr[]` → arrival times of trains
- `dep[]` → departure times of trains

Find the **minimum number of platforms** so that no train has to wait.

> Think of it like this: at any single moment in time, how many trains are present at the station simultaneously? The answer to that is your answer.

---

## Pattern

```
PATTERN: Sorting + Two Pointer (Merge-interval style)
```

This is a classic **"how many events overlap at the same time"** problem.
Whenever you see: *"how many things are active at the same time"* → think **sort arrivals + sort departures, walk with two pointers**.

---

## Understand with a diagram

```
Trains:   A    B    C    D    E    F
Arrival:  900  940  950  1100 1500 1800
Depart:   910  1200 1120 1130 1900 2000

Timeline:
  900 ──[A]──910
       940 ──────[B]─────────────1200
            950 ──[C]────1120
                  1100─[D]──1130
                                1500──[E]──1900
                                           1800─[F]──2000

At time 950–1120:  A is gone, B C D are all present → 3 platforms needed
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — O(n²)

**Idea:** For every train, count how many other trains overlap with it.

```
for each train i:
    count = 1
    for each train j (j ≠ i):
        if arr[j] <= dep[i] and arr[i] <= dep[j]:   ← they overlap
            count += 1
    max_platforms = max(max_platforms, count)
```

**Why slow?** Nested loop → O(n²). For n=10,000 that's 100 million checks.

---

### 🟡 Better — Think differently

What if we mark every minute +1 (train arrives) or -1 (train departs)?
Sum up and find the peak. Works but needs large array for time range. O(T) space.

---

### 🟢 Optimal — Sort + Two Pointer — O(n log n)

**Core insight:**
Sort arrivals separately. Sort departures separately.
Walk both arrays with two pointers.
- If next arrival ≤ current departure → a new train comes in, need +1 platform
- If next arrival > current departure → a train left first, -1 platform

```
Sort arr[]  → [900, 940, 950, 1100, 1500, 1800]
Sort dep[]  → [910, 1120, 1130, 1200, 1900, 2000]

i=0 (arr ptr), j=0 (dep ptr), platforms=0, max_platforms=0

Step 1: arr[0]=900 <= dep[0]=910  → train arrives first  → platforms=1, i=1, max=1
Step 2: arr[1]=940 <= dep[0]=910? NO (940>910) → train leaves → platforms=0, j=1
Step 3: arr[1]=940 <= dep[1]=1120 → arrives → platforms=1, i=2, max=1
Step 4: arr[2]=950 <= dep[1]=1120 → arrives → platforms=2, i=3, max=2
Step 5: arr[3]=1100 <= dep[1]=1120 → arrives → platforms=3, i=4, max=3
Step 6: arr[4]=1500 <= dep[1]=1120? NO → train leaves → platforms=2, j=2
Step 7: arr[4]=1500 <= dep[2]=1130? NO → train leaves → platforms=1, j=3
Step 8: arr[4]=1500 <= dep[3]=1200? NO → train leaves → platforms=0, j=4
Step 9: arr[4]=1500 <= dep[4]=1900 → arrives → platforms=1, i=5, max=3
Step 10: arr[5]=1800 <= dep[4]=1900 → arrives → platforms=2, i=6 (done)

Answer = max_platforms = 3 ✅
```

---

## Pseudocode (Optimal)

```
function minPlatforms(arr, dep):
    sort arr
    sort dep
    n = length of arr

    i = 1          ← start from second arrival
    j = 0          ← start from first departure
    platforms = 1  ← first train always needs 1
    max_platforms = 1

    while i < n and j < n:
        if arr[i] <= dep[j]:
            platforms += 1          ← new train came in
            i += 1
        else:
            platforms -= 1          ← a train left
            j += 1
        max_platforms = max(max_platforms, platforms)

    return max_platforms
```

---

## Python Code

```python
def minimum_platforms(arr: list[int], dep: list[int]) -> int:
    arr.sort()
    dep.sort()
    n = len(arr)

    platforms = 1
    max_platforms = 1
    i = 1
    j = 0

    while i < n and j < n:
        if arr[i] <= dep[j]:
            platforms += 1
            i += 1
        else:
            platforms -= 1
            j += 1
        max_platforms = max(max_platforms, platforms)

    return max_platforms


# Test
arr = [900, 940, 950, 1100, 1500, 1800]
dep = [910, 1200, 1120, 1130, 1900, 2000]
print(minimum_platforms(arr, dep))  # Output: 3
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute Force | O(n²) | O(1) |
| Optimal | O(n log n) | O(1) |

---

## Edge Cases to remember

- A train that departs at 910 and another arrives at 910 → they DO overlap (≤ not <)
- Single train → answer is always 1
- All trains arrive and depart without overlap → answer is 1

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Meeting Rooms II (LeetCode 253) | Exact same — meeting = train |
| 2 | Car Pooling (LeetCode 1094) | Events on number line |
| 3 | Non-overlapping Intervals (LeetCode 435) | Interval overlap counting |
| 4 | Insert Interval (LeetCode 57) | Merge sorted intervals |
| 5 | Merge Intervals (LeetCode 56) | Sort + merge overlapping |
| 6 | Maximum CPU Load | Max overlapping jobs |
| 7 | Employee Free Time | Gap between sorted intervals |
| 8 | Task Scheduler (LeetCode 621) | Frequency + overlap |
| 9 | Minimum Meeting Rooms | Same as platforms |
| 10 | Job Scheduling (LeetCode 1235) | Weighted interval scheduling |

---

## The ONE trick to remember

```
"SORT BOTH ARRAYS SEPARATELY, NOT TOGETHER"
```

If you sort `(arr[i], dep[i])` as pairs, you lose the ability to compare
independent arrival vs departure streams. Always sort arrivals and departures
as two separate arrays and walk them with two pointers.

> Memory hook: "Two trains on two separate tracks — sort each track, walk together"

---
