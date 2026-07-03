# Q3 — Largest Rectangle in Histogram (LeetCode 84)

## 1. What is this question actually asking?

You're given an array of bar heights representing a histogram. Find the area of the largest rectangle that can be formed within the histogram. A rectangle must be aligned with the base and can span multiple consecutive bars — but its height is limited by the shortest bar in the span. For example, heights [2,1,5,6,2,3] has largest rectangle area 10 (spanning bars 5 and 6).

## 2. Pattern

Monotonic Stack — maintain an increasing stack; when a shorter bar is found, pop and compute area using the popped bar as the height

## 3. Understand with a Diagram

```
heights = [2, 1, 5, 6, 2, 3]
index  =   0  1  2  3  4  5

Histogram visual:
            |
         |  |
         |  |        |
|        |  |  |     |
|  |     |  |  |     |
0  1     2  3  4     5

For each bar as potential minimum height:
Bar 2(h=1): extends from 0 to 5 → area = 1*6 = 6
Bar 0(h=2): extends from 0 to 0 → area = 2*1 = 2 (bounded by bar 1 on right)
Bar 2(h=5): extends from 2 to 3 → area = 5*2 = 10 ← MAXIMUM
Bar 3(h=6): extends from 3 to 3 → area = 6*1 = 6
Bar 4(h=2): extends from 1 to 5 → area = 2*5 = 10
Bar 5(h=3): extends from 4 to 5 → area = 3*2 = 6

Answer: 10

Monotonic Stack Trace (increasing):
Process each bar; stack stores indices:

i=0, h=2: stack empty, push 0. stack=[0]
i=1, h=1: h[1]=1 < h[0]=2 → pop 0
  width = 1 - (-1) - 1 = 1  (no left boundary, using -1 as sentinel)
  area = 2 * 1 = 2 → max_area=2
  stack empty, push 1. stack=[1]
i=2, h=5: 5>1, push 2. stack=[1,2]
i=3, h=6: 6>5, push 3. stack=[1,2,3]
i=4, h=2: 2<6 → pop 3, width=4-2-1=1, area=6*1=6 → max=6
          2<5 → pop 2, width=4-1-1=2, area=5*2=10 → max=10
          2>=1, push 4. stack=[1,4]
i=5, h=3: 3>2, push 5. stack=[1,4,5]

End of array, process remaining stack:
pop 5: width=6-4-1=1, area=3*1=3
pop 4: width=6-1-1=4, area=2*4=8 → max=10
pop 1: width=6-(-1)-1=6, area=1*6=6

Answer: 10 ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — Try all pairs
**Idea:** For every pair (i, j), find the minimum height in that range, compute area as min_height * (j-i+1).

```
max_area = 0
for i from 0 to n-1:
    for j from i to n-1:
        min_h = min(heights[i..j])
        max_area = max(max_area, min_h * (j - i + 1))
return max_area
```

Why slow: O(n³) or O(n²) if we track running minimum.

### Better — For each bar as min, expand outward
**Idea:** For each bar, extend left and right as far as the bar is still the minimum.

```
max_area = 0
for i from 0 to n-1:
    min_h = heights[i]
    for j from i to n-1:
        min_h = min(min_h, heights[j])
        max_area = max(max_area, min_h * (j - i + 1))
return max_area
```

O(n²) — better but still too slow for n=10^5.

### Optimal — Monotonic Stack
**Idea:** Process bars left to right. Maintain a stack of increasing bar indices. When a bar shorter than the stack top arrives, it defines the right boundary for the popped bar. The left boundary is the new stack top. Compute area and track maximum.

```
stack = []
max_area = 0
heights.append(0)  // sentinel to flush stack at end

for i from 0 to n:
    while stack and heights[stack.top()] > heights[i]:
        h = heights[stack.pop()]
        w = i if stack is empty else i - stack.top() - 1
        max_area = max(max_area, h * w)
    stack.push(i)

