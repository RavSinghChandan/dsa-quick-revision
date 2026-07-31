# Day 5 — Stack, Queue & Greedy · Q9 · N Meetings in One Room

---

## What is this question actually asking?

Given meetings with start/end times, find the **maximum number of meetings** one room can host (no overlaps).

```
start = [1,3,0,5,8,5], end = [2,4,6,7,9,9] → 4
```

> The **activity selection** greedy: always pick the meeting that **ends earliest** among those that still fit.

---

## Pattern

```
PATTERN: Greedy — sort by END time, pick if it starts after the last chosen end
```

Whenever you see: *"maximum non-overlapping intervals / most activities"* → sort by finishing time; greedily take a meeting whenever its start is after the last selected meeting's end. Earliest finish leaves the most room for the rest.

---

## Understand with a diagram

```
pair (start,end), sort by end:
 (1,2)(3,4)(0,6)(5,7)(8,9)(5,9)   sorted by end → (1,2)(3,4)(0,6)(5,7)(8,9)(5,9)

last_end = -inf
(1,2): start1 > -inf → take, last_end=2
(3,4): start3 > 2 → take, last_end=4
(0,6): start0 <=4 → skip
(5,7): start5 > 4 → take, last_end=7
(8,9): start8 > 7 → take, last_end=9   → count 4
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — try all subsets — O(2^n)
Check every subset for non-overlap. Infeasible.

### 🟢 Optimal — greedy by end time — O(n log n)
Sort by end; sweep, taking any meeting starting strictly after the last end. Provably optimal (exchange argument).

---

## Pseudocode (Optimal)

```
function max_meetings(start, end):
    meetings = zip(start, end)
    sort meetings by end time
    count = 0; last_end = -infinity
    for (s, e) in meetings:
        if s > last_end:
            count++; last_end = e
    return count
```

---

## Python Code

```python
def max_meetings(start: list[int], end: list[int]) -> int:
    meetings = sorted(zip(start, end), key=lambda m: m[1])   # by END time
    count = 0
    last_end = float('-inf')
    for s, e in meetings:
        if s > last_end:          # starts after the last chosen meeting ends
            count += 1
            last_end = e
    return count


# Test
print(max_meetings([1,3,0,5,8,5], [2,4,6,7,9,9]))   # 4
print(max_meetings([10,12,20], [20,25,30]))         # 1
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Greedy | O(n log n) | O(n) for pairs |

---

## Edge Cases to remember

- **Sort by END, not start** — this is the crux of activity selection.
- **Strict `>`** — if the next meeting starts exactly when the last ends, they conflict (a room can't do both at the same instant); use `>` unless the problem says end-inclusive is fine, then `>=`.
- **Keep start & end paired** while sorting.
- **All overlapping** → answer 1.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Non-overlapping Intervals (LC 435) | Remove fewest = total − max kept |
| 2 | Minimum Arrows to Burst Balloons (LC 452) | Greedy by end |
| 3 | Meeting Rooms I (LC 252) | Overlap check |
| 4 | Maximum Length of Pair Chain (LC 646) | Same greedy |
| 5 | Job Sequencing (Q10) | Greedy scheduling |
| 6 | Course schedule with deadlines | Greedy |
| 7 | Interval scheduling maximization | Activity selection |
| 8 | Erase overlap intervals | Greedy by end |

---

## The ONE trick to remember

```
"SORT BY END TIME; GREEDILY TAKE THE EARLIEST-FINISHING COMPATIBLE MEETING."
```

Choosing the meeting that ends first always leaves maximum room for the remaining ones. Sort by end, sweep, and take any meeting starting after the last selected end.

> Memory hook: "Grab the meeting that frees the room soonest."

---
