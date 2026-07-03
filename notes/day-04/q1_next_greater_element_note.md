# Q1 — Next Greater Element (2-min card) — LC 496

**For each number in nums1, find the first greater number to its right in nums2.**

**Pattern:** Monotonic Stack — decreasing stack; incoming larger element resolves waiting elements

```
nums2 = [1, 3, 4, 2]

val=1: stack=[1]
val=3: 3>1 → nge[1]=3, stack=[3]
val=4: 4>3 → nge[3]=4, stack=[4]
val=2: 2<4 → stack=[4,2]
remaining: nge[4]=-1, nge[2]=-1

nge = {1:3, 3:4, 4:-1, 2:-1}
nums1=[4,1,2] → [-1, 3, -1]
```

**The trick:** "Stack holds elements waiting for NGE — bigger incoming value resolves all smaller waiting ones."

```python
stack, nge = [], {}
for val in nums2:
    while stack and stack[-1] < val:
        nge[stack.pop()] = val
    stack.append(val)
while stack: nge[stack.pop()] = -1
return [nge[x] for x in nums1]
```

**Complexity:** Time O(n+m) | Space O(n)

**Same pattern solves:** NGE II Circular (LC 503), Daily Temperatures (LC 739), Largest Rectangle in Histogram (LC 84), Trapping Rain Water (LC 42), Sum Subarray Minimums (LC 907), Stock Span (GFG), Next Greater in LL (LC 1019).
