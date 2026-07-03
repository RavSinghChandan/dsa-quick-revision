# Q2 — Trapping Rain Water (2-min card) — LC 42

**Count units of water trapped between elevation bars after rain.**

**Pattern:** Two Pointers — process the shorter-max side; water = max_so_far - current_height

```
height = [4,2,0,3,2,5]  answer = 9

lo=0,hi=5 left_max=0 right_max=0
h[0]=4 > h[5]=5? No → left side (4<=5)
  left_max=4; lo=1
h[1]=2 <= h[5]=5 → water += 4-2=2; lo=2; total=2
h[2]=0 <= h[5]=5 → water += 4-0=4; lo=3; total=6
h[3]=3 <= h[5]=5 → water += 4-3=1; lo=4; total=7
h[4]=2 <= h[5]=5 → water += 4-2=2; lo=5; total=9
lo=hi → stop.  Answer=9 ✓
```

**The trick:** "Process the pointer with smaller max — that side determines water level, other side is always taller."

```python
lo, hi, lm, rm, total = 0, len(h)-1, 0, 0, 0
while lo < hi:
    if h[lo] <= h[hi]:
        if h[lo] >= lm: lm = h[lo]
        else: total += lm - h[lo]
        lo += 1
    else:
        if h[hi] >= rm: rm = h[hi]
        else: total += rm - h[hi]
        hi -= 1
return total
```

**Complexity:** Time O(n) | Space O(1)

**Same pattern solves:** Container With Most Water (LC 11), Largest Rectangle Histogram (LC 84), Maximal Rectangle (LC 85), Product Except Self (LC 238), Rain Water II (LC 407), Stock Span (GFG), Sum Subarray Ranges (LC 2104).
