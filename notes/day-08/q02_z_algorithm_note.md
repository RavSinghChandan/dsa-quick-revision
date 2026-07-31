# ⚡ Quick Note · Day 8 — Strings & Graphs · Q2 · Z Algorithm
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Z[i] = longest substring at i matching the prefix; linear pattern search."

## Pattern
`Z-array with [L,R] window reuse`

## Visual in your head
```
[L,R] = rightmost prefix-match segment
i<R: Z[i]=min(R-i, Z[i-L])   (reuse)
then extend by comparison
search: pat+'#'+text, Z[i]==len(pat)
```

## The trick (say it out loud)
> "Reuse Z inside the [L,R] window, extend outside. Match where Z[i] == len(pattern)."

## Code skeleton
```python
if i<R: Z[i]=min(R-i, Z[i-L])
while i+Z[i]<n and s[Z[i]]==s[i+Z[i]]: Z[i]+=1
if i+Z[i]>R: L,R=i,i+Z[i]
```

## Complexity
- Time O(n) · Space O(n)

## This trick solves more
KMP · Longest Happy Prefix · Shortest Palindrome (214) · strStr (28) · Repeated Substring (459)
