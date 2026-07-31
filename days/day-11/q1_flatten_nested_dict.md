# Day 11 · Q1 · Flatten a Nested Dictionary

---

## What is this question actually asking?

You are given a dictionary that has dictionaries inside it (nested), like a JSON config or an API response.
Turn it into a **single flat dictionary** where each nested key is joined by a separator (usually `.`).

```
Input:  {"a": 1, "b": {"c": 2, "d": {"e": 3}}}
Output: {"a": 1, "b.c": 2, "b.d.e": 3}
```

> Think of it like this: a nested dict is a folder tree. Flattening = writing every file's FULL path (`b/d/e`) on one line instead of walking folders.

---

## Pattern

```
PATTERN: Recursion (DFS on a tree) with a running "prefix"
```

Whenever you see: *"walk something that contains itself"* (dict inside dict, list inside list, tree, JSON) → think **recursion**, carry the path so far as a **prefix string**.

---

## Understand with a diagram

```
{
  "a": 1,                         →  a           = 1
  "b": {                          
        "c": 2,                   →  b.c         = 2
        "d": {                    
              "e": 3              →  b.d.e       = 3
             }
       }
}

Prefix builds up as we go deeper:
""  →  "b"  →  "b.d"  →  "b.d.e"
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — hardcode the levels

**Idea:** Loop level 1, then a loop inside for level 2, then level 3...

```
for k1 in d:
    if not dict: out[k1] = v
    else:
        for k2 in d[k1]:
            for k3 ...      ← breaks the moment nesting is deeper than you coded
```

**Why bad?** You don't know how deep the nesting goes. Fails on real JSON.

---

### 🟢 Optimal — Recursion with prefix — O(n)

**Core insight:**
Write ONE function that handles a dict at ANY depth. For each key:
- if the value is a dict → **recurse**, adding this key to the prefix
- else → it's a leaf → store `prefix.key = value`

This handles depth 1 or depth 100 with the same 6 lines.

```
flatten({"a":1,"b":{"c":2,"d":{"e":3}}}, prefix="")

k="a" val=1     (leaf)  → out["a"] = 1
k="b" val=dict  (recurse, prefix="b")
     k="c" val=2   (leaf) → out["b.c"] = 2
     k="d" val=dict (recurse, prefix="b.d")
          k="e" val=3 (leaf) → out["b.d.e"] = 3
```

---

## Pseudocode (Optimal)

```
function flatten(d, prefix, out):
    for key, value in d:
        new_key = prefix + "." + key   (or just key if prefix empty)
        if value is a dict:
            flatten(value, new_key, out)     ← go deeper
        else:
            out[new_key] = value             ← leaf, store it
    return out
```

---

## Python Code

```python
def flatten_dict(d: dict, prefix: str = "", sep: str = ".") -> dict:
    out = {}
    for key, value in d.items():
        new_key = f"{prefix}{sep}{key}" if prefix else key
        if isinstance(value, dict):
            out.update(flatten_dict(value, new_key, sep))  # recurse
        else:
            out[new_key] = value
    return out


# Test
data = {"a": 1, "b": {"c": 2, "d": {"e": 3}}}
print(flatten_dict(data))
# {'a': 1, 'b.c': 2, 'b.d.e': 3}
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Recursion | O(n) — visits each key once | O(d) recursion stack, d = depth |

---

## Edge Cases to remember

- **Empty dict** `{}` → return `{}`.
- **Empty nested dict** `{"a": {}}` → `a` maps to nothing; decide: skip it, or keep `{"a": {}}`. State your choice in interview.
- **Lists inside** `{"a": [1, 2]}` → the basic version keeps the list as-is (`a` = `[1,2]`). Interviewer may ask you to also index it as `a.0`, `a.1` (see Flatten Nested List, Q2).
- **Key already contains the separator** → can create ambiguous keys; mention it.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Unflatten a dict (reverse it) | Split key on `.`, rebuild nesting |
| 2 | Flatten nested JSON API response | Same, real-world |
| 3 | Flatten nested list (Q2) | Recursion, no keys |
| 4 | Config resolver (`a.b.c` lookups) | Dotted-path access |
| 5 | pandas `json_normalize` internals | This IS the algorithm |
| 6 | Nested defaultdict → flat | Same recursion |
| 7 | Directory tree → file paths | Dict = folder tree |
| 8 | GraphQL response flattening | Nested objects |

---

## The ONE trick to remember

```
"CARRY THE PREFIX DOWN, RECURSE ON DICTS, STORE ON LEAVES"
```

The whole trick is a single `isinstance(value, dict)` check: dict → recurse (deeper), not a dict → it's a leaf (store it). The `prefix` string is just the path you've walked so far.

> Memory hook: "Nested dict = folder tree. Flatten = write the full file path."

---
