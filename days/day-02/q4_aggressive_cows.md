# Q4 — Aggressive Cows (SPOJ AGGRCOW)

## 1. What is this question actually asking?

You have n stalls at various positions on a number line, and c cows to place in those stalls (one cow per stall). The cows are "aggressive" and want to be as far apart as possible. You want to place them so that the minimum distance between any two cows is maximized. In other words, find the best arrangement such that the closest pair of cows is as far apart as possible.

## 2. Pattern

Binary Search on Answer — binary search on the minimum distance, check feasibility with a greedy placement

## 3. Understand with a Diagram

```
Stalls: [1, 2, 8, 4, 9]    Cows: 3

Sort stalls: [1, 2, 4, 8, 9]
              0  1  2  3  4

We binary search on the answer: "minimum distance d"
d ranges from 1 to (max_stall - min_stall) = 9-1 = 8

Can we place 3 cows with min gap = 3?
Greedy: place first cow at stall[0]=1
  next stall with pos >= 1+3=4: stall[2]=4 → place 2nd cow
  next stall with pos >= 4+3=7: stall[3]=8 → place 3rd cow
  All 3 placed! → d=3 is feasible ✓

Can we place 3 cows with min gap = 4?
Greedy: place at 1
  next >= 1+4=5: stall[3]=8 → place 2nd cow
  next >= 8+4=12: none in [1,2,4,8,9] → only 2 placed → infeasible ✗

Binary search result:
lo=1, hi=8

d=4: infeasible → hi=3
d=2: feasible   → lo=3
d=3: feasible   → lo=4
lo=4 > hi=3 → answer = 3

Diagram of best placement:
[1]   2   [4]   8   [9]  ← d=3 doesn't work for 3rd
Actually from trace: [1, 4, 8] → gaps 3 and 4 → min gap = 3 ✓

Stall positions:
1     2     3     4     5     6     7     8     9
|     |           |                       |     |
Cow1              Cow2                   Cow3
←————3————→←————————————4————————————→
min gap = 3 → Answer = 3
```

## 4. Brute Force → Better → Optimal

### Brute Force — Try all placements
**Idea:** Try every possible assignment of c cows to n stalls, compute minimum pairwise distance for each, track maximum.

```
for each subset S of size c from stalls:
    min_dist = min pairwise distance in S
    answer = max(answer, min_dist)
```

Why slow: C(n,c) subsets, each O(c²) to check. Exponential.

### Better — Try all possible minimum distances
**Idea:** The answer must be one of the pairwise distances between stalls. Sort them and try each as the minimum distance.

```
sort stalls
distances = all pairwise distances (sorted)
for d in distances:
    if canPlace(stalls, c, d): answer = d
    else: break (distances only get larger)
```

Why better: O(n²) pairs, O(n) greedy check each → O(n³) total. Better than brute but still slow.

### Optimal — Binary Search on Answer
**Idea:** Binary search on the value of the minimum distance. For a given distance d, greedily check if c cows can be placed such that no two are within d of each other. The feasibility check is O(n) and binary search is O(log(max_dist)), so total O(n log(max_dist)).

```
sort stalls
lo = 1
hi = stalls[-1] - stalls[0]

while lo <= hi:
    mid = (lo + hi) // 2
    if canPlace(stalls, c, mid):
        answer = mid
        lo = mid + 1   // try larger distance
    else:
        hi = mid - 1   // distance too large

return answer

function canPlace(stalls, c, min_dist):
    count = 1
    last = stalls[0]
    for i from 1 to n-1:
        if stalls[i] - last >= min_dist:
            count += 1
            last = stalls[i]
            if count == c: return True
    return count >= c
```

**Trace:**
```
stalls=[1,2,4,8,9], c=3, lo=1, hi=8

mid=4: canPlace(d=4)? place at 1, next>=5: 8, next>=12: none → 2 cows < 3 → False → hi=3
mid=2: canPlace(d=2)? place at 1, next>=3: 4, next>=6: 8 → 3 cows ✓ → answer=2, lo=3
mid=3: canPlace(d=3)? place at 1, next>=4: 4, next>=7: 8 → 3 cows ✓ → answer=3, lo=4
lo=4 > hi=3 → return 3
```

