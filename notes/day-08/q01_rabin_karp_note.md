# ⚡ Quick Note · Day 8 — Strings & Graphs · Q1 · Rabin-Karp
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Find pattern in text using a rolling hash."

## Pattern
`Rolling hash (polynomial) + sliding window`

## Visual in your head
```
h(window) = polynomial base-b mod m
slide: h = (h - out*b^(m-1))*b + in  (mod m)
hash match → verify exact chars
```

## The trick (say it out loud)
> "Roll the hash in O(1); verify exact chars on a hash match (collisions)."

## Code skeleton
```python
win = ((win - ord(text[i])*high)*base + ord(text[i+m])) % mod
if win==pat_hash and text[i+1:i+1+m]==pat: found
```

## Complexity
- Time O(n+m) avg · Space O(1)

## This trick solves more
Repeated DNA (187) · Longest Dup Substring (1044) · strStr (28) · Distinct substrings · KMP/Z alt
