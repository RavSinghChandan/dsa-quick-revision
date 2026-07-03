# Q6 — Longest Increasing Subsequence (2-min card) — LC 300

**Length of longest strictly increasing subsequence (not necessarily consecutive).**

**Pattern:** O(n log n) Patience Sorting — maintain tails array; binary search to place/replace each element

```
nums=[10,9,2,5,3,7,101,18]

tails=[]
10→append→[10]
9<10→replace→[9]
2<9→replace→[2]
5>2→append→[2,5]
3>2,3<5→replace tails[1]→[2,3]
7>3→append→[2,3,7]
101>7→append→[2,3,7,101]
18>7,18<101→replace tails[3]→[2,3,7,18]

len(tails)=4 ✓
```

**The trick:** "Binary search for first tail >= num; replace it (or append if none) — tails length = LIS."

```python
from bisect import bisect_left
tails = []
for num in nums:
    pos = bisect_left(tails, num)
    if pos == len(tails): tails.append(num)
    else: tails[pos] = num
return len(tails)
```

**Complexity:** Time O(n log n) | Space O(n)

**Same pattern solves:** Russian Doll Envelopes (LC 354), Number of LIS (LC 673), Largest Divisible Subset (LC 368), Delete Columns Sorted III (LC 960), Increasing Triplet (LC 334), Bitonic Subsequence (GFG), Wiggle Subsequence (LC 376).
