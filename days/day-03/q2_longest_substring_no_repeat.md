# Day 3 — Sliding Window & Backtracking · Q2 · Longest Substring Without Repeating Characters

---

## What is this question actually asking?

Find the length of the **longest substring with all distinct characters**.

```
"abcabcbb" → 3   ("abc")
"bbbbb"    → 1   ("b")
"pwwkew"   → 3   ("wke")
```

> The definitive **variable-size sliding window** question. Tests: grow the window, and shrink from the left when a duplicate appears.

---

## Pattern

```
PATTERN: Variable Sliding Window + last-seen index map
```

Whenever you see: *"longest substring/subarray satisfying a constraint"* → expand `right`; when the constraint breaks (a repeat), move `left` past the previous occurrence. Track the max window length.

---

## Understand with a diagram

```
"abcabcbb"
window [left..right], all distinct

right→ a b c   (len 3, max 3)
right=a again → seen at idx0 → jump left to idx0+1
continue... window never holds a duplicate
max window length = answer
```

Use `last[ch]` = last index of ch. When `ch` seen inside window, `left = max(left, last[ch]+1)`.

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — all substrings — O(n²) or O(n³)
Check each substring for uniqueness with a set.

### 🟢 Optimal — sliding window — O(n)
One pass with a last-seen map. `left` only moves forward. Each character visited at most twice.

---

## Pseudocode (Optimal)

```
function longest_unique(s):
    last = {}          ← char → last index seen
    left = 0
    best = 0
    for right, ch in enumerate(s):
        if ch in last and last[ch] >= left:
            left = last[ch] + 1        ← shrink past the repeat
        last[ch] = right
        best = max(best, right - left + 1)
    return best
```

---

## Python Code

```python
def longest_unique_substring(s: str) -> int:
    last = {}                    # char -> last index
    left = 0
    best = 0
    for right, ch in enumerate(s):
        if ch in last and last[ch] >= left:
            left = last[ch] + 1  # move left past previous occurrence
        last[ch] = right
        best = max(best, right - left + 1)
    return best


# Test
print(longest_unique_substring("abcabcbb"))   # 3
print(longest_unique_substring("bbbbb"))      # 1
print(longest_unique_substring("pwwkew"))     # 3
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²)–O(n³) | O(min(n, alphabet)) |
| Sliding window | O(n) | O(min(n, alphabet)) |

---

## Edge Cases to remember

- **`last[ch] >= left` guard** — only jump `left` if the previous occurrence is *inside* the current window; a stale index outside must not pull `left` backward.
- **Empty string** → 0.
- **All unique** → whole length.
- **`left = max(left, last[ch]+1)`** is an equivalent, robust form of the guard.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Longest substring ≤ K distinct (Q3) | Window + count map |
| 2 | Minimum window substring (LC 76) | Window + need map |
| 3 | Longest repeating char replacement (LC 424) | Window + max freq |
| 4 | Permutation in string (LC 567) | Fixed window counts |
| 5 | Fruit into baskets (LC 904) | ≤2 distinct window |
| 6 | Subarray with sum/product constraint | Variable window |
| 7 | Max consecutive ones III (LC 1004) | Window with budget |
| 8 | Longest nice subarray | Window + bitmask |

---

## The ONE trick to remember

```
"EXPAND RIGHT; ON A REPEAT, JUMP LEFT PAST THE PREVIOUS OCCURRENCE."
```

Keep a last-seen index per character. When the current char was seen inside the window, move `left` to just after it. The window always stays duplicate-free; track its max length.

> Memory hook: "Slide the right edge in, snap the left edge past any repeat."

---
