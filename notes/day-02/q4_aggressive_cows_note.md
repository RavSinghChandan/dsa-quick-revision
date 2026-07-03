# Q4 — Aggressive Cows (2-min card) — SPOJ AGGRCOW

**Place c cows in stalls to maximize the minimum distance between any two cows.**

**Pattern:** Binary Search on Answer — binary search on min-distance d, greedy feasibility check

```
Stalls=[1,2,4,8,9]  c=3   sorted already

Binary search d in [1..8]:
d=4: place at 1, next>=5→8, next>=12→none → 2 cows ✗ → hi=3
d=2: place at 1,4,8 → 3 cows ✓ → answer=2, lo=3
d=3: place at 1,4,8 → 3 cows ✓ → answer=3, lo=4
done → Answer = 3
```

**The trick:** "Binary search the minimum distance; greedy place cows to check if d is feasible."

```python
stalls.sort(); lo, hi, ans = 1, stalls[-1]-stalls[0], 0
while lo <= hi:
    mid = (lo+hi)//2
    # greedy: count cows placeable with min gap=mid
    cnt, last = 1, stalls[0]
    for s in stalls[1:]:
        if s-last >= mid: cnt+=1; last=s
    if cnt >= c: ans=mid; lo=mid+1
    else: hi=mid-1
```

**Complexity:** Time O(n log n + n log D) | Space O(1)

**Same pattern solves:** Book Allocation (GFG), Painter's Partition (GFG), Split Array Largest Sum (LC 410), Magnetic Force Between Balls (LC 1552), Capacity to Ship (LC 1011), Gas Station Min Distance (LC 774), Min Speed on Time (LC 1870).
