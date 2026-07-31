# Day 1 — Arrays · Q5 · Find the Repeating and Missing Number

---

## What is this question actually asking?

An array holds numbers `1..n`, but **one number repeats** and **one is missing**. Find both.

```
[3, 1, 2, 5, 3] (n=5) → repeating = 3, missing = 4
```

> Tests turning two unknowns into two equations, or the XOR bit-partition trick — O(n), O(1).

---

## Pattern

```
PATTERN: Math (sum & sum-of-squares equations)  OR  XOR partition
```

Whenever you see: *"one repeats, one missing in 1..n"* → set up equations from the difference between actual and expected sums, or use XOR to isolate the two differing numbers.

---

## Understand with a diagram

```
Let repeating = x, missing = y.
S  = sum(arr)      ; expected Sn  = n(n+1)/2
P  = sum(arr²)     ; expected Pn  = n(n+1)(2n+1)/6

S  - Sn = x - y            (eq1)
P  - Pn = x² - y² = (x-y)(x+y)
        → (P-Pn)/(S-Sn) = x + y   (eq2)

Solve eq1 & eq2 → x and y.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — count each 1..n — O(n²)
For each value 1..n, count occurrences. Slow.

### 🟡 Better — frequency array — O(n) time, O(n) space
Count array of size n+1; value with count 2 repeats, count 0 missing.

### 🟢 Optimal — Two equations (sum & sum²) — O(n), O(1)
Two equations, two unknowns. No extra array.

---

## Pseudocode (Optimal — math)

```
function find(nums, n):
    S  = sum(nums);          Sn = n*(n+1)/2
    P  = sum(v*v for v);     Pn = n*(n+1)*(2n+1)/6
    diff = S - Sn            # = x - y
    summ = (P - Pn) / diff   # = x + y
    x = (diff + summ) / 2    # repeating
    y = x - diff             # missing
    return x, y
```

---

## Python Code

```python
def find_repeating_missing(nums: list[int]) -> tuple[int, int]:
    n = len(nums)
    S  = sum(nums)
    Sn = n * (n + 1) // 2
    P  = sum(v * v for v in nums)
    Pn = n * (n + 1) * (2 * n + 1) // 6

    diff = S - Sn                 # x - y
    summ = (P - Pn) // diff       # x + y
    x = (diff + summ) // 2        # repeating
    y = x - diff                  # missing
    return x, y


# Test
print(find_repeating_missing([3, 1, 2, 5, 3]))   # (3, 4)  → repeating 3, missing 4
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Frequency array | O(n) | O(n) |
| Math / XOR | O(n) | O(1) |

---

## Edge Cases to remember

- **Overflow** — sum-of-squares can overflow in fixed-width languages (Python is safe); mention using XOR in Java/C++.
- **Integer division** — `(diff+summ)` is always even here; use `//`.
- **`diff` could be negative** — `x - y` may be negative; the algebra still resolves x and y correctly.
- **XOR alternative** — XOR all array values with 1..n → gives `x^y`; use the lowest set bit to split into two groups and isolate each.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Missing Number (LC 268) | sum difference |
| 2 | Find the Duplicate Number (LC 287) | Floyd cycle / binary search |
| 3 | Single Number (LC 136) | XOR |
| 4 | Single Number III (Day 10) | XOR partition |
| 5 | Set Mismatch (LC 645) | Same problem |
| 6 | Find all numbers disappeared (LC 448) | Index marking |
| 7 | First missing positive (LC 41) | Index placement |
| 8 | Sum of 1..n tricks | Gauss formula |

---

## The ONE trick to remember

```
"TWO UNKNOWNS → TWO EQUATIONS: (sum diff) and (sum-of-squares diff)."
```

`S - Sn = x - y` and `(P - Pn)/(S - Sn) = x + y`. Two linear equations solve for both. Or XOR everything to get `x^y`, then split by a set bit. Both are O(n), O(1).

> Memory hook: "One equation from the sums, one from the square-sums — solve the pair."

---
