# Q2 — Trapping Rain Water (LeetCode 42)

## 1. What is this question actually asking?

You're given an array of non-negative integers representing an elevation map where each bar has width 1. Calculate how much rainwater can be trapped between the bars after raining. For example, [0,1,0,2,1,0,1,3,2,1,2,1] traps 6 units of water. Water at any position is bounded by the shorter of the tallest bar to its left and the tallest bar to its right, minus the bar's own height.

## 2. Pattern

Two Pointers — maintain left_max and right_max pointers; process from whichever side has smaller max height

## 3. Understand with a Diagram

```
height = [0,1,0,2,1,0,1,3,2,1,2,1]
index  =  0 1 2 3 4 5 6 7 8 9 A B

Visual:
              #
      #       ##  #
  #   # #   # ## ###
0 1 2 3 4 5 6 7 8 9 A B

Water trapped (marked with ~):
              #
      #~~~~~~~##~~#
  #~~~#~#~~~#~######
  ← water here →

Water at each index:
idx 0: min(0,3)-0 = 0
idx 1: min(1,3)-1 = 0
idx 2: min(1,3)-0 = 1
idx 3: min(2,3)-2 = 0
idx 4: min(2,3)-1 = 1
idx 5: min(2,3)-0 = 2
idx 6: min(2,3)-1 = 1
idx 7: min(3,3)-3 = 0
idx 8: min(3,2)-2 = 0  (right_max to right of 8 is 2)
Wait let me recalculate from the known answer of 6.

For each index i: water[i] = max(0, min(left_max[i], right_max[i]) - height[i])

left_max  = [0,0,1,1,2,2,2,2,3,3,3,3]
right_max = [3,3,3,3,3,3,3,3,2,2,2,1]
min(L,R)  = [0,0,1,1,2,2,2,2,2,2,2,1]
height    = [0,1,0,2,1,0,1,3,2,1,2,1]
water     = [0,0,1,0,1,2,1,0,0,1,0,0] → total = 6 ✓

Two-pointer approach avoids building the arrays:
lo=0, hi=11, left_max=0, right_max=0

If height[lo] <= height[hi]:
  left side is the constraint
  if height[lo] >= left_max: update left_max
  else: water += left_max - height[lo]
  lo++
else:
  right side is the constraint
  if height[hi] >= right_max: update right_max
  else: water += right_max - height[hi]
  hi--
```

## 4. Brute Force → Better → Optimal

### Brute Force — For each position scan both sides
**Idea:** For each position, scan left to find max bar, scan right to find max bar, compute water at that position.

```
total = 0
for i from 0 to n-1:
    left_max = max(height[0..i])
    right_max = max(height[i..n-1])
    total += max(0, min(left_max, right_max) - height[i])
return total
```

Why slow: O(n²) — scanning for each position.

### Better — Precompute left and right max arrays
**Idea:** Two passes: one from left to build left_max[], one from right to build right_max[]. Then one more pass to compute water.

```
left_max[0] = height[0]
for i from 1 to n-1:
    left_max[i] = max(left_max[i-1], height[i])

right_max[n-1] = height[n-1]
for i from n-2 to 0:
    right_max[i] = max(right_max[i+1], height[i])

total = 0
for i from 0 to n-1:
    total += max(0, min(left_max[i], right_max[i]) - height[i])
return total
```

O(n) time, O(n) space.

### Optimal — Two Pointers (no extra space)
**Idea:** Use two pointers from both ends. The side with the smaller max is the bottleneck — we can safely compute water for that side. Move that pointer inward.

```
lo = 0, hi = n-1
left_max = right_max = 0
total = 0

while lo < hi:
    if height[lo] <= height[hi]:
        // left is bottleneck
        if height[lo] >= left_max:
            left_max = height[lo]
        else:
            total += left_max - height[lo]
        lo++
    else:
        // right is bottleneck
        if height[hi] >= right_max:
            right_max = height[hi]
        else:
            total += right_max - height[hi]
        hi--

return total
```

O(n) time, O(1) space.

