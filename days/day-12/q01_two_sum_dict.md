# Day 12 · Q1 · Two Sum (Dictionary / Hash Map)  ·  [DICTS]

---

## What is this question actually asking?

Given a list of numbers and a `target`, return the **indices of the two numbers that add up to the target**.

```
nums = [2, 7, 11, 15], target = 9
→ 2 + 7 = 9 → indices [0, 1]
```

> The single most-asked warm-up. It tests one thing: do you reach for a **hash map** to turn an O(n²) scan into O(n)?

---

## Pattern

```
PATTERN: Hash Map "seen so far" (complement lookup)
```

Whenever you see: *"find a pair / has this appeared / need the matching value"* → store what you've seen in a dict, and for each new item ask *"is my complement already in the dict?"* in O(1).

---

## Understand with a diagram

```
target = 9
walk nums, remember {value → index}:

 2 → need 7. seen? no.  store {2:0}
 7 → need 2. seen? YES at index 0 → answer [0,1]
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — O(n²)
```
for i: for j>i: if nums[i]+nums[j]==target: return [i,j]
```
Check every pair. Slow for large n.

### 🟢 Optimal — Hash Map — O(n)
For each number, its partner must be `target - num`. Keep a dict of numbers seen so far; if the complement is already there, you're done — one pass.

---

## Pseudocode (Optimal)

```
function two_sum(nums, target):
    seen = {}                      ← value → index
    for i, num in enumerate(nums):
        need = target - num
        if need in seen:
            return [seen[need], i]
        seen[num] = i
    return []                      ← no pair
```

---

## Python Code

```python
def two_sum(nums: list[int], target: int) -> list[int]:
    seen = {}                          # value -> index
    for i, num in enumerate(nums):
        need = target - num
        if need in seen:               # O(1) lookup
            return [seen[need], i]
        seen[num] = i
    return []


# Test
print(two_sum([2, 7, 11, 15], 9))   # [0, 1]
print(two_sum([3, 2, 4], 6))        # [1, 2]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(1) |
| Hash Map | O(n) | O(n) |

---

## Edge Cases to remember

- **Same element twice** — `[3,3], target 6` → must be two *different* indices; storing after the check handles it.
- **No solution** → return `[]` (or per spec).
- **Negative numbers / zero** → complement math still works.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Two Sum II (sorted) | Two pointer instead |
| 2 | 3Sum / 4Sum | Fix one, two-sum the rest |
| 3 | Subarray sum equals K | Prefix sum + hash map |
| 4 | Contains Duplicate | "seen" set |
| 5 | First unique character | Count map |
| 6 | Pair with given difference | Complement lookup |
| 7 | Longest consecutive sequence | Set membership |
| 8 | Group anagrams | Key = sorted word |

---

## The ONE trick to remember

```
"STORE WHAT YOU'VE SEEN; ASK IF THE COMPLEMENT IS ALREADY THERE."
```

The hash map turns "find the partner" from an O(n) inner scan into an O(1) lookup. Check for the complement **before** inserting the current value, so a number can't pair with itself.

> Memory hook: "I need `target - num` — have I met it already?"

---
