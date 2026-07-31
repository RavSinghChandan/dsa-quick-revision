# Day 8 — Strings & Graphs · Q1 · Rabin-Karp Pattern Matching

---

## What is this question actually asking?

Find all occurrences of a pattern `p` inside a text `t` — using **hashing** to compare windows in O(1) average.

```
text="abedabc", pattern="abc" → found at index 4
```

> Tests the **rolling hash**: hash the pattern once, then slide a window over the text updating the hash in O(1) per step (remove the leaving char, add the entering char).

---

## Pattern

```
PATTERN: Rolling Hash (polynomial hash + sliding window)
```

Whenever you see: *"substring search / repeated substring / compare many windows"* → hash the pattern and each text window; a rolling formula updates the window hash in O(1). Verify character-by-character on a hash match (to guard against collisions).

---

## Understand with a diagram

```
hash(window) as base-b polynomial mod m:
   h = c0*b^(L-1) + c1*b^(L-2) + ... + c(L-1)

slide by one:
   h = (h - leaving_char * b^(L-1)) * b + entering_char   (mod m)

pattern hash computed once. On hash == pattern_hash → verify exact chars.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — compare at every position — O(n·m)
Check the pattern against each start.

### 🟢 Optimal — rolling hash — O(n + m) average
Hash the pattern, roll a window hash across the text in O(1) steps; verify on matches. Worst case O(n·m) if many hash collisions (rare with a good mod).

---

## Pseudocode (Optimal)

```
function rabin_karp(text, pat):
    n, m = len(text), len(pat)
    b, mod = 256, large_prime
    pat_hash = hash(pat)
    win_hash = hash(text[0:m])
    high = b^(m-1) mod mod
    result = []
    for i in 0..n-m:
        if win_hash == pat_hash and text[i:i+m] == pat:   ← verify
            result.append(i)
        if i < n-m:
            win_hash = ((win_hash - text[i]*high) * b + text[i+m]) mod mod
    return result
```

---

## Python Code

```python
def rabin_karp(text: str, pat: str) -> list[int]:
    n, m = len(text), len(pat)
    if m == 0 or m > n:
        return []
    base, mod = 256, 1_000_000_007
    high = pow(base, m - 1, mod)          # base^(m-1) mod mod

    def h(s):
        v = 0
        for ch in s:
            v = (v * base + ord(ch)) % mod
        return v

    pat_hash = h(pat)
    win_hash = h(text[:m])
    result = []
    for i in range(n - m + 1):
        if win_hash == pat_hash and text[i:i + m] == pat:   # verify (collision guard)
            result.append(i)
        if i < n - m:                     # roll the hash forward
            win_hash = ((win_hash - ord(text[i]) * high) * base
                        + ord(text[i + m])) % mod
    return result


# Test
print(rabin_karp("abedabc", "abc"))          # [4]
print(rabin_karp("aaaaa", "aa"))             # [0, 1, 2, 3]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n·m) | O(1) |
| Rabin-Karp | O(n + m) avg | O(1) |

---

## Edge Cases to remember

- **Verify on hash match** — hashes can collide; confirm the actual substring. Without this you get false positives.
- **Modular arithmetic** — subtract then keep positive: Python's `%` already returns non-negative; in C++/Java add `mod` before `%`.
- **Precompute `base^(m-1)`** — needed to remove the leaving char.
- **Empty / too-long pattern** → handle up front.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Repeated DNA Sequences (LC 187) | Rolling hash of length 10 |
| 2 | Longest Duplicate Substring (LC 1044) | Binary search + rolling hash |
| 3 | Strstr / Implement indexOf (LC 28) | Substring search |
| 4 | Distinct substrings count | Hash set of hashes |
| 5 | KMP (Q3) | Alternative matcher |
| 6 | Z-algorithm (Q2) | Alternative matcher |
| 7 | Longest happy prefix (Q4) | Prefix-hash |
| 8 | Find anagram substrings | Rolling counts |

---

## The ONE trick to remember

```
"ROLL THE HASH IN O(1); VERIFY EXACT CHARS ON A HASH MATCH."
```

Hash the pattern and the first window; slide by removing the outgoing char's contribution and adding the incoming char — O(1) per step. Always verify the real substring on a hash hit to avoid collision false-positives.

> Memory hook: "Fingerprint the window; slide the fingerprint; double-check on a match."

---
