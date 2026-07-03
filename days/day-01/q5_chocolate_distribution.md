# Q5 — Chocolate Distribution Problem

## 1. What is this question actually asking?

You have m students and n packets of chocolates, where each packet has a different number of chocolates in it. You need to distribute exactly m packets (one per student) such that the difference between the maximum and minimum chocolates received by any student is minimized. You must pick exactly m packets from the n available packets — not necessarily consecutive — and the rest go unused.

## 2. Pattern

Greedy — sort the array, then use a sliding window of size m to find minimum (max - min) in any window

## 3. Understand with a Diagram

```
Packets: [3, 4, 1, 9, 56, 7, 9, 12]    m = 5 students

Sort:    [1, 3, 4, 7, 9, 9, 12, 56]
index:    0  1  2  3  4  5   6   7

Sliding window of size m=5:
Window 1: [1, 3, 4, 7, 9]   max-min = 9-1   = 8
           ^              ^
          min            max

Window 2: [3, 4, 7, 9, 9]   max-min = 9-3   = 6
              ^           ^

Window 3: [4, 7, 9, 9, 12]  max-min = 12-4  = 8
                 ^        ^

Window 4: [7, 9, 9, 12, 56] max-min = 56-7  = 49
                    ^      ^

Minimum difference = min(8, 6, 8, 49) = 6
Answer: 6 (give packets [3,4,7,9,9])

Why does sorting work?
After sorting, the m closest values are always adjacent.
Spreading picks across non-adjacent positions can only increase the gap.
```

## 4. Brute Force → Better → Optimal

### Brute Force — Try all combinations of m packets
**Idea:** Try every possible way to choose m packets from n. For each combination, compute max - min and track the minimum.

```
min_diff = infinity
for each combination C of size m from packets:
    diff = max(C) - min(C)
    min_diff = min(min_diff, diff)
return min_diff
```

Why slow: C(n, m) combinations. For n=100, m=50, that's astronomical. O(C(n,m) * m).

### Better — Sort + DP
**Idea:** Sort the array. Now the minimum difference window must consist of adjacent elements (proven after sorting). Use DP to track minimum windows.

Actually, once you sort, DP reduces to the same sliding window. Sorting is the real insight.

### Optimal — Sort + Sliding Window
**Idea:** Sort the array. The optimal m packets must be a contiguous window in the sorted array (because any gap between non-adjacent elements only adds to the difference). Slide a window of size m across the sorted array and track minimum (arr[i+m-1] - arr[i]).

```
sort packets
min_diff = infinity

for i from 0 to n-m:
    diff = packets[i + m - 1] - packets[i]
    min_diff = min(min_diff, diff)

return min_diff
```

**Trace:**
```
packets = [3, 4, 1, 9, 56, 7, 9, 12], m = 5
sorted  = [1, 3, 4, 7, 9, 9, 12, 56]
n-m = 3, so i goes 0,1,2,3

i=0: packets[4]-packets[0] = 9-1 = 8
i=1: packets[5]-packets[1] = 9-3 = 6  ← minimum
i=2: packets[6]-packets[2] = 12-4 = 8
i=3: packets[7]-packets[3] = 56-7 = 49

Answer: 6
```

## 5. Pseudocode (Optimal)

```
function chocolateDistribution(packets[], m):
    sort packets in ascending order
    n = len(packets)
    
    if m >= n:
        return packets[n-1] - packets[0]  # take all, no choice
    
    min_diff = infinity
    
    for i from 0 to n-m (inclusive):
        diff = packets[i + m - 1] - packets[i]
        if diff < min_diff:
            min_diff = diff
    
    return min_diff
```

## 6. Python Code

```python
def chocolate_distribution(packets, m):
    if not packets or m == 0:
        return 0
    
    packets = sorted(packets)
    n = len(packets)
    
    if m >= n:
        return packets[-1] - packets[0]
    
    min_diff = float('inf')
    
    for i in range(n - m + 1):
        diff = packets[i + m - 1] - packets[i]
        min_diff = min(min_diff, diff)
    
    return min_diff


# Test 1 — standard example
packets = [3, 4, 1, 9, 56, 7, 9, 12]
m = 5
print(f"Min difference: {chocolate_distribution(packets, m)}")
# Min difference: 6

# Test 2 — m equals n (must take all)
packets2 = [1, 5, 10]
m2 = 3
print(f"Take all: {chocolate_distribution(packets2, m2)}")
# Take all: 9

# Test 3 — m = 1 (any single packet, diff = 0)
packets3 = [10, 20, 30]
m3 = 1
print(f"Single student: {chocolate_distribution(packets3, m3)}")
# Single student: 0

# Test 4 — all same values
packets4 = [5, 5, 5, 5]
m4 = 2
print(f"All same: {chocolate_distribution(packets4, m4)}")
# All same: 0

# Test 5 — two elements, m=2
packets5 = [3, 7]
m5 = 2
print(f"Two packets: {chocolate_distribution(packets5, m5)}")
# Two packets: 4
```

## 7. Complexity Table

| Approach        | Time           | Space | Notes                          |
|-----------------|----------------|-------|--------------------------------|
| Brute Force     | O(C(n,m) * m)  | O(m)  | Exponential, infeasible        |
| Sort + DP       | O(n log n)     | O(n)  | Reduces to sliding window      |
| Sort + Sliding  | O(n log n)     | O(1)  | Sorting dominates, O(1) extra  |

## 8. Edge Cases to Remember

- m = 1 — single student, any packet works, diff = 0
- m = n — must take all packets, answer = max - min of entire array
- m > n — impossible, usually stated m <= n in problem
- All packets equal — answer is 0
- Two packets — answer is abs(a - b) if m=2
- Very large values — use long/int64 if needed
- m = 0 — no students, return 0

## 9. Similar Questions

| Question                              | Where Pattern Applies                                | LeetCode # |
|---------------------------------------|------------------------------------------------------|------------|
| Minimum Difference Between Largest and Smallest in K Moves | Sort + greedy window | LC 1551    |
| Minimum Absolute Difference           | Sort, then check adjacent pairs                      | LC 1200    |
| Find K Closest Elements               | Sort + sliding window of size k                      | LC 658     |
| Smallest Range Covering K Lists       | Sliding window across sorted merged list             | LC 632     |
| Split Array Largest Sum               | Binary search on answer                              | LC 410     |
| Minimize Maximum Pair Sum in Array    | Sort + two pointers                                  | LC 1877    |
| Maximum Sum of Subsequence            | Sliding window after sorting                         | —          |
| Candy Distribution                    | Sort + greedy neighbors                              | LC 135     |

## 10. The ONE Trick to Remember

**"Sort first, then slide a window of size m — adjacent in sorted order means closest values."**

After sorting, the m elements with the minimum possible spread are always neighbors. There's no way to pick m elements that are "close together" without them being adjacent in a sorted arrangement — any gap in the sorted order only increases max-min. So sort once, slide once, done.
