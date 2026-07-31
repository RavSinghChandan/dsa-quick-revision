# ⚡ Quick Note · Day 10 — Math, Bit & Trie · Q3 · Single Number III
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Two numbers appear once, rest twice — find both."

## Pattern
`XOR all + split by a differing bit`

## Visual in your head
```
x = XOR all = a^b (pairs cancel)
diff = x & (-x)  (lowest set bit)
group by bit → XOR each → a and b
```

## The trick (say it out loud)
> "XOR all gives a^b. A set bit splits a from b. XOR each group to isolate them."

## Code skeleton
```python
x=0
for n in nums: x^=n
diff=x&(-x); a=b=0
for n in nums:
    if n&diff: a^=n
    else: b^=n
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
Single Number I (136) · II (137, mod3) · III (260) · Missing Number (268) · Repeat&Missing
