# Day 3 — Sliding Window & Backtracking · Q1 · Count Subarrays with Given XOR K

---

## What is this question actually asking?

Count contiguous subarrays whose elements **XOR** together to exactly `k`.

```
[4,2,2,6,4], k=6 → 4 subarrays
[5,6,7,8,9], k=5 → 2
```

> The XOR twin of "subarray sum equals K." Sliding window does NOT work for XOR (no monotonicity) — you need a **prefix-XOR + hash map** trick.

---

## Pattern

```
PATTERN: Prefix XOR + Hash Map of prefix counts
```

Whenever you see: *"count subarrays with XOR/sum equal to k"* → compute a running prefix value; a subarray `(i..j]` has XOR k when `prefixXOR[j] ^ prefixXOR[i] = k`, i.e. `prefixXOR[i] = prefixXOR[j] ^ k`. Count how many earlier prefixes equal that.

---

## Understand with a diagram

```
prefix_xor as we scan.  For current prefix px, we want earlier prefixes = px ^ k.

           want = px ^ k
count += freq[want]         ← that many subarrays ending here have XOR k
freq[px] += 1               ← record current prefix

Why: (subarray XOR) = px ^ (earlier prefix). Set it = k → earlier = px ^ k.
Seed freq[0] = 1 for subarrays starting at index 0.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all subarrays — O(n²)
For each start, XOR forward, count matches.

### 🟢 Optimal — prefix XOR + hash map — O(n)
One pass; running XOR `px`; look up `px ^ k` in a frequency map. `defaultdict(int)` seeded with `{0:1}`.

---

## Pseudocode (Optimal)

```
function count_xor_k(nums, k):
    freq = {0: 1}
    px = 0
    count = 0
    for x in nums:
        px ^= x
        count += freq.get(px ^ k, 0)     ← earlier prefixes making XOR = k
        freq[px] += 1
    return count
```

---

## Python Code

```python
from collections import defaultdict

def count_subarrays_xor_k(nums: list[int], k: int) -> int:
    freq = defaultdict(int)
    freq[0] = 1                       # empty prefix
    px = 0
    count = 0
    for x in nums:
        px ^= x                       # running prefix XOR
        count += freq[px ^ k]         # earlier prefix that yields XOR k
        freq[px] += 1
    return count


# Test
print(count_subarrays_xor_k([4,2,2,6,4], 6))   # 4
print(count_subarrays_xor_k([5,6,7,8,9], 5))   # 2
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(1) |
| Prefix XOR + map | O(n) | O(n) |

---

## Edge Cases to remember

- **Seed `freq[0] = 1`** — for subarrays that start at index 0 (whole-prefix equals k).
- **Count then insert** — look up `px^k` BEFORE adding the current prefix, or you may count a zero-length subarray when `k=0`.
- **Sliding window doesn't apply** — XOR isn't monotonic; the hash-map approach is required.
- **`k = 0`** → counts subarrays whose XOR is 0 (equal-prefix pairs).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Subarray Sum Equals K (LC 560) | Prefix sum + map |
| 2 | Count subarrays sum divisible by K (LC 974) | Prefix mod + map |
| 3 | Longest subarray sum = K | Prefix sum first-seen |
| 4 | Contiguous array 0/1 equal (LC 525) | Prefix balance |
| 5 | Subarray with equal 0s and 1s | Prefix map |
| 6 | Count nice subarrays (Q4) | Prefix count of odds |
| 7 | Binary subarrays with sum (LC 930) | Prefix sum |
| 8 | Max length subarray sum k | Prefix map |

---

## The ONE trick to remember

```
"prefixXOR[i] = prefixXOR[j] ^ k  → COUNT EARLIER PREFIXES EQUAL TO px^k."
```

A subarray's XOR equals `k` iff the current prefix XORed with some earlier prefix equals `k`. So for each position, add `freq[px ^ k]`, then record `px`. Seed `{0:1}`.

> Memory hook: "Want XOR k? Find the earlier prefix that cancels down to k."

---
