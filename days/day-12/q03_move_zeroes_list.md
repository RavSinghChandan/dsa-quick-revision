# Day 12 · Q3 · Move Zeroes to End (in-place, two pointer)  ·  [LISTS]

---

## What is this question actually asking?

Given a list, move all `0`s to the **end** while keeping the order of the non-zero numbers. Do it **in place** (no new list).

```
[0, 1, 0, 3, 12] → [1, 3, 12, 0, 0]
```

> Tests the in-place **two-pointer / write-index** trick — reorganize a list without extra memory. Comes up constantly in data cleaning ("push nulls to the end", "compact valid rows").

---

## Pattern

```
PATTERN: Two Pointer — a slow "write" index + a fast "read" index
```

Whenever you see: *"keep some elements, remove/push others, in place, preserve order"* → walk with a **read pointer**; whenever the element should stay, write it at the **write pointer** and advance write.

---

## Understand with a diagram

```
[0, 1, 0, 3, 12]     w = write index (next slot for a non-zero)
 r scans →

r=0 val0  → zero, skip
r=1 val1  → write at w=0 → [1,1,0,3,12], w=1
r=2 val0  → skip
r=3 val3  → write at w=1 → [1,3,0,3,12], w=2
r=4 val12 → write at w=2 → [1,3,12,3,12], w=3
fill from w=3 to end with 0 → [1,3,12,0,0]
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — new list
Build `[x for x in nums if x!=0]` then pad zeros → O(n) time but **O(n) extra space**, and not in place.

### 🟢 Optimal — Two pointer, in place — O(n), O(1)
One pass: copy every non-zero forward to the write index. Then fill the remaining tail with zeros.

---

## Pseudocode (Optimal)

```
function move_zeroes(nums):
    w = 0
    for r in range(len(nums)):
        if nums[r] != 0:
            nums[w] = nums[r]
            w += 1
    for i in range(w, len(nums)):
        nums[i] = 0
```

---

## Python Code

```python
def move_zeroes(nums: list[int]) -> None:
    w = 0                              # write index for next non-zero
    for r in range(len(nums)):         # read index
        if nums[r] != 0:
            nums[w] = nums[r]
            w += 1
    for i in range(w, len(nums)):      # pad tail with zeros
        nums[i] = 0


# Test
a = [0, 1, 0, 3, 12]
move_zeroes(a)
print(a)   # [1, 3, 12, 0, 0]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Two pointer | O(n) | O(1) — in place |

---

## Edge Cases to remember

- **All zeros** / **no zeros** → still correct, write index handles both.
- **In-place means modify the input** — don't return a new list unless allowed.
- **Swap variant** — you can swap instead of copy+pad; both O(n), pick one and be consistent.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Remove Element (LC 27) | Write index skips target |
| 2 | Remove Duplicates from Sorted Array (LC 26) | Write unique only |
| 3 | Partition array (Dutch flag) | Multiple write pointers |
| 4 | Sort colors 0/1/2 (LC 75) | Three pointers |
| 5 | Compact valid rows in a dataset | Push invalid to end |
| 6 | Filter in place | Write-index copy |
| 7 | Merge sorted array in place (LC 88) | Write from the back |
| 8 | Squeeze out spaces from string | Write index |

---

## The ONE trick to remember

```
"WRITE INDEX FOR KEEPERS; SCAN WITH READ; PAD THE TAIL."
```

A slow write pointer marks where the next kept element goes; the fast read pointer scans everything. Copy keepers forward, then fill the rest. O(1) space, order preserved.

> Memory hook: "Read scouts ahead, write places the keepers."

---