## 5. Pseudocode (Optimal)

```
function aggressiveCows(stalls[], c):
    sort stalls
    lo = 1
    hi = stalls[-1] - stalls[0]
    answer = 0
    
    while lo <= hi:
        mid = (lo + hi) // 2
        if canPlace(stalls, c, mid):
            answer = mid
            lo = mid + 1
        else:
            hi = mid - 1
    
    return answer

function canPlace(stalls[], c, min_dist):
    count = 1
    last_pos = stalls[0]
    for i from 1 to len(stalls)-1:
        if stalls[i] - last_pos >= min_dist:
            count += 1
            last_pos = stalls[i]
    return count >= c
```

## 6. Python Code

```python
def can_place(stalls, c, min_dist):
    count = 1
    last = stalls[0]
    for i in range(1, len(stalls)):
        if stalls[i] - last >= min_dist:
            count += 1
            last = stalls[i]
            if count == c:
                return True
    return count >= c


def aggressive_cows(stalls, c):
    stalls = sorted(stalls)
    lo = 1
    hi = stalls[-1] - stalls[0]
    answer = 0
    
    while lo <= hi:
        mid = (lo + hi) // 2
        if can_place(stalls, c, mid):
            answer = mid
            lo = mid + 1
        else:
            hi = mid - 1
    
    return answer


# Test 1 — standard SPOJ example
print(aggressive_cows([1, 2, 8, 4, 9], 3))   # 3

# Test 2 — evenly spaced stalls
print(aggressive_cows([0, 3, 6, 9], 2))       # 9

# Test 3 — 2 cows, answer = max spread
print(aggressive_cows([1, 2, 4, 8, 9], 2))   # 8 (place at 1 and 9)

# Test 4 — all cows must go in all stalls
print(aggressive_cows([1, 5], 2))             # 4

# Test 5 — large gaps
print(aggressive_cows([1, 10, 100], 3))       # 9 (place at 1, 10, 100; min gap=9)
```

## 7. Complexity Table

| Approach              | Time                  | Space | Notes                              |
|-----------------------|-----------------------|-------|------------------------------------|
| Brute Force           | O(C(n,c) * c²)        | O(c)  | Exponential                        |
| Try all distances     | O(n³)                 | O(n²) | Enumerate pairwise distances       |
| Binary Search+Greedy  | O(n log n + n log D)  | O(1)  | D = max_stall - min_stall          |

## 8. Edge Cases to Remember

- c = 1 — any single stall works, distance = 0 (but usually c >= 2)
- c = 2 — answer is always stalls[-1] - stalls[0] (place at ends)
- c = n — must use all stalls, answer = minimum gap between adjacent stalls
- All stalls at same position — answer = 0
- Two stalls — place one at each end, answer = difference
- Very large stall positions — binary search handles since it's on value range

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Book Allocation Problem               | Binary search on max pages, greedy check            | GFG / LC 1011|
| Painter's Partition Problem           | Binary search on max painter load, greedy check     | GFG        |
| Split Array Largest Sum               | Binary search on answer, greedy check               | LC 410     |
| Minimize Max Distance to Gas Station  | Binary search on max gap                            | LC 774     |
| Capacity to Ship Packages             | Binary search on ship capacity                      | LC 1011    |
| Magnetic Force Between Balls          | Same as aggressive cows (maximize minimum distance) | LC 1552    |
| Minimum Speed to Arrive on Time       | Binary search on speed                              | LC 1870    |
| EKO (SPOJ)                            | Binary search on tree cut height                    | SPOJ       |

## 10. The ONE Trick to Remember

**"Binary search the answer, greedy check feasibility — maximize the minimum."**

Whenever a problem asks to maximize the minimum (or minimize the maximum) of something, binary search on that value. Then write a greedy check: "can I achieve this?" Greedy for placing cows is: place first cow at stall[0], then place next cow only when the gap from the last placed cow is >= d. If you place all c cows, d is feasible.
