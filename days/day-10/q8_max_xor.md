# Day 10 — Math, Bit & Trie · Q8 · Maximum XOR of Two Numbers in an Array

---

## What is this question actually asking?

Find the maximum value of `a XOR b` over all pairs in the array.

```
[3,10,5,25,2,8] → 28   (5 XOR 25 = 28)
```

> The showcase problem for a **bit Trie** (binary trie). Insert numbers bit by bit (MSB→LSB); for each number, greedily walk the opposite bit to maximize XOR.

---

## Pattern

```
PATTERN: Binary (bit) Trie — greedily choose the opposite bit at each level
```

Whenever you see: *"maximum XOR pair / XOR queries"* → build a Trie of the numbers' bits (fixed width, MSB first). For each number, traverse preferring the **opposite** bit at each step (which sets that XOR bit to 1). O(n·32).

---

## Understand with a diagram

```
Insert each number as a 32-bit path (MSB→LSB) in a binary Trie (children 0/1).

For candidate x, to MAXIMIZE x XOR y:
    at each bit, go to the child with the OPPOSITE bit if it exists
    (opposite bit → XOR contributes a 1 at this position → bigger result)
    else follow the same bit.

Accumulate the XOR value along the walk. Best over all x = answer.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all pairs — O(n²)
XOR every pair.

### 🟢 Optimal — bit Trie — O(n·B), B = bit width (~32)
Insert all numbers, then query each against the Trie greedily. Linear in n (times constant bit width).

---

## Pseudocode (Optimal)

```
function find_max_xor(nums):
    B = highest bit needed (e.g. 31)
    trie = {}
    insert each num bit by bit (MSB→LSB)
    best = 0
    for x in nums:
        node = trie; cur = 0
        for b from B down to 0:
            bit = (x >> b) & 1
            want = 1 - bit                 ← opposite bit maximizes XOR
            if want in node: cur |= (1<<b); node = node[want]
            else: node = node[bit]
        best = max(best, cur)
    return best
```

---

## Python Code

```python
def find_maximum_xor(nums: list[int]) -> int:
    B = max(nums).bit_length() - 1 if nums else 0     # highest bit index
    root = {}
    # insert all numbers
    for num in nums:
        node = root
        for b in range(B, -1, -1):
            bit = (num >> b) & 1
            node = node.setdefault(bit, {})

    best = 0
    for num in nums:
        node = root
        cur = 0
        for b in range(B, -1, -1):
            bit = (num >> b) & 1
            want = 1 - bit                 # opposite bit → XOR = 1 here
            if want in node:
                cur |= (1 << b)
                node = node[want]
            else:
                node = node[bit]
        best = max(best, cur)
    return best


# Test
print(find_maximum_xor([3,10,5,25,2,8]))   # 28
print(find_maximum_xor([0]))               # 0
print(find_maximum_xor([2,4]))             # 6
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(1) |
| Bit Trie | O(n·B) | O(n·B) |

---

## Edge Cases to remember

- **Traverse MSB→LSB** — the most significant differing bit dominates the XOR value; greedily maximizing high bits first is optimal.
- **Prefer the OPPOSITE bit** — that's what sets the XOR bit to 1.
- **Fixed bit width** — use `max.bit_length()` or a constant like 31/32; be consistent across insert and query.
- **Single element / all equal** → 0.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Maximum XOR of Two Numbers (LC 421) | This problem |
| 2 | Maximum XOR With an Element (LC 1707) | Bit Trie + offline queries |
| 3 | Count pairs with XOR in range (LC 1803) | Bit Trie counts |
| 4 | Minimum XOR pair | Sort adjacent |
| 5 | Single Number III (Q3) | Bit tricks |
| 6 | Subarray max XOR | Prefix XOR + Trie |
| 7 | XOR queries of subarray (LC 1310) | Prefix XOR |
| 8 | Concatenation with max XOR | Bit Trie |

---

## The ONE trick to remember

```
"BIT TRIE (MSB→LSB); AT EACH BIT WALK THE OPPOSITE BRANCH TO MAXIMIZE XOR."
```

Store numbers as binary paths in a Trie. For each number, greedily descend toward the opposite bit at every level — the opposite bit makes that XOR position a 1. Since higher bits dominate, greedy-from-the-top is optimal.

> Memory hook: "Chase the opposite bit down the tree — every mismatch is a 1 in the XOR."

---
