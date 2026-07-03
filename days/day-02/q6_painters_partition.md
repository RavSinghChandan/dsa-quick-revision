# Q6 — Painter's Partition Problem

## 1. What is this question actually asking?

You have a row of boards and k painters. Each painter takes 1 unit of time per unit of board length, and can only paint a contiguous section of boards. All painters work simultaneously. You want to assign contiguous sections to painters to minimize the total time taken (which is the time taken by the slowest painter, since they work in parallel). This is identical in structure to the Book Allocation problem — just with painters and boards instead of students and books.

## 2. Pattern

Binary Search on Answer — binary search on maximum board length any one painter handles, greedy check how many painters are needed

## 3. Understand with a Diagram

```
Boards: [10, 20, 30, 40]    Painters: k=2

Each painter works simultaneously → time = max(painter's total boards)
Minimize that maximum.

Binary search range:
lo = max(boards) = 40   (min possible: one painter does the biggest board)
hi = sum(boards) = 100  (max possible: one painter does everything)

Can 2 painters finish with each doing max=60 boards?
  Painter 1: 10, 20 (total=30), 30 (total=60) → 60 ≤ 60 ✓
  Painter 2: 40 → 40 ≤ 60 ✓
  2 painters needed ✓ → 60 is feasible

Can 2 painters finish with max=50?
  Painter 1: 10, 20 (=30), 30 (=60 > 50) → stop at 20
             Take [10,20] = 30
  Painter 2: 30, 40 = 70 > 50 → need 3rd painter → infeasible ✗

Timeline visualization (answer=60):
time →  10   20   30   40   50   60
Painter1: [10][  20  ][      30      ]  done at 60
Painter2: [          40           ]      done at 40
                                    ↑
                             max time = 60

Answer: 60
```

## 4. Brute Force → Better → Optimal

### Brute Force — Try all partitions
**Idea:** Try every way to split boards into k contiguous groups. For each partition, compute max group sum (time taken by slowest painter), track minimum.

```
recursively try all (k-1) split points among n boards
answer = minimum of all maximum-group-sums found
```

Why slow: C(n-1, k-1) ways. Exponential.

### Better — DP
**Idea:** dp[i][j] = minimum possible maximum when first j boards are assigned to i painters.

```
dp[i][j] = min over p < j of:
            max(dp[i-1][p], sum(boards[p..j-1]))
```

Why better: O(k * n²). Polynomial but slow for large inputs.

### Optimal — Binary Search on Answer
**Idea:** Same pattern as Book Allocation. Binary search on the maximum load any painter handles. For each candidate max, greedily count painters needed.

```
sort boards (do NOT sort — boards are in a fixed order!)

lo = max(boards)
hi = sum(boards)

while lo <= hi:
    mid = (lo + hi) // 2
    if paintersNeeded(boards, mid) <= k:
        answer = mid
        hi = mid - 1
    else:
        lo = mid + 1

function paintersNeeded(boards, max_load):
    count = 1
    current = 0
    for length in boards:
        if current + length > max_load:
            count += 1
            current = length
        else:
            current += length
    return count
```

**Trace:**
```
boards=[10,20,30,40], k=2
lo=40, hi=100

mid=70: painters(70): 10+20+30=60<70, 60+40=100>70 → new painter (count=2), 40<70 → 2 painters ≤ 2 ✓
  answer=70, hi=69

mid=54: painters(54): 10+20+30=60>54 → new painter at 30 (count=2), 30+40=70>54 → count=3 > 2 ✗
  lo=55

mid=62: painters(62): 10+20+30=60<62, 60+40=100>62 → count=2 ≤ 2 ✓
  answer=62, hi=61

mid=58: 10+20+30=60>58 → count=2 at 30, 30+40=70>58 → count=3 > 2 ✗ → lo=59

mid=60: 10+20+30=60≤60, +40=100>60 → count=2 ✓ → answer=60, hi=59

lo=59>hi=59... wait:
mid=59: 10+20+30=60>59 → count=2 at 30, 30+40=70>59 → count=3 ✗ → lo=60

lo=60>hi=59... stop → return 60
```

## 5. Pseudocode (Optimal)

```
function paintersPartition(boards[], k):
    lo = max(boards)
    hi = sum(boards)
    answer = hi
    
    while lo <= hi:
        mid = (lo + hi) // 2
        if paintersNeeded(boards, mid) <= k:
            answer = mid
            hi = mid - 1
        else:
            lo = mid + 1
    
    return answer

function paintersNeeded(boards[], max_load):
    count = 1
    current = 0
    for length in boards:
        if current + length > max_load:
            count += 1
            current = length
        else:
            current += length
    return count
```

## 6. Python Code

```python
def painters_needed(boards, max_load):
    count = 1
    current = 0
    for length in boards:
        if current + length > max_load:
            count += 1
            current = length
        else:
            current += length
    return count


def painters_partition(boards, k):
    lo = max(boards)
    hi = sum(boards)
    answer = hi
    
    while lo <= hi:
        mid = (lo + hi) // 2
        if painters_needed(boards, mid) <= k:
            answer = mid
            hi = mid - 1
        else:
            lo = mid + 1
    
    return answer


# Test 1 — standard example
print(painters_partition([10, 20, 30, 40], 2))   # 60

# Test 2 — one painter (takes all)
print(painters_partition([10, 20, 30, 40], 1))   # 100

# Test 3 — four painters (each takes one board)
print(painters_partition([10, 20, 30, 40], 4))   # 40

# Test 4 — equal boards
print(painters_partition([10, 10, 10, 10], 2))   # 20

# Test 5 — more painters than boards
print(painters_partition([5, 10], 5))             # 10 (limited by boards)
```

## 7. Complexity Table

| Approach          | Time          | Space  | Notes                              |
|-------------------|---------------|--------|------------------------------------|
| Brute Force       | O(C(n-1,k-1)) | O(k)   | Exponential                        |
| DP                | O(k * n²)     | O(k*n) | Polynomial DP                      |
| Binary Search     | O(n log S)    | O(1)   | S = sum of all board lengths       |

## 8. Edge Cases to Remember

- k >= n — each board gets its own painter, answer = max(boards)
- k = 1 — one painter does all, answer = sum(boards)
- Single board — answer = boards[0] regardless of k
- Boards are NOT sorted — order matters! Don't sort, painters must paint contiguous sections
- Board length = 0 — unlikely but handled naturally (won't force a new painter)
- Very long single board — lo starts there, answer cannot be less than it

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Book Allocation Problem               | Identical — students/books instead of painters/boards| GFG       |
| Split Array Largest Sum               | Same binary search + greedy check                   | LC 410     |
| Capacity to Ship Packages             | Same — minimize max ship capacity over days         | LC 1011    |
| Minimum Number of Days to Complete Jobs| Binary search on days                              | LC 1885    |
| Divide Array in Sets of K Consecutive | Greedy partition                                   | LC 1296    |
| Aggressive Cows                       | Binary search — maximize minimum                    | SPOJ       |
| Magnetic Force Between Balls          | Maximize minimum distance                           | LC 1552    |
| Koko Eating Bananas                   | Binary search on eating speed                       | LC 875     |

## 10. The ONE Trick to Remember

**"Minimize the maximum — binary search lo=max(boards), hi=sum(boards); greedy count painters."**

The answer lives between the biggest single board (floor — can't do better) and the total sum (ceiling — one painter). Binary search this range. For each candidate max load, greedily count how many painters you need. If it fits in k painters, try smaller; otherwise go larger. The last valid value is your answer. (Identical template as Book Allocation — same code, different variable names.)
