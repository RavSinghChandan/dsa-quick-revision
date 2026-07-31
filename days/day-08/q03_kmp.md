# Day 8 — Strings & Graphs · Q3 · KMP Pattern Matching (LPS Array)

---

## What is this question actually asking?

Find all occurrences of a pattern in text in **O(n + m)** using the **LPS (Longest Proper Prefix that is also Suffix)** array to avoid re-checking characters.

```
text="ababcabab", pattern="abab" → indices [0, 5]
```

> The classic linear matcher. The LPS array tells you, on a mismatch, how far back to jump the pattern without re-scanning text.

---

## Pattern

```
PATTERN: KMP — precompute LPS (failure function), never move text pointer back
```

Whenever you see: *"substring search / periodicity / prefix-suffix overlap"* → build the LPS array of the pattern; scan the text once, and on a mismatch fall back to `lps[j-1]` instead of restarting.

---

## Understand with a diagram

```
LPS[i] = length of the longest proper prefix of pat[0..i] that is also a suffix.

pat = "abab" → LPS = [0,0,1,2]
   at 'a','b': 0,0 ; "aba" has prefix "a"=suffix "a" → 1 ; "abab" → "ab" → 2

Matching: i over text, j over pattern.
  match → i++, j++ ; if j==m → record (i-m), j = lps[j-1]
  mismatch & j>0 → j = lps[j-1]   (jump, don't move i)
  mismatch & j==0 → i++
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — O(n·m)
Re-check from each start on mismatch.

### 🟢 Optimal — KMP — O(n + m)
LPS build is O(m); the search is O(n). The text pointer never goes backward.

---

## Pseudocode (Optimal)

```
function build_lps(pat):
    lps = [0]*m; length = 0; i = 1
    while i < m:
        if pat[i] == pat[length]: length++; lps[i] = length; i++
        elif length > 0: length = lps[length-1]     ← fall back
        else: lps[i] = 0; i++
    return lps

function kmp(text, pat):
    lps = build_lps(pat); res = []; i = j = 0
    while i < n:
        if text[i] == pat[j]: i++; j++
        if j == m: res.append(i-m); j = lps[j-1]
        elif i < n and text[i] != pat[j]:
            if j > 0: j = lps[j-1]
            else: i++
    return res
```

---

## Python Code

```python
def build_lps(pat: str) -> list[int]:
    m = len(pat)
    lps = [0] * m
    length = 0
    i = 1
    while i < m:
        if pat[i] == pat[length]:
            length += 1
            lps[i] = length
            i += 1
        elif length > 0:
            length = lps[length - 1]      # fall back within the pattern
        else:
            lps[i] = 0
            i += 1
    return lps

def kmp_search(text: str, pat: str) -> list[int]:
    n, m = len(text), len(pat)
    if m == 0:
        return []
    lps = build_lps(pat)
    res = []
    i = j = 0
    while i < n:
        if text[i] == pat[j]:
            i += 1
            j += 1
            if j == m:
                res.append(i - m)
                j = lps[j - 1]            # continue searching
        elif j > 0:
            j = lps[j - 1]                # jump using LPS
        else:
            i += 1
    return res


# Test
print(build_lps("abab"))                  # [0, 0, 1, 2]
print(kmp_search("ababcabab", "abab"))    # [0, 5]
print(kmp_search("aaaaa", "aa"))          # [0, 1, 2, 3]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Brute | O(n·m) | O(1) |
| KMP | O(n + m) | O(m) LPS |

---

## Edge Cases to remember

- **LPS is "proper" prefix** — can't be the whole string; `lps[0] = 0` always.
- **Fall back to `lps[length-1]`**, not 0 — this is what makes it linear (avoids re-scanning).
- **Text pointer `i` never decreases** — the invariant that guarantees O(n).
- **After a full match**, set `j = lps[j-1]` to find overlapping occurrences.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Implement strStr (LC 28) | KMP search |
| 2 | Repeated Substring Pattern (LC 459) | `n % (n - lps[n-1]) == 0` |
| 3 | Shortest Palindrome (LC 214) | LPS of s + rev |
| 4 | Longest Happy Prefix (Q4) | LPS last value |
| 5 | Z-algorithm (Q2) | Alternative matcher |
| 6 | Find period of string | n − lps[n-1] |
| 7 | Count pattern occurrences | KMP |
| 8 | String rotation check | Concatenation + KMP |

---

## The ONE trick to remember

```
"LPS[i] = LONGEST PREFIX==SUFFIX; ON MISMATCH JUMP j = lps[j-1] (i NEVER BACKS UP)."
```

The failure function encodes how much of the pattern you've already matched that can be reused. On a mismatch you slide the pattern forward via `lps[j-1]` without re-reading text — giving O(n + m).

> Memory hook: "Don't restart — the LPS remembers how far you can safely skip."

---
