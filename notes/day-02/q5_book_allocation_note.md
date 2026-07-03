# Q5 — Book Allocation Problem (2-min card)

**Allocate n books to m students (contiguous, each gets ≥1) minimizing the maximum pages any student reads.**

**Pattern:** Binary Search on Answer — binary search on max-pages, greedy count students

```
books=[12,34,67,90]  m=2

lo=90 (max book), hi=203 (sum all)
mid=146: 12+34+67=113<146, 90<146 → 2 students ✓ → hi=145, ans=146
mid=117: 12+34+67=113<117, 90<117 → 2 students ✓ → hi=116, ans=117
mid=103: need 3 students → infeasible → lo=104
...converges to 113
Answer = 113  [12,34,67] | [90]
```

**The trick:** "Search from max_book to sum_all; greedily count students for each candidate limit."

```python
lo, hi, ans = max(books), sum(books), sum(books)
while lo <= hi:
    mid = (lo+hi)//2
    cnt, cur = 1, 0
    for p in books:
        if cur+p > mid: cnt+=1; cur=p
        else: cur+=p
    if cnt <= m: ans=mid; hi=mid-1
    else: lo=mid+1
```

**Complexity:** Time O(n log S) where S=sum(books) | Space O(1)

**Same pattern solves:** Painter's Partition (GFG), Split Array Largest Sum (LC 410), Capacity to Ship (LC 1011), Aggressive Cows (SPOJ), Magnetic Force (LC 1552), Min Trips Time (LC 2187), Divide Chocolate (LC 1231).