return max_area
```

O(n) — each index pushed and popped exactly once.

## 5. Pseudocode (Optimal)

```
function largestRectangleArea(heights[]):
    stack = []
    max_area = 0
    heights.append(0)  // sentinel
    n = len(heights)
    
    for i from 0 to n-1:
        while stack is not empty and heights[stack.top()] > heights[i]:
            h = heights[stack.pop()]
            w = i if stack is empty else i - stack.top() - 1
            max_area = max(max_area, h * w)
        stack.push(i)
    
    return max_area
```

## 6. Python Code

```python
def largestRectangleArea(heights):
    stack = []
    max_area = 0
    heights = heights + [0]  # sentinel triggers final flush
    
    for i, h in enumerate(heights):
        while stack and heights[stack[-1]] > h:
            height = heights[stack.pop()]
            width = i if not stack else i - stack[-1] - 1
            max_area = max(max_area, height * width)
        stack.append(i)
    
    return max_area


# Test 1 — LeetCode example 1
print(largestRectangleArea([2,1,5,6,2,3]))   # 10

# Test 2 — LeetCode example 2
print(largestRectangleArea([2,4]))            # 4

# Test 3 — all same height
print(largestRectangleArea([3,3,3,3]))        # 12

# Test 4 — single bar
print(largestRectangleArea([5]))              # 5

# Test 5 — monotonically increasing
print(largestRectangleArea([1,2,3,4,5]))      # 9 (3*3 or 2*4 or 1*5... check: 9 from 3+4+5 span)
# Actually: min(3,4,5)*3=9 or min(4,5)*2=8 or min(5)*1=5 → 9? No:
# [1,2,3,4,5]: for i=4,h=5: area=5. i=3,h=4: area=8. i=2,h=3: 3*3=9. i=1,h=2: 2*4=8. i=0,h=1: 1*5=5
print(largestRectangleArea([1,2,3,4,5]))      # 9

# Test 6 — decreasing
print(largestRectangleArea([5,4,3,2,1]))      # 9 (same by symmetry)
```

## 7. Complexity Table

| Approach              | Time  | Space | Notes                               |
|-----------------------|-------|-------|-------------------------------------|
| All pairs             | O(n³) | O(1)  | Triple loop                         |
| Running min in range  | O(n²) | O(1)  | Double loop                         |
| Monotonic Stack       | O(n)  | O(n)  | Each element pushed/popped once     |

## 8. Edge Cases to Remember

- Single bar — area = that bar's height
- All bars same height — area = height * n
- Strictly increasing bars — stack fills then flushes at sentinel
- Strictly decreasing bars — each bar pops immediately on next bar
- Sentinel (0 at end) is critical — it flushes remaining stack elements that never found a right boundary
- Width when stack is empty after pop = i (bar spans from index 0 to i-1)
- Width when stack is not empty = i - stack.top() - 1 (gap between boundaries)

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Maximal Rectangle                     | Apply histogram technique to each row               | LC 85      |
| Trapping Rain Water                   | Same pre-compute structure with stack               | LC 42      |
| Next Greater Element                  | Same monotonic stack direction                      | LC 496     |
| Sum of Subarray Minimums              | Monotonic stack for left/right smaller boundaries   | LC 907     |
| Maximum Width Ramp                    | Monotonic stack on indices                          | LC 962     |
| Buildings With Ocean View             | Monotonic stack suffix                              | LC 1762    |
| Stock Span Problem                    | Monotonic stack counting span                       | GFG        |
| Number of Visible People in Queue     | Monotonic stack count                               | LC 1944    |

## 10. The ONE Trick to Remember

**"Stack stores increasing heights; shorter bar arriving = right boundary for everything taller in stack."**

The key insight is that each bar wants to extend as far left and right as possible while being the minimum height. The monotonic stack gives you both boundaries: the right boundary is the current shorter bar (index i), and the left boundary is the new top of stack after popping (i - stack.top() - 1). Width is right boundary minus left boundary minus 1. Append a sentinel 0 to flush all remaining stack elements.
