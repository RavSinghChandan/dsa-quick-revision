# ⚡ Quick Note · Day 12 · Q10 · Dedup File Lines [FILES]
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Remove duplicate lines, keep first-seen order."

## Pattern
`Streaming pass + 'seen' set (O(1) membership)`

## Visual in your head
```
seen=set()
for line in f:
  if line not in seen: emit; seen.add(line)
→ order preserved (bare set() would NOT)
```

## The trick (say it out loud)
> "Seen set for O(1) membership; emit in read order. Or one-liner dict.fromkeys(items)."

## Code skeleton
```python
seen=set()
for line in fin:
    if line not in seen: seen.add(line); fout.write(line)
# in-memory: list(dict.fromkeys(items))
```

## Complexity
- Time O(n) · Space O(unique)  (naive `in list` is O(n²))

## This trick solves more
Remove dups keep order · Contains Duplicate (217) · Unique visitors · Dedup URLs · File set ops · Longest substr (3)
