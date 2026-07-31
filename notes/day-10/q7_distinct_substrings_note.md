# ⚡ Quick Note · Day 10 — Math, Bit & Trie · Q7 · Distinct Substrings
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Count distinct non-empty substrings."

## Pattern
`Suffix Trie — node count = distinct substrings`

## Visual in your head
```
substring = prefix of some suffix
insert all suffixes into a Trie
each NEW node = one new distinct substring
```

## The trick (say it out loud)
> "Insert every suffix into a Trie; the number of nodes = number of distinct substrings."

## Code skeleton
```python
for i in range(n):
    node=root
    for j in range(i,n):
        if s[j] not in node: node[s[j]]={}; count+=1
        node=node[s[j]]
```

## Complexity
- Trie O(n²) · Suffix Automaton O(n) · Space O(n²)

## This trick solves more
Count Distinct Substrings (1698) · Longest Repeated Substring · Suffix array/LCP · Matching subsequences
