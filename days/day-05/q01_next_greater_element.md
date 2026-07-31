# Day 5 — Stack, Queue & Greedy · Q1 · Next Greater Element

---

## What is this question actually asking?

For each element, find the **next element to its right that is greater** than it. If none, use `-1`.

```
[4, 5, 2, 25] → [5, 25, 25, -1]
[13, 7, 6, 12] → [-1, 12, 12, -1]
```

> The foundational **monotonic stack** problem. Once you own this, a dozen "next greater / smaller / span" problems fall.

---

## Pattern

```
PATTERN: Monotonic (decreasing) Stack
```

Whenever you see: *"next greater / next smaller / previous greater"* → keep a stack of indices whose answers are still pending. A new element resolves everything smaller than it on the stack.

---

## Understand with a diagram

```
[4, 5, 2, 25]   stack holds indices with no answer yet (decreasing values)

x=4  → stack empty → push 4
x=5  → 5>4 → pop 4, ans[4]=5 → push 5
x=2  → 2<5 → push 2
x=25 → 25>2 pop, ans[2]=25 ; 25>5 pop, ans[5]=25 → push 25
end  → remaining (25) get -1
→ [5,25,25,-1]
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — for each, scan right — O(n²)
Nested loop.

### 🟢 Optimal — monotonic stack — O(n)
Each element is pushed and popped at most once. When a bigger element arrives, it's the "next greater" for everything smaller waiting on the stack.

---

## Pseudocode (Optimal)

```
function next_greater(nums):
    ans = [-1] * n
    stack = []                     ← indices, values decreasing
    for i, x in enumerate(nums):
        while stack and nums[stack[-1]] < x:
            ans[stack.pop()] = x    ← x is the next greater for that index
        stack.push(i)
    return ans
```

---

## Python Code

```python
def next_greater_element(nums: list[int]) -> list[int]:
    n = len(nums)
    ans = [-1] * n
    stack = []                         # stack of indices (values decreasing)
    for i, x in enumerate(nums):
        while stack and nums[stack[-1]] < x:
            ans[stack.pop()] = x       # x resolves this index
        stack.append(i)
    return ans


# Test
print(next_greater_element([4, 5, 2, 25]))    # [5, 25, 25, -1]
print(next_greater_element([13, 7, 6, 12]))   # [-1, 12, 12, -1]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(1) |
| Monotonic stack | O(n) | O(n) |

---

## Edge Cases to remember

- **Store indices, not values** — you need to write `ans[index]`.
- **Circular version** (LC 503) → iterate `2n` times with `i % n`.
- **Next *smaller*** → flip the comparison to `>`.
- **Leftover stack** → those have no next greater → stay `-1`.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Next Greater Element I/II (LC 496/503) | Direct |
| 2 | Daily Temperatures (LC 739) | Next warmer day |
| 3 | Stock Span (LC 901) | Previous greater |
| 4 | Largest Rectangle Histogram (Q5) | Prev/next smaller |
| 5 | Trapping Rain Water (Q4) | Boundaries |
| 6 | Sum of Subarray Minimums (LC 907) | Span via monotonic stack |
| 7 | Remove K Digits (Q7) | Monotonic increasing |
| 8 | Asteroid Collision (Q2) | Stack simulation |

---

## The ONE trick to remember

```
"MONOTONIC STACK OF INDICES; A BIGGER ELEMENT RESOLVES ALL SMALLER WAITING ONES."
```

Keep indices on a decreasing stack. When a larger value comes, pop every smaller index and record it as their next-greater. Each index is pushed/popped once → O(n).

> Memory hook: "The stack is a queue of people waiting for someone taller."

---
