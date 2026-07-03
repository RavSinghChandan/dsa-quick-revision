# Q3 — Largest Rectangle in Histogram (2-min card) — LC 84

**Find the largest rectangle area in a histogram.**

**Pattern:** Monotonic Stack (increasing) — shorter bar arriving = right boundary for all taller bars in stack

```
heights = [2,1,5,6,2,3] + [0 sentinel]

i=0,h=2: stack=[0]
i=1,h=1: 1<2 → pop 0: h=2, w=1(stack empty) area=2; stack=[1]
i=2,h=5: stack=[1,2]
i=3,h=6: stack=[1,2,3]
i=4,h=2: 2<6→pop3: h=6,w=4-2-1=1,area=6
          2<5→pop2: h=5,w=4-1-1=2,area=10 ← max!
          stack=[1,4]
i=5,h=3: stack=[1,4,5]
sentinel flushes rest
Answer = 10 ✓
```

**The trick:** "Stack top is the height; right bound = current i; left bound = new stack top; width = i - left - 1."

```python
heights.append(0)  # sentinel
stack, max_area = [], 0
for i, h in enumerate(heights):
    while stack and heights[stack[-1]] > h:
        height = heights[stack.pop()]
        width = i if not stack else i - stack[-1] - 1
        max_area = max(max_area, height * width)
    stack.append(i)
return max_area
```

**Complexity:** Time O(n) | Space O(n)

**Same pattern solves:** Maximal Rectangle (LC 85), Trapping Rain Water (LC 42), Next Greater Element (LC 496), Sum Subarray Mins (LC 907), Stock Span (GFG), Max Width Ramp (LC 962), Buildings Ocean View (LC 1762).
