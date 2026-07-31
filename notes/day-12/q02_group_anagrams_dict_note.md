# ⚡ Quick Note · Day 12 · Q2 · Group Anagrams [DICTS]
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Group words made of the same letters."

## Pattern
`Hash map grouping by a canonical signature`

## Visual in your head
```
"eat","tea","ate" → sort → "aet" → same bucket
key = "".join(sorted(word))   (or 26-letter count tuple)
```

## The trick (say it out loud)
> "Make a canonical key per item, defaultdict(list), append. Same fingerprint → same bucket."

## Code skeleton
```python
groups=defaultdict(list)
for w in words: groups["".join(sorted(w))].append(w)
return list(groups.values())
```

## Complexity
- Sort key O(n·k log k) · Count key O(n·k) · Space O(n·k)

## This trick solves more
Valid Anagram · Group by rule · Duplicate files by hash · Isomorphic strings · ML partition-by-feature
