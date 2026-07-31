# Day 5 — Stack, Queue & Greedy · Q8 · Minimum Number of Platforms

---

## What is this question actually asking?

Given train **arrival** and **departure** times, find the minimum number of **platforms** so no train waits.

```
arr = [900, 940, 950, 1100, 1500, 1800]
dep = [910, 1200, 1120, 1130, 1900, 2000]  → 3
```

> "How many events overlap at once." Sort arrivals and departures separately and sweep with two pointers.

---

## Pattern

```
PATTERN: Sort + Two Pointer sweep (event counting)
```

Whenever you see: *"max things active at the same time"* → sort arrivals and departures independently; sweep a timeline: an arrival before the next departure needs a platform (+1), else one frees (−1). Track the peak.

---

## Understand with a diagram

```
sort arr = [900, 940, 950, 1100, 1500, 1800]
sort dep = [910, 1120, 1130, 1200, 1900, 2000]
            i (arr)          j (dep)

if arr[i] <= dep[j]: platforms++, i++     (a train comes before one leaves)
else:                platforms--, j++     (a train leaves first)
track max platforms

peak simultaneously present = 3
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — count overlaps per train — O(n²)
For each train, count trains present during its stay.

### 🟢 Optimal — sort both + two pointer — O(n log n)
Sort arrivals and departures separately, sweep, track the max concurrent count.

---

## Pseudocode (Optimal)

```
function min_platforms(arr, dep):
    sort arr; sort dep
    i = j = 0; platforms = 0; best = 0
    while i < n:
        if arr[i] <= dep[j]:
            platforms++; i++
        else:
            platforms--; j++
        best = max(best, platforms)
    return best
```

---

## Python Code

```python
def min_platforms(arr: list[int], dep: list[int]) -> int:
    arr.sort()
    dep.sort()
    n = len(arr)
    i = j = 0
    platforms = best = 0
    while i < n:
        if arr[i] <= dep[j]:        # a train arrives before/at a departure
            platforms += 1
            i += 1
        else:                       # a train departs first
            platforms -= 1
            j += 1
        best = max(best, platforms)
    return best


# Test
print(min_platforms([900,940,950,1100,1500,1800],
                    [910,1200,1120,1130,1900,2000]))   # 3
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Sort + sweep | O(n log n) | O(1) |

---

## Edge Cases to remember

- **Sort arr and dep SEPARATELY** — not as paired tuples; you're merging two independent event streams.
- **Tie: `arr[i] <= dep[j]`** — if a train arrives exactly when another departs, they DO overlap (need a platform) — use `<=`.
- **Loop on `i < n`** — arrivals drive the sweep; departures can't exceed arrivals processed.
- **Single train** → 1.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Meeting Rooms II (LC 253) | Same problem |
| 2 | Car Pooling (LC 1094) | Sweep line |
| 3 | Merge Intervals (Day 12 Q4) | Sort + sweep |
| 4 | Max CPU load | Overlap count |
| 5 | Employee Free Time | Gaps in sweep |
| 6 | Minimum arrows to burst balloons | Interval greedy |
| 7 | Non-overlapping intervals (LC 435) | Sort + greedy |
| 8 | Range module | Interval events |

---

## The ONE trick to remember

```
"SORT ARRIVALS & DEPARTURES SEPARATELY; SWEEP; PEAK CONCURRENT = ANSWER."
```

Two independent sorted streams. Each arrival before the next departure raises the platform count; each departure lowers it. The maximum count during the sweep is the minimum platforms needed.

> Memory hook: "Two tracks — sort each, walk together, watch the peak crowd."

---