**Trace (abbreviated):**
```
height = [0,1,0,2,1,0,1,3,2,1,2,1]
lo=0,hi=11, left_max=0, right_max=0, total=0

h[0]=0 <= h[11]=1: left bottleneck
  h[0]=0 < left_max=0: water+=0-0=0; lo=1
h[1]=1 > h[11]=1: right bottleneck (equal goes left, let's check: 1<=1 → left)
  h[1]=1 >= left_max=0: left_max=1; lo=2
h[2]=0 <= h[11]=1: left bottleneck
  h[2]=0 < left_max=1: water+=1-0=1; lo=3, total=1
...continues → total=6
```

## 5. Pseudocode (Optimal)

```
function trap(height[]):
    lo = 0
    hi = len(height) - 1
    left_max = right_max = 0
    total = 0
    
    while lo < hi:
        if height[lo] <= height[hi]:
            if height[lo] >= left_max:
                left_max = height[lo]
            else:
                total += left_max - height[lo]
            lo += 1
        else:
            if height[hi] >= right_max:
                right_max = height[hi]
            else:
                total += right_max - height[hi]
            hi -= 1
    
    return total
```

## 6. Python Code

```python
def trap(height):
    lo, hi = 0, len(height) - 1
    left_max = right_max = 0
    total = 0
    
    while lo < hi:
        if height[lo] <= height[hi]:
            if height[lo] >= left_max:
                left_max = height[lo]
            else:
                total += left_max - height[lo]
            lo += 1
        else:
            if height[hi] >= right_max:
                right_max = height[hi]
            else:
                total += right_max - height[hi]
            hi -= 1
    
    return total


# Test 1 — classic LeetCode example
print(trap([0,1,0,2,1,0,1,3,2,1,2,1]))   # 6

# Test 2 — second LeetCode example
print(trap([4,2,0,3,2,5]))               # 9

# Test 3 — no water (monotonically increasing)
print(trap([1,2,3,4,5]))                 # 0

# Test 4 — no water (monotonically decreasing)
print(trap([5,4,3,2,1]))                 # 0

# Test 5 — valley
print(trap([3,0,3]))                     # 3

# Test 6 — single element
print(trap([5]))                         # 0
```

## 7. Complexity Table

| Approach              | Time  | Space | Notes                               |
|-----------------------|-------|-------|-------------------------------------|
| Brute Force           | O(n²) | O(1)  | Scan both sides for each position   |
| Precompute L/R arrays | O(n)  | O(n)  | Three passes, two arrays            |
| Two Pointers          | O(n)  | O(1)  | One pass — optimal                  |

## 8. Edge Cases to Remember

- Single or two elements — no water possible (need at least 3 elements for a valley)
- Strictly increasing or decreasing — no water trapped
- All same height — no water trapped
- Valley at both ends — no water at ends themselves, only inside
- Very large heights — use integer arithmetic, no overflow risk in Python
- Equal heights at lo and hi — handle by processing lo (either side is fine)

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Container With Most Water             | Two pointers maximize min height × width            | LC 11      |
| Largest Rectangle in Histogram        | Monotonic stack to find max area                    | LC 84      |
| Maximal Rectangle                     | Histogram technique row by row                      | LC 85      |
| Product of Array Except Self          | Left and right product arrays                       | LC 238     |
| Rain Water II (3D)                    | BFS + min-heap for 3D water trapping                | LC 407     |
| Sum of Subarray Ranges                | Precompute next smaller/greater                     | LC 2104    |
| Stock Span Problem                    | Monotonic stack span calculation                    | GFG        |
| Jump Game                             | Two pointer with condition                          | LC 55      |

## 10. The ONE Trick to Remember

**"Water level is determined by the shorter side — process whichever pointer has the smaller max."**

The water at any position is limited by the shorter of the two surrounding walls. The two-pointer approach exploits this: if left_max <= right_max, the left side sets the water level, and we can safely compute water for lo (even without knowing the full right side, because we know right is at least right_max which is >= left_max). Then move that pointer inward. Always process the constrained side.
