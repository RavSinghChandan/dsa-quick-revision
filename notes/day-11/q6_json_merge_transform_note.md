# ⚡ Quick Note · Day 11 · Q6 · Deep-Merge JSON / Transform
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Merge two JSON objects; nested dicts blend recursively, override wins on clash."

---

## Pattern
`Recursion walking TWO dicts in parallel`

---

## Visual in your head
```
base + override, per key:
  both values dicts? → RECURSE (merge subtree)
  else              → override value wins / add new
⚠ {**a,**b} is SHALLOW → loses nested data
```

---

## The trick (say it out loud)
> "Both sides dict? recurse. Else override wins. Never use plain update() — it's shallow. deepcopy the base."

---

## Code skeleton (3 lines to recall)
```python
if key in result and isinstance(result[key], dict) and isinstance(val, dict):
    result[key] = deep_merge(result[key], val)
else: result[key] = val
```

---

## Complexity
- Time O(total keys) · Space O(depth) + copy

---

## This trick solves more questions
Config layering · Merge API responses · JSON diff · Group/pivot records · Key remap · Redux-style state update
