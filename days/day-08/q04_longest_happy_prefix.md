# Day 8 — Strings & Graphs · Q4 · Longest Happy Prefix

---

## What is this question actually asking?

A "happy prefix" is a **non-empty prefix that is also a suffix** (but not the whole string). Return the **longest** one.

```
"level"   → "l"
"ababab"  → "abab"
"abcdef"  → ""       (no proper prefix = suffix)
```

> This is **literally the last value of the KMP LPS array**. `lps[n-1]` = length of the longest proper prefix-suffix.

---

## Pattern

```
PATTERN: KMP LPS (failure function) — answer = prefix of length lps[n-1]
```

Whenever you see: *"longest prefix that is also a suffix / border of a string"* → build the LPS array; `lps[n-1]` is exactly the length you want. The happy prefix is `s[:lps[n-1]]`.

---

## Understand with a diagram

```
s = "ababab"
LPS = [0,0,1,2,3,4]
lps[n-1] = 4 → longest proper prefix==suffix has length 4 → "abab"

(Prefixes:   a  ab  aba  abab ...
 Suffixes:      ...  bab  abab ...
 "abab" is both a prefix and a suffix → happy prefix.)
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — try every length — O(n²)
For each length L from n-1 down, compare `s[:L]` with `s[n-L:]`.

### 🟢 Optimal — LPS array — O(n)
Build the KMP failure function once; the last entry is the answer length.

---

## Pseudocode (Optimal)

```
function longest_happy_prefix(s):
    lps = build_lps(s)            ← same as KMP
    return s[: lps[n-1]]
```

---

## Python Code

```python
def longest_happy_prefix(s: str) -> str:
    n = len(s)
    lps = [0] * n
    length = 0
    i = 1
    while i < n:
        if s[i] == s[length]:
            length += 1
            lps[i] = length
            i += 1
        elif length > 0:
            length = lps[length - 1]      # fall back
        else:
            lps[i] = 0
            i += 1
    return s[: lps[n - 1]]                 # longest proper prefix == suffix


# Test
print(longest_happy_prefix("level"))      # "l"
print(longest_happy_prefix("ababab"))     # "abab"
print(longest_happy_prefix("abcdef"))     # ""
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n²) | O(1) |
| LPS | O(n) | O(n) |

---

## Edge Cases to remember

- **"Proper" prefix** — excludes the whole string; `lps` never equals `n` for a proper border.
- **No border** → `lps[n-1] = 0` → return `""`.
- **Hashing alternative** — compare prefix/suffix hashes for each length; LPS is cleaner and exact.
- **Single char** → `""` (no proper prefix that's also a suffix).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | KMP matching (Q3) | Same LPS |
| 2 | Repeated Substring Pattern (LC 459) | Uses lps[n-1] |
| 3 | Shortest Palindrome (LC 214) | LPS of s + "#" + rev(s) |
| 4 | Period of a string | n − lps[n-1] |
| 5 | Longest Happy Prefix (LC 1392) | This problem |
| 6 | Border array of a string | LPS |
| 7 | Smallest repeating unit | Period |
| 8 | String compression via period | LPS |

---

## The ONE trick to remember

```
"ANSWER = s[: lps[n-1]] — THE LAST VALUE OF THE KMP FAILURE ARRAY."
```

The longest prefix that is also a suffix is precisely what the KMP LPS array's final entry measures. Build LPS in O(n) and slice.

> Memory hook: "Happy prefix = the KMP failure function's grand finale."

---
