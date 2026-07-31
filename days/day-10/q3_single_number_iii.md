# Day 10 — Math, Bit & Trie · Q3 · Single Number III

---

## What is this question actually asking?

Every element appears **twice except two** which appear once. Find those two numbers — O(n) time, O(1) space.

```
[1,2,1,3,2,5] → [3, 5]   (3 and 5 appear once)
```

> The advanced XOR trick: XOR everything to get `a ^ b`, then use a **set bit** to split the numbers into two groups so each unique number is isolated.

---

## Pattern

```
PATTERN: XOR all + split by a differing bit
```

Whenever you see: *"two elements appear once, rest twice / find the two odd ones out"* → XOR the whole array to get `a ^ b`. Any set bit in that result is a bit where a and b differ; partition all numbers by that bit and XOR each group separately.

---

## Understand with a diagram

```
XOR all → x = a ^ b   (pairs cancel to 0)
pick a set bit of x, e.g. lowest: diff = x & (-x)

group A = numbers with that bit set   → XOR them → gives ONE unique number
group B = numbers with that bit clear → XOR them → gives the OTHER

Duplicates land in the same group as their twin → cancel out.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — hash count — O(n) time, O(n) space
Count each, return the two with count 1.

### 🟢 Optimal — XOR + bit split — O(n) time, O(1) space
Two passes: XOR all, then partition by a distinguishing bit and XOR each side.

---

## Pseudocode (Optimal)

```
function single_number_iii(nums):
    x = XOR of all nums          ← = a ^ b
    diff = x & (-x)              ← lowest set bit (a and b differ here)
    a = b = 0
    for n in nums:
        if n & diff: a ^= n      ← group with the bit set
        else:        b ^= n      ← group with the bit clear
    return [a, b]
```

---

## Python Code

```python
def single_number_iii(nums: list[int]) -> list[int]:
    x = 0
    for n in nums:
        x ^= n                    # x = a ^ b (pairs cancel)
    diff = x & (-x)               # isolate lowest set bit
    a = b = 0
    for n in nums:
        if n & diff:
            a ^= n                # group where the bit is set
        else:
            b ^= n                # group where the bit is clear
    return [a, b]


# Test
print(sorted(single_number_iii([1,2,1,3,2,5])))   # [3, 5]
print(sorted(single_number_iii([-1,0])))          # [-1, 0]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Hash count | O(n) | O(n) |
| XOR + split | O(n) | O(1) |

---

## Edge Cases to remember

- **`x & (-x)` isolates the lowest set bit** — any set bit works, but the lowest is the standard idiom.
- **Duplicates fall in the same group** — because equal numbers have identical bits → they cancel via XOR within the group.
- **Negative numbers** — two's complement bit ops still work in Python.
- **Single Number I** (all twice except one) → just XOR everything. **Single Number II** (all thrice except one) → bit-count mod 3.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Single Number (LC 136) | XOR all |
| 2 | Single Number II (LC 137) | Bit count mod 3 |
| 3 | Single Number III (LC 260) | This problem |
| 4 | Missing Number (LC 268) | XOR with indices |
| 5 | Find repeating & missing (Day 1 Q5) | XOR partition |
| 6 | Two non-repeating numbers | This |
| 7 | Maximum XOR of two (Q8) | Bit trie |
| 8 | Subset XOR problems | XOR properties |

---

## The ONE trick to remember

```
"XOR ALL → a^b; SPLIT NUMBERS BY A SET BIT OF a^b; XOR EACH GROUP."
```

XOR cancels pairs, leaving `a ^ b`. A set bit there is a position where a and b differ, so partitioning by that bit puts a and b in separate groups (with their duplicate twins), and XOR of each group yields one unique number.

> Memory hook: "One XOR gives their difference; a differing bit splits them apart."

---
