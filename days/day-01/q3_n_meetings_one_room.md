# Q3 — N Meetings in One Room (Activity Selection)

## 1. What is this question actually asking?

You have one meeting room and a list of meetings, each with a start time and end time. You can only hold one meeting at a time. Your goal is to figure out the maximum number of meetings you can hold in that single room. Two meetings can't overlap — the next one must start only after the previous one has ended (or at the exact same time it ends).

## 2. Pattern

Greedy — Activity Selection (sort by end time, greedily pick non-overlapping activities)

## 3. Understand with a Diagram

```
Meetings (start, end):
M1: (1, 2)
M2: (3, 4)
M3: (0, 6)
M4: (5, 7)
M5: (8, 9)
M6: (5, 9)

Timeline:
0   1   2   3   4   5   6   7   8   9
    [M1-]
            [M2-]
[------M3---------]
                    [--M4--]
                                [M5-]
                    [------M6-------]

Sort by END time:
M1(end=2), M2(end=4), M3(end=6), M4(end=7), M5(end=9), M6(end=9)

Greedy picks:
Pick M1(1,2)  → last_end = 2
Pick M2(3,4)  → 3 >= 2, pick  → last_end = 4
Skip M3(0,6)  → 0 < 4, overlap
Pick M4(5,7)  → 5 >= 4, pick  → last_end = 7
Skip M6(5,9)  → 5 < 7, overlap
Pick M5(8,9)  → 8 >= 7, pick  → last_end = 9

Result: 4 meetings [M1, M2, M4, M5]
```

## 4. Brute Force → Better → Optimal

### Brute Force — Try all subsets
**Idea:** Generate every subset of meetings, check if the subset has zero overlapping meetings, track the maximum valid subset size.

```
for each subset S of meetings:
    if no two meetings in S overlap:
        answer = max(answer, len(S))
return answer
```

Why slow: There are 2^n subsets. For n=20 that's over a million checks. O(2^n * n).

### Better — Sort + DP
**Idea:** Sort meetings by end time. For each meeting, either skip it or take it (but only if it doesn't overlap the last taken meeting). Use DP table.

```
sort meetings by end time
dp[i] = max meetings using first i meetings
for i from 1 to n:
    dp[i] = dp[i-1]  (skip meeting i)
    j = last meeting that ends before meeting i starts
    dp[i] = max(dp[i], dp[j] + 1)
return dp[n]
```

Why better: O(n log n). But you need binary search to find j efficiently. DP table takes O(n) space.

### Optimal — Greedy (Activity Selection)
**Idea:** Sort by end time. Always pick the meeting that ends earliest among those that don't conflict with the last picked meeting. This leaves the maximum remaining time for future meetings.

```
sort meetings by end time
count = 1
last_end = meetings[0].end

for i from 1 to n-1:
    if meetings[i].start >= last_end:
        count += 1
        last_end = meetings[i].end

return count
```

Why optimal: Picking the earliest-ending meeting never hurts — it frees up maximum future time. This greedy choice is provably optimal (exchange argument proof).

**Trace on example:**
```
Sorted: M1(1,2), M2(3,4), M3(0,6), M4(5,7), M6(5,9), M5(8,9)

i=0: Pick M1. count=1, last_end=2
i=1: M2.start=3 >= last_end=2 → Pick. count=2, last_end=4
i=2: M3.start=0 < last_end=4 → Skip
i=3: M4.start=5 >= last_end=4 → Pick. count=3, last_end=7
i=4: M6.start=5 < last_end=7 → Skip
i=5: M5.start=8 >= last_end=7 → Pick. count=4, last_end=9

Answer: 4
```

## 5. Pseudocode (Optimal)

```
function maxMeetings(start[], end[], n):
    create list of (start[i], end[i], index i+1) for all i
    sort list by end time
    
    count = 1
    last_end = list[0].end
    result = [list[0].index]
    
    for i from 1 to n-1:
        if list[i].start >= last_end:
            count += 1
            last_end = list[i].end
            result.append(list[i].index)
    
    return count, result
```

## 6. Python Code

```python
def max_meetings(start, end):
    n = len(start)
    # Pair up: (start, end, original_index)
    meetings = sorted(zip(end, start, range(1, n+1)))
    # sorted by end time; zip(end, start,...) puts end first for sort
    
    count = 1
    last_end = meetings[0][0]
    selected = [meetings[0][2]]
    
    for i in range(1, n):
        e, s, idx = meetings[i]
        if s >= last_end:
            count += 1
            last_end = e
            selected.append(idx)
    
    return count, selected


# Test
start = [1, 3, 0, 5, 8, 5]
end   = [2, 4, 6, 7, 9, 9]
count, order = max_meetings(start, end)
print(f"Max meetings: {count}")          # Max meetings: 4
print(f"Meeting order (1-indexed): {order}")  # [1, 2, 4, 5]


# Edge case: single meeting
print(max_meetings([1], [2]))   # (1, [1])

# Edge case: all overlap
start2 = [1, 1, 1]
end2   = [5, 5, 5]
print(max_meetings(start2, end2))  # (1, [1])

# Edge case: back-to-back (end = next start)
start3 = [1, 2, 3]
end3   = [2, 3, 4]
count3, _ = max_meetings(start3, end3)
print(f"Back-to-back: {count3}")   # 3 (all fit)
```

## 7. Complexity Table

| Approach   | Time       | Space  | Notes                             |
|------------|------------|--------|-----------------------------------|
| Brute Force| O(2^n * n) | O(n)   | Enumerate all subsets             |
| DP         | O(n log n) | O(n)   | Sort + weighted interval schedule |
| Greedy     | O(n log n) | O(n)   | Sort is the bottleneck            |

## 8. Edge Cases to Remember

- Meeting where start == end (instant meeting) — valid, counts as 1
- Next meeting starting exactly when previous ends — this is allowed (start >= last_end)
- All meetings have same end time — only one gets picked
- Meetings given unsorted — must sort first, don't assume input is sorted
- Single meeting — answer is always 1
- All meetings back-to-back (non-overlapping already) — greedy picks all of them
- Meetings with same start but different end — pick the one that ends earlier

## 9. Similar Questions

| Question                              | Where Pattern Applies                          | LeetCode # |
|---------------------------------------|------------------------------------------------|------------|
| Activity Selection Problem            | Classic greedy — this exact problem            | —          |
| Non-overlapping Intervals             | Remove min intervals to make rest non-overlap  | LC 435     |
| Minimum number of arrows to burst balloons | Greedy by end coordinate                  | LC 452     |
| Meeting Rooms I                       | Can one person attend all meetings?            | LC 252     |
| Meeting Rooms II                      | Min rooms needed for all meetings              | LC 253     |
| Insert Interval                       | Merge a new interval into sorted list          | LC 57      |
| Merge Intervals                       | Merge all overlapping intervals                | LC 56      |
| Job Scheduling to Maximize Profit     | Weighted activity selection with DP            | LC 1235    |

## 10. The ONE Trick to Remember

**"Always pick the meeting that ends first."**

When you finish early, you leave the most time open for future meetings. Any other choice (picking a meeting that ends later) can only reduce your options. This is the greedy exchange argument — swapping any selected meeting for an earlier-ending one never hurts, so always choosing earliest-end is safe.
