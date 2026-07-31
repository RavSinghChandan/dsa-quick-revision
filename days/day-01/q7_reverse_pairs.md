# Day 1 — Arrays · Q7 · Reverse Pairs

---

## What is this question actually asking?

Count pairs `(i, j)` with `i < j` **and** `nums[i] > 2 * nums[j]`.

```
[1, 3, 2, 3, 1] → 2   pairs: (3,1) at idx(1,4) and (3,1) at idx(3,4)
[2, 4, 3, 5, 1] → 3
```

> A counting-inversions variant. Brute is O(n²); the interview answer is **merge sort** while counting, in O(n log n).

---

## Pattern

```
PATTERN: Merge Sort with a counting step (divide & conquer)
```

Whenever you see: *"count pairs across the array satisfying an order condition"* (inversions, reverse pairs, smaller-to-right) → piggyback on **merge sort**: count valid cross-pairs between the two sorted halves before merging.

---

## Understand with a diagram

```
Split array, recursively sort+count each half.
Both halves are now SORTED. Count cross-pairs (i in left, j in right):

left  = [3, 3]   right = [1, 1]   (condition: left > 2*right)
 j pointer scans right; for each left[i], count how many right[j] with left[i] > 2*right[j]
 since both sorted, j only moves FORWARD across all i → O(n) counting

Then merge the two halves normally.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all pairs — O(n²)
Double loop, check `nums[i] > 2*nums[j]`.

### 🟢 Optimal — Merge sort + count — O(n log n)
During the merge step, both halves are sorted. Use a two-pointer scan to count pairs where `left[i] > 2*right[j]` in O(n) per merge. Then merge as usual.

---

## Pseudocode (Optimal)

```
function count(nums):
    return merge_sort(nums, 0, n-1)

function merge_sort(a, lo, hi):
    if lo >= hi: return 0
    mid = (lo+hi)//2
    cnt = merge_sort(a, lo, mid) + merge_sort(a, mid+1, hi)
    # count cross reverse-pairs (both halves sorted)
    j = mid+1
    for i in lo..mid:
        while j <= hi and a[i] > 2*a[j]: j++
        cnt += (j - (mid+1))
    merge(a, lo, mid, hi)      ← standard merge
    return cnt
```

---

## Python Code

```python
def reverse_pairs(nums: list[int]) -> int:
    def merge_sort(a, lo, hi):
        if lo >= hi:
            return 0
        mid = (lo + hi) // 2
        cnt = merge_sort(a, lo, mid) + merge_sort(a, mid + 1, hi)

        # count pairs: a[i] > 2*a[j], i in left, j in right (both sorted)
        j = mid + 1
        for i in range(lo, mid + 1):
            while j <= hi and a[i] > 2 * a[j]:
                j += 1
            cnt += j - (mid + 1)

        # standard merge
        a[lo:hi + 1] = sorted(a[lo:hi + 1])
        return cnt

    return merge_sort(nums[:], 0, len(nums) - 1)


# Test
print(reverse_pairs([1, 3, 2, 3, 1]))   # 2
print(reverse_pairs([2, 4, 3, 5, 1]))   # 3
```

> Note: `sorted(a[lo:hi+1])` is used for a readable merge; a true two-array merge keeps it strictly O(n) per level. The **counting** is the interview point.

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(1) |
| Merge sort + count | O(n log n) | O(n) |

---

## Edge Cases to remember

- **Count BEFORE merging** — the counting scan relies on both halves being sorted but still separate.
- **Overflow** — `2 * a[j]` can overflow in fixed-width languages; use long/`2L`. Python is safe.
- **`j` never resets** across the `i` loop — because left is sorted, the boundary only advances → O(n) per merge.
- **Strict `>`** — the condition is strictly greater than `2*nums[j]`.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Count Inversions | Same merge-count, condition `>` |
| 2 | Count Smaller Numbers After Self (LC 315) | Merge sort / BIT |
| 3 | Count of Range Sum (LC 327) | Merge sort on prefix sums |
| 4 | Reverse Pairs (LC 493) | This exact problem |
| 5 | Global vs Local Inversions | Inversion counting |
| 6 | Kth smallest via merge | Divide & conquer |
| 7 | Sort + count anything cross-half | Merge template |
| 8 | Merge K sorted (Day 12 Q6) | Merge mechanics |

---

## The ONE trick to remember

```
"PIGGYBACK ON MERGE SORT: COUNT CROSS-PAIRS WHILE BOTH HALVES ARE SORTED."
```

Brute O(n²) → embed the count in merge sort. Because each half is sorted, a two-pointer scan counts all valid `(i, j)` cross-pairs in O(n) before merging. Total O(n log n).

> Memory hook: "Sort as you go, and count the pairs the sort reveals."

---
