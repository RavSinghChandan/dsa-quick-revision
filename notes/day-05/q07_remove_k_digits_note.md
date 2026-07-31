# ⚡ Quick Note · Day 5 — Stack & Greedy · Q7 · Remove K Digits
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Remove k digits to make the smallest number."

## Pattern
`Monotonic increasing stack (greedy removal)`

## Visual in your head
```
for d in num:
  while k>0 and stack[-1]>d: pop; k--
  push d
trim last k, strip leading zeros, "0" if empty
```

## The trick (say it out loud)
> "Pop a digit when it's bigger than the next (while k>0). Trim leftover k from the end. Strip leading zeros."

## Code skeleton
```python
for d in num:
    while k>0 and stack and stack[-1]>d: stack.pop(); k-=1
    stack.append(d)
res="".join(stack[:len(stack)-k]).lstrip("0")
return res or "0"
```

## Complexity
- Time O(n) · Space O(n)

## This trick solves more
Remove Dup Letters (316) · Smallest Subseq (1081) · Create Max Number (321) · Monotone Digits (738)
