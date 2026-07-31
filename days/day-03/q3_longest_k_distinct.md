# Day 3 — Sliding Window & Backtracking · Q3 · Longest Substring With At Most K Distinct Characters

---

## What is this question actually asking?

Find the length of the longest substring containing **at most `k` distinct** characters.

```
"eceba", k=2 → 3   ("ece")
"aa", k=1    → 2
```

> The general "at most K distinct" sliding window — a template that solves a whole family of problems.

---

## Pattern

```
PATTERN: Variable Sliding Window + count map, shrink while distinct > k
```

Whenever you see: *"longest window with at most K distinct / at most K of something"* → expand `right`, track a char-count map; while the map has more than `k` keys, shrink from `left`. Track the max valid window.

---

## Understand with a diagram

```
"eceba", k=2   count map of chars in window

right→ e c e   map{e:2,c:1} (2 distinct ≤2) len3 max3
right=b → map{e,c,b}=3 distinct >2 → shrink left:
   remove e... until distinct ≤2
continue. best = 3
```

Shrinking: decrement `count[s[left]]`; if it hits 0, delete the key (distinct drops).

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all substrings — O(n²)
Check distinct count per substring.

### 🟢 Optimal — sliding window — O(n)
Expand right, and while `len(count) > k`, shrink left. Each char enters/leaves once → O(n).

---

## Pseudocode (Optimal)

```
function longest_k_distinct(s, k):
    count = {}
    left = 0
    best = 0
    for right, ch in enumerate(s):
        count[ch] += 1
        while len(count) > k:               ← too many distinct → shrink
            count[s[left]] -= 1
            if count[s[left]] == 0: del count[s[left]]
            left += 1
        best = max(best, right - left + 1)
    return best
```

---

## Python Code

```python
from collections import defaultdict

def longest_k_distinct(s: str, k: int) -> int:
    if k == 0:
        return 0
    count = defaultdict(int)
    left = 0
    best = 0
    for right, ch in enumerate(s):
        count[ch] += 1
        while len(count) > k:                # shrink until ≤ k distinct
            count[s[left]] -= 1
            if count[s[left]] == 0:
                del count[s[left]]
            left += 1
        best = max(best, right - left + 1)
    return best


# Test
print(longest_k_distinct("eceba", 2))   # 3
print(longest_k_distinct("aa", 1))      # 2
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Sliding window | O(n) | O(k) |

---

## Edge Cases to remember

- **Delete the key at count 0** — otherwise `len(count)` overcounts distinct chars.
- **`k = 0`** → answer 0 (guard).
- **`k >= distinct chars`** → whole string.
- **`while` not `if`** — shrinking may need multiple steps (though for "add one char" a single shrink suffices, `while` is the safe template that generalizes).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Longest substring no-repeat (Q2) | k = "all distinct" |
| 2 | Fruit Into Baskets (LC 904) | k = 2 exactly |
| 3 | Subarrays with K different integers (LC 992) | atMost(k) − atMost(k−1) |
| 4 | Longest substring exactly K distinct | atMost(k) − atMost(k−1) |
| 5 | Max consecutive ones III (LC 1004) | "at most K zeros" |
| 6 | Minimum window substring (LC 76) | Need-map window |
| 7 | Replace to get K repeating (LC 424) | Window + max freq |
| 8 | Longest nice/valid window | Same template |

---

## The ONE trick to remember

```
"EXPAND RIGHT; WHILE distinct > k, SHRINK LEFT (delete zero-count keys)."
```

The count map's size *is* the distinct count. Grow the window; when it exceeds k distinct, shrink from the left, deleting keys that hit zero. "Exactly K" = `atMost(K) − atMost(K−1)`.

> Memory hook: "Let it grow until too colorful, then trim the left until it's calm again."

---
