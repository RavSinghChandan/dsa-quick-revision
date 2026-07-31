# ⚡ Quick Note · Day 8 — Strings & Graphs · Q4 · Longest Happy Prefix
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Longest non-empty prefix that is also a suffix (not whole string)."

## Pattern
`KMP LPS — answer = s[:lps[n-1]]`

## Visual in your head
```
build LPS (KMP failure function)
lps[n-1] = length of longest proper prefix==suffix
"ababab" → lps[-1]=4 → "abab"
```

## The trick (say it out loud)
> "It's the last value of the KMP LPS array. Answer = s[:lps[n-1]]."

## Code skeleton
```python
# build lps like KMP
return s[:lps[n-1]]
```

## Complexity
- Time O(n) · Space O(n)

## This trick solves more
KMP (Q3) · Repeated Substring (459) · Shortest Palindrome (214) · Period of string · Happy Prefix (1392)
