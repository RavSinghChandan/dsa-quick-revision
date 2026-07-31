# Day 5 — Stack, Queue & Greedy · Q5 · Largest Rectangle in Histogram

---

## What is this question actually asking?

Given bar heights, find the area of the **largest rectangle** that fits under the histogram.

```
[2,1,5,6,2,3] → 10   (bars 5 and 6 → width 2 × height 5)
```

> The premier **monotonic stack** area problem. For each bar as the shortest, find how far it extends left and right.

---

## Pattern

```
PATTERN: Monotonic increasing stack — for each bar, width to first-smaller both sides
```

Whenever you see: *"largest rectangle / max area with a limiting height"* → each bar can be the rectangle's height; its width = distance between the previous-smaller and next-smaller bars. A monotonic stack finds these boundaries in O(n).

---

## Understand with a diagram

```
[2,1,5,6,2,3]

Keep an increasing stack of indices. When a shorter bar arrives, the popped bar's
rectangle is finalized:
   height = heights[popped]
   width  = current_index - stack_top_after_pop - 1   (next smaller = current, prev smaller = new top)

bar 6 popped by 2: height 6, width 1 → area 6
bar 5 popped by 2: height 5, width 2 → area 10  ← max
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — every pair — O(n²)
For each bar, expand left/right while ≥ its height.

### 🟢 Optimal — monotonic stack — O(n)
Push increasing indices. On a smaller bar, pop and compute each popped bar's max rectangle. A sentinel `0` at the end flushes the stack.

---

## Pseudocode (Optimal)

```
function largest_rectangle(heights):
    stack = []          ← increasing indices
    best = 0
    for i in 0..n (append a virtual 0 at the end):
        h = heights[i] if i<n else 0
        while stack and heights[stack[-1]] >= h:
            top = stack.pop()
            height = heights[top]
            width  = i if not stack else i - stack[-1] - 1
            best = max(best, height * width)
        stack.push(i)
    return best
```

---

## Python Code

```python
def largest_rectangle_area(heights: list[int]) -> int:
    stack = []              # increasing stack of indices
    best = 0
    n = len(heights)
    for i in range(n + 1):
        h = heights[i] if i < n else 0     # sentinel to flush
        while stack and heights[stack[-1]] >= h:
            top = stack.pop()
            height = heights[top]
            width = i if not stack else i - stack[-1] - 1
            best = max(best, height * width)
        stack.append(i)
    return best


# Test
print(largest_rectangle_area([2,1,5,6,2,3]))   # 10
print(largest_rectangle_area([2,4]))           # 4
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(1) |
| Monotonic stack | O(n) | O(n) |

---

## Edge Cases to remember

- **Sentinel 0 at the end** — forces every remaining bar to be popped and measured.
- **Width formula** — after popping, `width = i - stack[-1] - 1`; if the stack is empty, the bar extends from index 0 → `width = i`.
- **`>=` vs `>`** — using `>=` on pop keeps it simple; equal-height bars still yield correct max area.
- **Single bar** → area = its height.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Maximal Rectangle (LC 85) | Histogram per matrix row |
| 2 | Trapping Rain Water (Q4) | Boundaries |
| 3 | Sum of subarray minimums (LC 907) | Contribution |
| 4 | Max area of binary matrix | Row histograms |
| 5 | Sum of subarray ranges (Q3) | Prev/next smaller |
| 6 | Next/Previous smaller element | Monotonic stack |
| 7 | Stock span (LC 901) | Monotonic |
| 8 | Remove K digits (Q7) | Monotonic |

---

## The ONE trick to remember

```
"EACH BAR IS A RECTANGLE'S HEIGHT; WIDTH = PREV-SMALLER → NEXT-SMALLER."
```

Use an increasing monotonic stack. When a shorter bar appears, pop and finalize each taller bar's rectangle: its width spans from just after the previous smaller bar to just before the current one. A trailing sentinel flushes the rest.

> Memory hook: "Every bar dreams of being the tallest — measure how wide it can stretch before a shorter one stops it."

---
