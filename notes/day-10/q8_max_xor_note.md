# ⚡ Quick Note · Day 10 — Math, Bit & Trie · Q8 · Maximum XOR
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Max a XOR b over all pairs."

## Pattern
`Binary (bit) Trie — greedily pick the opposite bit`

## Visual in your head
```
insert numbers as 32-bit paths (MSB→LSB)
for x: at each bit go to OPPOSITE branch if exists
  → sets that XOR bit to 1
accumulate, track best
```

## The trick (say it out loud)
> "Bit Trie, MSB→LSB. Walk toward the opposite bit each level — every mismatch is a 1 in the XOR."

## Code skeleton
```python
want = 1 - bit
if want in node: cur|=(1<<b); node=node[want]
else: node=node[bit]
```

## Complexity
- Time O(n·B) · Space O(n·B)

## This trick solves more
Max XOR (421) · Max XOR With Element (1707) · Pairs XOR in range (1803) · Subarray max XOR · XOR Queries (1310)
