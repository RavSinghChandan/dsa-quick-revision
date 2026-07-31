# Day 1 — Arrays · Q4 · Majority Element II (> n/3)

---

## What is this question actually asking?

Find all elements that appear **more than ⌊n/3⌋ times**.

```
[3,2,3] → [3]
[1,1,1,3,3,2,2,2] → [1, 2]
```

> There can be **at most two** such elements (two things can't each exceed a third of the array... three could, but not with strict >n/3 → max 2). Tests the extended Boyer-Moore voting algorithm.

---

## Pattern

```
PATTERN: Boyer-Moore Majority Voting (extended to two candidates)
```

Whenever you see: *"element(s) appearing more than n/k times"* → track `k-1` candidates with counts, cancel across all when a new value appears, then **verify** the survivors in a second pass.

---

## Understand with a diagram

```
Two candidates c1,c2 with counts n1,n2.
For each x:
  x == c1 → n1++
  x == c2 → n2++
  n1 == 0 → c1 = x, n1 = 1
  n2 == 0 → c2 = x, n2 = 1
  else    → n1--, n2--        (x cancels one of each)

After: c1,c2 are the ONLY possible answers → verify counts in pass 2.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — count each — O(n²) or hash O(n) space
Hash-map counting is O(n) time but O(n) space.

### 🟢 Optimal — Boyer-Moore voting — O(n) time, O(1) space
Two candidate slots, cancel on mismatch, verify at the end. Constant space.

---

## Pseudocode (Optimal)

```
function majority_third(nums):
    c1 = c2 = None; n1 = n2 = 0
    for x in nums:
        if c1 == x: n1++
        elif c2 == x: n2++
        elif n1 == 0: c1 = x; n1 = 1
        elif n2 == 0: c2 = x; n2 = 1
        else: n1--; n2--
    # verify (candidates may not actually exceed n/3)
    result = [c for c in {c1, c2} if nums.count(c) > n/3]
    return result
```

---

## Python Code

```python
def majority_element_ii(nums: list[int]) -> list[int]:
    c1 = c2 = None
    n1 = n2 = 0
    for x in nums:
        if c1 == x:
            n1 += 1
        elif c2 == x:
            n2 += 1
        elif n1 == 0:
            c1, n1 = x, 1
        elif n2 == 0:
            c2, n2 = x, 1
        else:
            n1 -= 1
            n2 -= 1
    # Verification pass — candidates aren't guaranteed
    n = len(nums)
    return [c for c in {c1, c2} if c is not None and nums.count(c) > n // 3]


# Test
print(majority_element_ii([3, 2, 3]))                  # [3]
print(majority_element_ii([1,1,1,3,3,2,2,2]))          # [1, 2]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Hash count | O(n) | O(n) |
| Boyer-Moore | O(n) | O(1) |

---

## Edge Cases to remember

- **Verification is mandatory** — the voting phase only *narrows* to 2 candidates; they may not actually exceed n/3.
- **`elif` order matters** — check "matches existing candidate" BEFORE "slot empty", or you double-assign.
- **Distinct candidates** — use a set `{c1, c2}` so you don't report the same one twice.
- **> n/3 is strict** → at most 2 answers.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Majority Element (> n/2, LC 169) | Single-candidate voting |
| 2 | Majority > n/k generalized | k-1 candidates |
| 3 | Find element appearing > 25% | Voting |
| 4 | Check if a majority exists | Voting + verify |
| 5 | Repeated element detection | Counting |
| 6 | Most frequent (Top-1) | Counter |
| 7 | Water-filling / cancellation problems | Pairing/cancel |
| 8 | Design a voting tally | Counters |

---

## The ONE trick to remember

```
"TWO CANDIDATE SLOTS, CANCEL ON A THIRD VALUE, THEN VERIFY."
```

At most two elements can exceed n/3. Track two candidates with counts; a value matching neither cancels one from each. The survivors are only *candidates* — a second pass confirms they truly exceed n/3.

> Memory hook: "Two thrones; any outsider knocks one point off each; count the winners at the end."

---
