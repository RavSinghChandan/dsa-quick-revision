# Q6 — Painter's Partition Problem (2-min card)

**Assign contiguous board sections to k painters (parallel) to minimize time = max painter load.**

**Pattern:** Binary Search on Answer — same template as Book Allocation

```
boards=[10,20,30,40]  k=2

lo=40 (max board), hi=100 (sum)
mid=70: [10,20,30]=60<70, +40>70 → 2 painters ✓ → hi=69, ans=70
mid=54: need 3 painters → lo=55
mid=62: 2 painters ✓ → hi=61, ans=62
mid=58: 3 painters → lo=59
mid=60: [10,20,30]=60 ✓ → 2 painters → hi=59, ans=60
lo>hi → Answer = 60   [10,20,30] | [40]
```

**The trick:** "lo=max(boards), hi=sum(boards); greedy count painters for each candidate load."

```python
lo, hi, ans = max(boards), sum(boards), sum(boards)
while lo <= hi:
    mid = (lo+hi)//2
    cnt, cur = 1, 0
    for b in boards:
        if cur+b > mid: cnt+=1; cur=b
        else: cur+=b
    if cnt <= k: ans=mid; hi=mid-1
    else: lo=mid+1
```

**Complexity:** Time O(n log S) | Space O(1)

**Same pattern solves:** Book Allocation (GFG), Split Array Largest Sum (LC 410), Capacity to Ship (LC 1011), Koko Eating Bananas (LC 875), Aggressive Cows (SPOJ), Magnetic Force (LC 1552), Min Number Days (LC 1885).
