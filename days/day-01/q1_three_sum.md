# Day 1 — Arrays · Q1 · 3 Sum

---

## What is this question actually asking?

Find **all unique triplets** `(a, b, c)` in an array that sum to **0**.

```
nums = [-1, 0, 1, 2, -1, -4]
→ [[-1, -1, 2], [-1, 0, 1]]     (no duplicate triplets)
```

> The classic "extend Two Sum to three numbers, without duplicates." Tests: sort + two-pointer, and careful duplicate-skipping.

---

## Pattern

```
PATTERN: Sort + fix one element + Two Pointer on the rest
```

Whenever you see: *"find a triplet/quadruple summing to a target"* → **sort**, loop to fix the first number, then use **two pointers** (left/right) to find the other two in O(n). Sorting also makes duplicate-skipping easy.

---

## Understand with a diagram

```
sort: [-4, -1, -1, 0, 1, 2]

fix i=-4 → need two summing to 4 → l,r walk inward
fix i=-1 → need two summing to 1:
   l=-1 r=2 → -1+2=1 ✓ → triplet (-1,-1,2)
   move both, skip duplicates
fix i=-1 (again) → SKIP (same as previous i) to avoid dup triplet
```

Two pointers on a sorted array: sum too small → move `l` right; too big → move `r` left.

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — three loops — O(n³)
Try every triple `i<j<k`, check sum, dedup with a set. Correct but O(n³).

### 🟡 Better — hash set for the third — O(n²) time, O(n) space
Fix two, look up the complement in a set. Faster but dedup is fiddly.

### 🟢 Optimal — Sort + Two Pointer — O(n²) time, O(1) extra
Sort once. Fix `i`; move `l=i+1`, `r=n-1` inward based on the sum. Skip duplicate values for `i`, `l`, and `r` so each triplet is unique.

---

## Pseudocode (Optimal)

```
function three_sum(nums):
    sort nums
    result = []
    for i in range(n):
        if i > 0 and nums[i] == nums[i-1]: continue     ← skip dup first
        l, r = i+1, n-1
        while l < r:
            s = nums[i] + nums[l] + nums[r]
            if s == 0:
                add (nums[i], nums[l], nums[r])
                l++, r--
                skip duplicates at l and r
            elif s < 0: l++
            else: r--
    return result
```

---

## Python Code

```python
def three_sum(nums: list[int]) -> list[list[int]]:
    nums.sort()
    n = len(nums)
    result = []
    for i in range(n):
        if i > 0 and nums[i] == nums[i - 1]:
            continue                       # skip duplicate 'first' element
        l, r = i + 1, n - 1
        while l < r:
            s = nums[i] + nums[l] + nums[r]
            if s == 0:
                result.append([nums[i], nums[l], nums[r]])
                l += 1
                r -= 1
                while l < r and nums[l] == nums[l - 1]:  # skip dup l
                    l += 1
                while l < r and nums[r] == nums[r + 1]:  # skip dup r
                    r -= 1
            elif s < 0:
                l += 1                     # need a bigger sum
            else:
                r -= 1                     # need a smaller sum
    return result


# Test
print(three_sum([-1, 0, 1, 2, -1, -4]))   # [[-1, -1, 2], [-1, 0, 1]]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n³) | O(1) |
| Optimal | O(n²) | O(1) (excluding output) |

---

## Edge Cases to remember

- **Duplicate triplets** — the whole difficulty; skip equal values at `i`, `l`, and `r`.
- **Fewer than 3 elements** → return `[]`.
- **All zeros** `[0,0,0]` → one triplet `[0,0,0]`.
- **Target other than 0** → same code with `s == target`.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Two Sum II (sorted) | Two pointer core |
| 2 | 4 Sum | Fix two, two-pointer rest |
| 3 | 3 Sum Closest | Track min diff instead of ==0 |
| 4 | 3 Sum Smaller | Count pairs with sum < target |
| 5 | Two Sum (Day 12 Q1) | Complement idea |
| 6 | Pair with target difference | Two pointer |
| 7 | Container with most water | Two pointer inward |
| 8 | Trapping rain water | Two pointer |

---

## The ONE trick to remember

```
"SORT, FIX ONE, TWO-POINTER THE REST — AND SKIP EQUAL NEIGHBORS."
```

Sorting turns the inner search into an O(n) two-pointer sweep and makes duplicates adjacent, so you skip them with `nums[i]==nums[i-1]`. Total O(n²).

> Memory hook: "Lock the first, squeeze the other two from both ends."

---
