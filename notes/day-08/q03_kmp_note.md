# ⚡ Quick Note · Day 8 — Strings & Graphs · Q3 · KMP (LPS)
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Linear pattern matching using the LPS (failure) array."

## Pattern
`KMP — precompute LPS, text pointer never backs up`

## Visual in your head
```
LPS[i]=longest proper prefix==suffix of pat[0..i]
match → i++,j++; j==m → record, j=lps[j-1]
mismatch j>0 → j=lps[j-1]; else i++
```

## The trick (say it out loud)
> "LPS = longest prefix that's also suffix. On mismatch, jump j=lps[j-1]; i never moves back."

## Code skeleton
```python
if text[i]==pat[j]: i+=1; j+=1; (j==m → record, j=lps[j-1])
elif j>0: j=lps[j-1]
else: i+=1
```

## Complexity
- Time O(n+m) · Space O(m)

## This trick solves more
strStr (28) · Repeated Substring (459) · Shortest Palindrome (214) · Happy Prefix (Q4) · Period of string
