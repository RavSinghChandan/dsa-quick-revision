# Q3 — N Meetings in One Room (2-min card)

**Pick the maximum number of non-overlapping meetings from a list using one room.**

**Pattern:** Greedy — Activity Selection (sort by end time)

```
Meetings: (1,2) (3,4) (0,6) (5,7) (8,9) (5,9)
Sort by end →  end=2  end=4  end=6  end=7  end=9  end=9

Pick (1,2)  last_end=2
Pick (3,4)  3>=2 ✓ last_end=4
Skip (0,6)  0<4  ✗
Pick (5,7)  5>=4 ✓ last_end=7
Pick (8,9)  8>=7 ✓ last_end=9
→ 4 meetings
```

**The trick:** "Pick the meeting that ends first" — finishing early leaves maximum room for future meetings.

```python
meetings = sorted(zip(end, start, range(n)))  # sort by end time
last_end = meetings[0][0]; count = 1
for e, s, _ in meetings[1:]:
    if s >= last_end: count += 1; last_end = e
```

**Complexity:** Time O(n log n) | Space O(n)

**Same pattern solves:** Non-overlapping Intervals (LC 435), Burst Balloons arrows (LC 452), Meeting Rooms I (LC 252), Merge Intervals (LC 56), Insert Interval (LC 57), Weighted Job Scheduling (LC 1235), Task scheduler.
