# ⚡ Quick Note · Day 1 — Arrays · Q4 · Majority Element II (>n/3)
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Find all elements appearing more than n/3 times (at most two)."

## Pattern
`Boyer-Moore voting with TWO candidates + verify`

## Visual in your head
```
c1,c2 with counts. per x:
  matches c1/c2 → count++
  a count is 0 → put x there
  else → n1--, n2--
verify survivors count > n/3
```

## The trick (say it out loud)
> "Two candidate slots. A third value cancels one from each. ALWAYS verify at the end."

## Code skeleton
```python
if c1==x: n1+=1
elif c2==x: n2+=1
elif n1==0: c1,n1=x,1
elif n2==0: c2,n2=x,1
else: n1-=1; n2-=1
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
Majority > n/2 (169) · Majority > n/k · >25% element · Check majority exists
