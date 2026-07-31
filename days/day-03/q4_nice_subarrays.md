# Day 3 — Sliding Window & Backtracking · Q4 · Count Number of Nice Subarrays

---

## What is this question actually asking?

Count subarrays that contain **exactly `k` odd numbers** (a "nice" subarray).

```
[1,1,2,1,1], k=3 → 2
[2,4,6], k=1     → 0
```

> A "count subarrays with exactly K ..." problem. Two clean approaches: **prefix-count hash map**, or the **atMost(k) − atMost(k−1)** sliding-window trick.

---

## Pattern

```
PATTERN: "Exactly K" = atMost(K) − atMost(K−1)   (or prefix-count map)
```

Whenever you see: *"count subarrays with EXACTLY k of something"* → it's hard directly, but **at most k** is an easy sliding window. So `exactly(k) = atMost(k) − atMost(k−1)`. Alternatively, map each element to 0/1 (odd=1) and count subarrays with sum = k via a prefix map.

---

## Understand with a diagram

```
Turn odds into 1, evens into 0 → count subarrays with SUM = k.

atMost(k): longest windows with ≤ k ones; number of subarrays ending at right
           = (right - left + 1) while ones ≤ k.

exactly(k) = atMost(k) - atMost(k-1)

[1,1,2,1,1], k=3
atMost(3) counts all; atMost(2) removes those with ≤2 → difference = 2
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all subarrays — O(n²)
Count odds in each.

### 🟢 Optimal A — atMost(k) − atMost(k−1) — O(n)
Two sliding-window passes counting subarrays with at most k odds.

### 🟢 Optimal B — prefix odd-count + hash map — O(n)
Running count of odds; for each position add `freq[oddCount − k]`. One pass, `{0:1}` seed.

---

## Pseudocode (Optimal — atMost)

```
function nice(nums, k):
    return atMost(nums, k) - atMost(nums, k-1)

function atMost(nums, k):
    if k < 0: return 0
    left = 0; odd = 0; res = 0
    for right in range(n):
        odd += nums[right] % 2
        while odd > k:
            odd -= nums[left] % 2; left++
        res += right - left + 1       ← subarrays ending at right with ≤k odds
    return res
```

---

## Python Code

```python
def number_of_nice_subarrays(nums: list[int], k: int) -> int:
    def at_most(k: int) -> int:
        if k < 0:
            return 0
        left = odd = res = 0
        for right in range(len(nums)):
            odd += nums[right] & 1
            while odd > k:
                odd -= nums[left] & 1
                left += 1
            res += right - left + 1      # windows ending at right with ≤k odds
        return res

    return at_most(k) - at_most(k - 1)


# Alternative: prefix odd-count + hash map
from collections import defaultdict
def nice_prefix(nums, k):
    freq = defaultdict(int); freq[0] = 1
    odd = count = 0
    for x in nums:
        odd += x & 1
        count += freq[odd - k]
        freq[odd] += 1
    return count


# Test
print(number_of_nice_subarrays([1,1,2,1,1], 3))   # 2
print(nice_prefix([1,1,2,1,1], 3))                # 2
print(number_of_nice_subarrays([2,4,6], 1))       # 0
```

---

## Complexity

| | Time | Space |
|---|---|---|
| atMost trick | O(n) | O(1) |
| Prefix + map | O(n) | O(n) |

---

## Edge Cases to remember

- **`atMost(k−1)` with k=0** → `at_most(-1)` returns 0 (guard).
- **Prefix seed `{0:1}`** — for subarrays starting at index 0.
- **odd test `x & 1`** — 1 if odd, works for negatives too in Python (be careful in other langs).
- **`res += right-left+1`** counts all valid windows ending at `right`, not just the longest.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Subarrays with K Different Integers (LC 992) | atMost(k)−atMost(k−1) |
| 2 | Binary Subarrays With Sum (LC 930) | Same trick |
| 3 | Subarray Sum Equals K (LC 560) | Prefix + map |
| 4 | Count subarrays XOR K (Q1) | Prefix + map |
| 5 | Subarrays with exactly K odd/even | Map odds to 1 |
| 6 | Fruit into baskets (LC 904) | atMost |
| 7 | Count nice/valid windows | atMost |
| 8 | Replace odds/evens problems | 0/1 transform |

---

## The ONE trick to remember

```
"EXACTLY K = atMost(K) − atMost(K−1). (Map odd→1, count subarray sums.)"
```

Direct "exactly k" is awkward; "at most k" is an easy shrinking window. Subtract to get exactly k. Or run a prefix odd-count with a hash map, adding `freq[oddCount − k]` each step.

> Memory hook: "Exactly = (at most K) minus (at most K−1)."

---
