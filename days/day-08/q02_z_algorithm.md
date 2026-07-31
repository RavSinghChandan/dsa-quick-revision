# Day 8 — Strings & Graphs · Q2 · Z Algorithm

---

## What is this question actually asking?

Compute the **Z-array**: for each position `i`, the length of the longest substring starting at `i` that **matches a prefix** of the string. Used for O(n) pattern matching.

```
s = "aabxaab"  → Z = [_, 1, 0, 0, 3, 1, 0]
(at index 4 "aab" matches the prefix "aab" → Z[4]=3)
```

> A linear string-matching tool. Concatenate `pattern + separator + text`; wherever `Z[i] == len(pattern)`, the pattern occurs.

---

## Pattern

```
PATTERN: Z-array with a [L, R] window (reuse previously computed matches)
```

Whenever you see: *"match prefix everywhere / linear pattern search"* → maintain a `[L, R]` interval (the rightmost prefix-match segment). Inside it, reuse earlier Z-values; outside, extend by direct comparison.

---

## Understand with a diagram

```
[L, R] = rightmost segment that matches a prefix.
For i in [L, R]:
   k = i - L (mirror position)
   if Z[k] < R - i + 1:  Z[i] = Z[k]        ← fully inside, reuse
   else: extend from R by comparing chars   ← may grow the window

Pattern search: build s = pat + "#" + text; occurrence where Z[i] == len(pat).
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — compare prefix at each i — O(n²)
For each i, compare forward with the prefix.

### 🟢 Optimal — Z-array — O(n)
The `[L, R]` window lets you reuse computed matches; total comparisons are linear.

---

## Pseudocode (Optimal)

```
function z_function(s):
    n = len(s); Z = [0]*n; Z[0] = n
    L = R = 0
    for i in 1..n-1:
        if i < R: Z[i] = min(R - i, Z[i - L])
        while i + Z[i] < n and s[Z[i]] == s[i + Z[i]]: Z[i]++
        if i + Z[i] > R: L = i; R = i + Z[i]
    return Z
```

---

## Python Code

```python
def z_function(s: str) -> list[int]:
    n = len(s)
    Z = [0] * n
    Z[0] = n
    L = R = 0
    for i in range(1, n):
        if i < R:
            Z[i] = min(R - i, Z[i - L])       # reuse inside the window
        while i + Z[i] < n and s[Z[i]] == s[i + Z[i]]:
            Z[i] += 1                          # extend by comparison
        if i + Z[i] > R:
            L, R = i, i + Z[i]                 # update rightmost window
    return Z

def z_search(text: str, pat: str) -> list[int]:
    s = pat + "\x00" + text                    # separator not in inputs
    Z = z_function(s)
    m = len(pat)
    return [i - m - 1 for i in range(len(s)) if Z[i] == m]


# Test
print(z_function("aabxaab"))            # [7, 1, 0, 0, 3, 1, 0]
print(z_search("abedabc", "abc"))       # [4]
print(z_search("aaaaa", "aa"))          # [0, 1, 2, 3]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(1) |
| Z-algorithm | O(n) | O(n) |

---

## Edge Cases to remember

- **Z[0]** is conventionally `n` (or 0/undefined); be consistent — for search it doesn't matter.
- **Separator char** must not appear in pattern or text (`\x00` or `#`).
- **`min(R - i, Z[i-L])`** — cap the reused value at the window boundary before extending.
- **Occurrence index** in `z_search` = `i - m - 1` (account for pattern + separator prefix).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | KMP pattern matching (Q3) | Alternative linear matcher |
| 2 | Longest Happy Prefix (Q4) | Prefix function |
| 3 | Shortest palindrome (LC 214) | Z / KMP on s + rev |
| 4 | String matching (LC 28) | Z-search |
| 5 | Count distinct substrings | Z / suffix structures |
| 6 | Period of a string | Z / failure |
| 7 | Repeated substring pattern (LC 459) | Prefix function |
| 8 | Search with wildcards (variants) | String DP |

---

## The ONE trick to remember

```
"Z[i] = MATCH LENGTH WITH THE PREFIX; REUSE INSIDE [L,R], EXTEND OUTSIDE."
```

Maintain the rightmost prefix-matching window `[L, R]`. Inside it, mirror a previously computed Z-value; then extend by direct comparison when needed. Concatenate `pat + sep + text` and look for `Z[i] == len(pat)` to find matches.

> Memory hook: "How far does this spot echo the start? Reuse the echo you already measured."

---
