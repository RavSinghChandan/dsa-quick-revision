# Day 10 — Math, Bit & Trie · Q7 · Number of Distinct Substrings

---

## What is this question actually asking?

Count how many **distinct non-empty substrings** a string has.

```
"abab" → 7   ("a","b","ab","ba","aba","bab","abab")
"aaa"  → 3   ("a","aa","aaa")
```

> A classic Trie (of suffixes) application: every substring is a prefix of some suffix. Insert all suffixes into a Trie; the number of Trie **nodes** = number of distinct substrings.

---

## Pattern

```
PATTERN: Suffix Trie — count nodes = count distinct substrings
```

Whenever you see: *"count distinct substrings"* → each distinct substring corresponds to exactly one path (node) in a Trie built from all suffixes of the string. Insert every suffix; count nodes created (excluding the root).

---

## Understand with a diagram

```
substring = prefix of some suffix.
Insert all suffixes of "abab": "abab","bab","ab","b"

Each new NODE created = one new distinct substring.
Total nodes (excluding root) = number of distinct substrings.

"abab" → 7 nodes → 7 distinct substrings.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — set of all substrings — O(n²) substrings, O(n³) with slicing
Generate every substring, put in a set. O(n²) substrings each O(n) to build/hash.

### 🟢 Trie of suffixes — O(n²) time/space
Insert n suffixes (total O(n²) chars); count nodes. Clear and interview-friendly.

### 🟢 Optimal — Suffix Automaton / Suffix Array + LCP — O(n) or O(n log n)
Advanced; mention it. For interviews the Trie or set is usually expected.

---

## Pseudocode (Suffix Trie)

```
function count_distinct_substrings(s):
    root = TrieNode()
    count = 0
    for i in 0..n-1:              ← each suffix starting at i
        node = root
        for j in i..n-1:
            ch = s[j]
            if ch not in node.children:
                node.children[ch] = TrieNode()
                count += 1        ← new node = new distinct substring
            node = node.children[ch]
    return count
```

---

## Python Code

```python
def count_distinct_substrings(s: str) -> int:
    root = {}
    count = 0
    n = len(s)
    for i in range(n):                # each suffix
        node = root
        for j in range(i, n):
            ch = s[j]
            if ch not in node:
                node[ch] = {}
                count += 1            # new node → new distinct substring
            node = node[ch]
    return count


# Test
print(count_distinct_substrings("abab"))   # 7
print(count_distinct_substrings("aaa"))    # 3
print(count_distinct_substrings("abc"))    # 6
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Set of substrings | O(n³) (slicing) / O(n²) hashing | O(n²) |
| Suffix Trie | O(n²) | O(n²) |
| Suffix Automaton | O(n) | O(n) |

---

## Edge Cases to remember

- **Each new Trie node = one new distinct substring** — the count is just nodes created.
- **Non-empty substrings** — don't count the root.
- **Total substrings (with dups) = n(n+1)/2** — distinct is ≤ that; equality iff all chars distinct.
- **Large n** → the O(n²) Trie may be too big; suffix automaton is the O(n) answer.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Distinct Subsequences count | Different DP |
| 2 | Longest Repeated Substring | Suffix structures |
| 3 | Longest Common Substring | Suffix structures |
| 4 | Count distinct substrings (LC 1698) | This |
| 5 | Suffix array + LCP problems | Suffix array |
| 6 | Number of matching subsequences | Trie |
| 7 | Palindromic substrings count | Different (expand) |
| 8 | Substring hashing dedup | Rolling hash set |

---

## The ONE trick to remember

```
"INSERT ALL SUFFIXES INTO A TRIE; NODE COUNT = DISTINCT SUBSTRINGS."
```

Every substring is a prefix of some suffix, so building a Trie of all suffixes gives one node per distinct substring. Count the nodes created (excluding root). O(n²) with a Trie; O(n) with a suffix automaton.

> Memory hook: "Every substring is the start of some suffix — count the unique paths."

---
