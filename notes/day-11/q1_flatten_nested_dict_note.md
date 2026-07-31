# ⚡ Quick Note · Day 11 · Q1 · Flatten Nested Dictionary
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Nested dict → flat dict with dotted keys: `{'b':{'c':2}}` → `{'b.c':2}`"

---

## Pattern
`Recursion (DFS) carrying a prefix string`

---

## Visual in your head
```
{"a":1, "b":{"c":2, "d":{"e":3}}}
   |         |          |
  "a"      "b.c"     "b.d.e"

value is dict? → recurse (prefix += key)
else          → leaf, store prefix.key
```

---

## The trick (say it out loud)
> "If value is a dict, recurse with a longer prefix. Else it's a leaf — store it."

---

## Code skeleton (3 lines to recall)
```python
new_key = f"{prefix}.{key}" if prefix else key
if isinstance(value, dict): out.update(flatten(value, new_key))
else: out[new_key] = value
```

---

## Complexity
- Time: O(n) · Space: O(depth) recursion stack

---

## This trick solves more questions
Unflatten dict · Flatten JSON response · json_normalize · Config dotted-path lookup · Flatten nested list
