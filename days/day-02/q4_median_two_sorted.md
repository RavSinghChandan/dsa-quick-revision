# Day 2 — Binary Search · Q4 · Median of Two Sorted Arrays

---

## What is this question actually asking?

Given two sorted arrays, find the **median** of their combined sorted order — in **O(log(min(m,n)))**.

```
[1,3], [2]     → 2.0
[1,2], [3,4]   → 2.5   (combined [1,2,3,4] → (2+3)/2)
```

> The hardest "easy-looking" binary search. Tests: binary-search a **partition** of the smaller array so the left halves hold exactly half the elements.

---

## Pattern

```
PATTERN: Binary Search on the PARTITION of the smaller array
```

Whenever you see: *"median / kth of two sorted arrays in log time"* → binary-search where to cut array A. The cut in B is forced (halves must total half the elements). Adjust the cut until the left side's max ≤ the right side's min.

---

## Understand with a diagram

```
A (smaller), B. Total left half must have (m+n+1)//2 elements.
cut A at i → cut B at j = half - i.

Left:  A[0..i-1], B[0..j-1]      Right: A[i..], B[j..]
Valid partition when:
   A[i-1] <= B[j]   AND   B[j-1] <= A[i]

     L1=A[i-1]  R1=A[i]
     L2=B[j-1]  R2=B[j]
median = max(L1,L2) [odd]  or  (max(L1,L2)+min(R1,R2))/2 [even]

If A[i-1] > B[j] → cut A too far right → move i left, else i right.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — merge fully — O(m+n)
Merge into one array, pick the middle. Simple; not log time.

### 🟡 Better — merge halfway — O(m+n)
Walk with two pointers until the middle. Still linear.

### 🟢 Optimal — binary search partition — O(log min(m,n))
Cut the smaller array; the other cut is determined. Check the 4 boundary values; adjust. Uses ±∞ sentinels at the edges.

---

## Pseudocode (Optimal)

```
function median(A, B):
    ensure A is the smaller array
    m, n = len(A), len(B)
    lo, hi = 0, m
    half = (m + n + 1) // 2
    while lo <= hi:
        i = (lo+hi)//2          ← cut in A
        j = half - i            ← cut in B
        L1 = A[i-1] if i>0 else -inf ; R1 = A[i] if i<m else +inf
        L2 = B[j-1] if j>0 else -inf ; R2 = B[j] if j<n else +inf
        if L1 <= R2 and L2 <= R1:              ← correct partition
            if (m+n) odd: return max(L1,L2)
            else: return (max(L1,L2)+min(R1,R2))/2
        elif L1 > R2: hi = i-1   ← cut A left
        else:         lo = i+1   ← cut A right
```

---

## Python Code

```python
def median_two_sorted(A: list[int], B: list[int]) -> float:
    if len(A) > len(B):
        A, B = B, A                    # binary search the smaller one
    m, n = len(A), len(B)
    half = (m + n + 1) // 2
    lo, hi = 0, m
    while lo <= hi:
        i = (lo + hi) // 2             # cut in A
        j = half - i                   # cut in B
        L1 = A[i - 1] if i > 0 else float('-inf')
        R1 = A[i]     if i < m else float('inf')
        L2 = B[j - 1] if j > 0 else float('-inf')
        R2 = B[j]     if j < n else float('inf')

        if L1 <= R2 and L2 <= R1:      # valid partition
            if (m + n) % 2:
                return float(max(L1, L2))
            return (max(L1, L2) + min(R1, R2)) / 2
        elif L1 > R2:
            hi = i - 1                 # move cut in A left
        else:
            lo = i + 1                 # move cut in A right
    raise ValueError("inputs not sorted")


# Test
print(median_two_sorted([1,3], [2]))     # 2.0
print(median_two_sorted([1,2], [3,4]))   # 2.5
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Merge | O(m+n) | O(1) |
| Partition BS | O(log min(m,n)) | O(1) |

---

## Edge Cases to remember

- **Search the SMALLER array** — swap first; keeps `j` in range and the log base minimal.
- **±∞ sentinels** at the boundaries (`i=0`, `i=m`, etc.) so the comparisons work without special cases.
- **`half = (m+n+1)//2`** with `max(L1,L2)` for odd — this convention puts the extra element on the left, handling both parities cleanly.
- **Even vs odd total** — odd → `max(L1,L2)`; even → average of `max(L1,L2)` and `min(R1,R2)`.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Kth element of two sorted arrays | Same partition idea |
| 2 | Median of a data stream (LC 295) | Two heaps (different) |
| 3 | Split Array Largest Sum (LC 410) | BS on answer |
| 4 | Merge two sorted arrays (LC 88) | Merge mechanics |
| 5 | Find K-th smallest pair distance | BS on answer |
| 6 | Partition array into balanced halves | Partition search |
| 7 | Median of row-sorted matrix | BS on value |
| 8 | Percentile of merged streams | Partition |

---

## The ONE trick to remember

```
"BINARY-SEARCH THE CUT IN THE SMALLER ARRAY; THE OTHER CUT IS FORCED."
```

Cut A at `i`; B's cut is `j = half - i`. A partition is valid when `A[i-1] <= B[j]` and `B[j-1] <= A[i]`. Use ±∞ at the edges. Then read the median off the four boundary values.

> Memory hook: "Slice the small one, the big one's slice is decided — line up the boundary four."

---
