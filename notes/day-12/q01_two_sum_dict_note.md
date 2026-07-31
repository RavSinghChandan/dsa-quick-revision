# ⚡ Quick Note · Day 12 · Q1 · Two Sum [DICTS]
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Find two indices whose values sum to target."

## Pattern
`Hash map "seen so far" — complement lookup`

## Visual in your head
```
for i,num: need = target-num
  need in seen? → return [seen[need], i]
  else store seen[num]=i
```

## The trick (say it out loud)
> "Store what you've seen; check if the complement is already there — before inserting."

## Code skeleton
```python
seen={}
for i,num in enumerate(nums):
    if target-num in seen: return [seen[target-num], i]
    seen[num]=i
```

## Complexity
- Time O(n) · Space O(n)

## This trick solves more
3Sum · Subarray sum = K · Contains Duplicate · First unique char · Pair with diff · Group anagrams
