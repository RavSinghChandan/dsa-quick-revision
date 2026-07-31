# Day 12 · Q4 · Merge Overlapping Intervals  ·  [LISTS]

---

## What is this question actually asking?

Given a list of intervals `[start, end]`, merge all the ones that overlap.

```
[[1,3],[2,6],[8,10],[15,18]] → [[1,6],[8,10],[15,18]]
   [1,3] & [2,6] overlap → [1,6]
```

> A staple. Tests: sort first, then a single sweep comparing "current end" vs "next start." Real use: merging time ranges, deduping date spans, combining prediction windows.

---

## Pattern

```
PATTERN: Sort by start, then linear sweep merging into the last kept interval
```

Whenever you see: *"combine/overlapping ranges"* → **sort by start**, keep a result list, and for each interval either extend the last one (overlap) or append a new one (gap).

---

## Understand with a diagram

```
Sort by start: [1,3] [2,6] [8,10] [15,18]

result = [[1,3]]
[2,6]: start 2 <= last end 3  → OVERLAP → extend end to max(3,6)=6 → [[1,6]]
[8,10]: start 8 > last end 6  → GAP    → append → [[1,6],[8,10]]
[15,18]: 15 > 10 → GAP → append → [[1,6],[8,10],[15,18]]
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — repeatedly merge pairs
Scan for any overlapping pair, merge, restart, until no overlaps → O(n²) or worse.

### 🟢 Optimal — Sort + one sweep — O(n log n)
Sorting by start guarantees that any interval overlapping the current group starts before the current group's end. So a single pass merges everything.

---

## Pseudocode (Optimal)

```
function merge(intervals):
    sort intervals by start
    result = [ first interval ]
    for each interval after the first:
        last = result[-1]
        if interval.start <= last.end:      ← overlap
            last.end = max(last.end, interval.end)
        else:                               ← gap
            result.append(interval)
    return result
```

---

## Python Code

```python
def merge_intervals(intervals: list[list[int]]) -> list[list[int]]:
    if not intervals:
        return []
    intervals.sort(key=lambda x: x[0])       # sort by start
    result = [intervals[0]]
    for start, end in intervals[1:]:
        last = result[-1]
        if start <= last[1]:                 # overlap
            last[1] = max(last[1], end)      # extend end
        else:
            result.append([start, end])      # gap → new interval
    return result


# Test
print(merge_intervals([[1,3],[2,6],[8,10],[15,18]]))
# [[1, 6], [8, 10], [15, 18]]
print(merge_intervals([[1,4],[4,5]]))   # [[1, 5]]  (touching = overlap)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Sort + sweep | O(n log n) | O(n) result (O(1) extra if in place) |

---

## Edge Cases to remember

- **Touching intervals** `[1,4],[4,5]` → treat as overlapping (`<=`) → `[1,5]`. Confirm the boundary rule with interviewer.
- **Already sorted / single interval** → still correct.
- **Unsorted input** → the sort is mandatory; forgetting it is the classic bug.
- **`max` on the end** — the next interval may end *before* the current group's end (nested) → keep the larger.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Insert Interval (LC 57) | Merge one into a sorted set |
| 2 | Non-overlapping Intervals (LC 435) | Count/remove overlaps |
| 3 | Meeting Rooms / Rooms II (LC 252/253) | Overlap detection |
| 4 | Interval List Intersections (LC 986) | Two-pointer on two lists |
| 5 | Minimum Platforms (Day 1 Q1) | Overlap counting |
| 6 | Employee Free Time | Merge then find gaps |
| 7 | Merge date ranges in a report | Same |
| 8 | Car Pooling (LC 1094) | Sweep line |

---

## The ONE trick to remember

```
"SORT BY START, THEN EXTEND-OR-APPEND AGAINST THE LAST INTERVAL."
```

After sorting by start, one pass is enough: if the new interval starts at or before the last one's end, extend the last end (`max`); otherwise it's a fresh interval. Sorting is what makes the single sweep valid.

> Memory hook: "Line them up, then either stretch the last box or start a new one."

---
