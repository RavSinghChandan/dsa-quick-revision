# Day 5 — Stack, Queue & Greedy · Q4 · Trapping Rain Water

---

## What is this question actually asking?

Given bar heights, compute how much **rainwater** is trapped between them.

```
[0,1,0,2,1,0,1,3,2,1,2,1] → 6
```

> A must-know. Best solution is the elegant **two-pointer** O(n)/O(1). Also solvable with a monotonic stack.

---

## Pattern

```
PATTERN: Two Pointers with running leftMax / rightMax
```

Whenever you see: *"water trapped / area bounded by walls"* → water above each bar = `min(maxLeft, maxRight) − height`. Two pointers move inward, always processing the shorter side (whose bound is known).

---

## Understand with a diagram

```
water[i] = min(leftMax[i], rightMax[i]) - height[i]

Two pointers l, r with leftMax, rightMax:
  if height[l] <= height[r]:
       leftMax known is the binding wall → water += leftMax - height[l]; l++
  else:
       rightMax is binding → water += rightMax - height[r]; r--

The shorter wall bounds the water, so we can commit that side safely.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — for each bar scan both ways — O(n²)
Find left/right max per index.

### 🟡 Better — prefix leftMax + suffix rightMax arrays — O(n) time, O(n) space
Precompute both, sum `min(L,R)-h`.

### 🟢 Optimal — two pointers — O(n) time, O(1) space
Move the pointer on the smaller side; its max so far is the true bound.

---

## Pseudocode (Optimal)

```
function trap(height):
    l, r = 0, n-1
    leftMax = rightMax = 0
    water = 0
    while l < r:
        if height[l] <= height[r]:
            leftMax = max(leftMax, height[l])
            water += leftMax - height[l]
            l++
        else:
            rightMax = max(rightMax, height[r])
            water += rightMax - height[r]
            r--
    return water
```

---

## Python Code

```python
def trap(height: list[int]) -> int:
    l, r = 0, len(height) - 1
    left_max = right_max = 0
    water = 0
    while l < r:
        if height[l] <= height[r]:
            left_max = max(left_max, height[l])
            water += left_max - height[l]     # bounded by left wall
            l += 1
        else:
            right_max = max(right_max, height[r])
            water += right_max - height[r]     # bounded by right wall
            r -= 1
    return water


# Test
print(trap([0,1,0,2,1,0,1,3,2,1,2,1]))   # 6
print(trap([4,2,0,3,2,5]))               # 9
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(1) |
| Prefix arrays | O(n) | O(n) |
| Two pointers | O(n) | O(1) |

---

## Edge Cases to remember

- **Process the SHORTER side** — its running max is the guaranteed bound; the taller side could still grow.
- **`<=` tie-break** — either side is fine when equal; pick one consistently.
- **No trapping** — monotonic heights → 0.
- **Water can't be negative** — the invariant guarantees `max ≥ height`, so no clamp needed.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Trapping Rain Water II (2D, LC 407) | Heap of boundary |
| 2 | Container With Most Water (LC 11) | Two pointers |
| 3 | Largest Rectangle Histogram (Q5) | Monotonic stack |
| 4 | Product of array except self | Prefix/suffix |
| 5 | Pour water simulation | Boundaries |
| 6 | Max area under skyline | Bounds |
| 7 | Sum of subarray ranges (Q3) | Boundaries |
| 8 | Rain over terrain | Two pointer |

---

## The ONE trick to remember

```
"water = min(leftMax, rightMax) − h. TWO POINTERS: ADVANCE THE SHORTER WALL."
```

The water above a bar is set by the smaller of the tallest walls to its left and right. Two pointers move inward, and the side with the smaller height is safe to commit because its running max is the binding wall.

> Memory hook: "The shorter wall decides the water — fill from the low side."

---
