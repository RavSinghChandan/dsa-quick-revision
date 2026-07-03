# Q5 — Chocolate Distribution Problem (2-min card)

**Pick m packets from n to minimize (max - min) chocolates among the chosen m.**

**Pattern:** Greedy — sort array, slide a window of size m

```
packets = [3,4,1,9,56,7,9,12]  m=5
sorted  = [1,3,4,7,9,9,12,56]

Window i=0: [1,3,4,7,9]   diff=8
Window i=1: [3,4,7,9,9]   diff=6  ← min
Window i=2: [4,7,9,9,12]  diff=8
Window i=3: [7,9,9,12,56] diff=49

Answer = 6
```

**The trick:** "Sort first, slide a window" — adjacent elements in sorted order are always the closest m values.

```python
packets.sort()
min_diff = float('inf')
for i in range(len(packets) - m + 1):
    min_diff = min(min_diff, packets[i+m-1] - packets[i])
```

**Complexity:** Time O(n log n) | Space O(1)

**Same pattern solves:** Find K Closest Elements (LC 658), Minimum Absolute Difference (LC 1200), Smallest Range Covering K Lists (LC 632), Minimize Max Pair Sum (LC 1877), K Closest Points (LC 973), Split Array Largest Sum (LC 410), Candy (LC 135).
