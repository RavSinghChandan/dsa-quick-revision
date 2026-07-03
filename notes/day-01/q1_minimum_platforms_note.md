# ⚡ Quick Note · Day 01 · Q1 · Minimum Platforms
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "At peak time, how many trains are at station simultaneously?"

---

## Pattern
`Sort + Two Pointer on arrival/departure arrays`

---

## Visual in your head
```
arr sorted:  [900, 940, 950, 1100, 1500, 1800]
dep sorted:  [910, 1120, 1130, 1200, 1900, 2000]
                 ↑ i                ↑ j

if arr[i] <= dep[j]  →  train IN   → platforms++
else                 →  train OUT  → platforms--
track max at each step
```

---

## The trick (say it out loud)
> "Sort BOTH arrays SEPARATELY. Walk with i on arrivals, j on departures."

---

## Code skeleton (3 lines to recall)
```python
arr.sort(); dep.sort()
if arr[i] <= dep[j]: platforms += 1; i++
else: platforms -= 1; j++
```

---

## Complexity
- Time: O(n log n) · Space: O(1)

---

## This trick solves 10 more questions
Meeting Rooms II · Car Pooling · Merge Intervals · Non-overlapping Intervals · Max CPU Load

---
