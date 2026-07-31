# ⚡ Quick Note · Day 11 · Q2 · Flatten Nested List
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "List of lists (any depth) → one flat list in order."

---

## Pattern
`Recursion (DFS)` or `Iterative stack` for deep nesting

---

## Visual in your head
```
[1, [2, [3,4], 5]]  →  [1,2,3,4,5]

element is list? → recurse / push items
else            → append value
```

---

## The trick (say it out loud)
> "Is it a list? Dive in. Else keep it. Deep nesting → use a stack, not recursion."

---

## Code skeleton (3 lines to recall)
```python
for x in lst:
    if isinstance(x, list): out.extend(flatten(x))
    else: out.append(x)
```

---

## Complexity
- Time: O(n) · Space: O(depth) recursion (or O(n) stack)

---

## This trick solves more questions
Nested List Iterator (LC341) · Nested Weight Sum (LC339) · Flatten multilevel list (LC430) · numpy ravel · Directory walk
