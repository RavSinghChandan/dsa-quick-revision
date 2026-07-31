# Day 2 — Binary Search · Q2 · Koko Eating Bananas

---

## What is this question actually asking?

Koko eats bananas from `piles`. Each hour she picks one pile and eats up to `speed` bananas from it. Find the **minimum eating speed** so she finishes all piles within `h` hours.

```
piles = [3,6,7,11], h = 8 → 4   (at speed 4, total hours = 1+2+2+3 = 8)
```

> The archetype of **"binary search on the answer"** — the answer is a number in a range, and "can we do it at speed X?" is monotonic.

---

## Pattern

```
PATTERN: Binary Search on the Answer (minimize with a feasibility check)
```

Whenever you see: *"minimum/maximum X such that a condition holds"* and the condition is **monotonic** (works at X ⇒ works at all X on one side) → binary-search the value of X, using a `feasible(X)` check.

---

## Understand with a diagram

```
speed range: [1 .. max(piles)]
feasible(speed) = hours needed = Σ ceil(pile/speed) <= h ?

higher speed → fewer hours → monotonic:
  too slow (hours>h) → need MORE speed → lo = mid+1
  fast enough        → try to go SLOWER → hi = mid   (keep as candidate)

converges to the smallest feasible speed.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — try every speed 1..max — O(max · n)
Linear scan of speeds. Works but slow for large piles.

### 🟢 Optimal — binary search on speed — O(n log(max))
Search the speed in `[1, max(piles)]`; each check sums `ceil(pile/speed)` in O(n). Because feasibility is monotonic in speed, binary search finds the minimum.

---

## Pseudocode (Optimal)

```
function min_speed(piles, h):
    lo, hi = 1, max(piles)
    while lo < hi:
        mid = (lo+hi)//2
        hours = sum(ceil(p/mid) for p in piles)
        if hours <= h: hi = mid        ← feasible, try slower
        else:          lo = mid+1      ← too slow, need faster
    return lo
```

---

## Python Code

```python
import math

def min_eating_speed(piles: list[int], h: int) -> int:
    def hours_at(speed: int) -> int:
        return sum((p + speed - 1) // speed for p in piles)   # ceil division

    lo, hi = 1, max(piles)
    while lo < hi:
        mid = (lo + hi) // 2
        if hours_at(mid) <= h:
            hi = mid            # feasible → try a smaller speed
        else:
            lo = mid + 1        # too slow → need faster
    return lo


# Test
print(min_eating_speed([3,6,7,11], 8))    # 4
print(min_eating_speed([30,11,23,4,20], 5))  # 30
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(max · n) | O(1) |
| Binary search on answer | O(n log(max pile)) | O(1) |

---

## Edge Cases to remember

- **Ceil division** — hours per pile is `ceil(p/speed)`, i.e. `(p + speed - 1)//speed`. Using floor is the classic bug.
- **Lower bound 1, upper bound max(piles)** — speed above max(piles) never helps (one pile per hour already).
- **`h >= len(piles)` always feasible** at speed max; if `h < len(piles)` it's impossible (each pile needs ≥1 hour).
- **`while lo < hi` + `hi = mid`** — the "find minimum feasible" template; don't use `mid-1` on the feasible side.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Aggressive Cows (Q3) | Max min-distance |
| 2 | Book Allocation / Split Array Largest Sum (LC 410) | Min largest part |
| 3 | Painter's Partition | Min max-time |
| 4 | Ship Packages in D Days (LC 1011) | Min capacity |
| 5 | Minimize Max Distance to Gas Station | BS on real answer |
| 6 | Smallest divisor given a threshold (LC 1283) | Min divisor |
| 7 | Capacity / rate problems | Feasibility BS |
| 8 | Minimum time to complete tasks | BS on time |

---

## The ONE trick to remember

```
"BINARY-SEARCH THE ANSWER; feasible(X) IS MONOTONIC."
```

When asked for the min/max value satisfying a monotonic condition, don't scan — binary search the value and test feasibility. Here: smaller speed ⇒ more hours; find the smallest speed with `hours <= h`.

> Memory hook: "Guess a speed, check the clock, squeeze toward the slowest that still finishes."

---
