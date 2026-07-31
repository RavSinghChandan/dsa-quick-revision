# ⚡ Quick Note · Day 1 — Arrays · Q5 · Repeating & Missing
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "1..n array: one number repeats, one missing — find both."

## Pattern
`Sum & sum-of-squares equations (or XOR partition)`

## Visual in your head
```
x=repeat, y=missing
S-Sn = x-y
(P-Pn)/(S-Sn) = x+y
→ x=((x-y)+(x+y))/2, y=x-(x-y)
```

## The trick (say it out loud)
> "Two unknowns, two equations: sum diff gives x−y, square-sum diff gives x+y."

## Code skeleton
```python
diff=S-Sn; summ=(P-Pn)//diff
x=(diff+summ)//2; y=x-diff
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
Missing Number (268) · Find Duplicate (287) · Single Number (136) · Set Mismatch (645)
