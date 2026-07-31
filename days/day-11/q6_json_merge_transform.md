# Day 11 · Q6 · Deep-Merge JSON / Data Transformation

---

## What is this question actually asking?

You have two (or more) JSON objects — e.g. a **default config** and a **user override**, or two API responses. Combine them into one, where nested objects **merge recursively** and the second one **wins** on conflicts.

```
base:     {"model": {"name": "gpt", "temp": 0.7}, "retries": 3}
override: {"model": {"temp": 0.2}, "stream": true}

merged:   {"model": {"name": "gpt", "temp": 0.2}, "retries": 3, "stream": true}
                                   ▲ override won      ▲ kept        ▲ added
```

A plain `dict.update()` is NOT enough — it would replace the whole `model` object, losing `name`. You need a **deep (recursive) merge**.

> Real AI use: merging config layers (defaults → env → CLI), combining partial API responses, assembling a record from multiple sources in a pipeline.

---

## Pattern

```
PATTERN: Recursion mirroring the structure (parallel walk of two dicts)
```

Same recursion family as flatten (Q1) — but now you walk **two** dicts at once. Rule per key: *both values dicts → merge deeper; otherwise → the override value wins.*

---

## Understand with a diagram

```
       base                    override
   ┌───────────┐            ┌───────────┐
   │ model ────┼── dict ────┼─ model    │  both dicts → RECURSE
   │ retries 3 │            │ stream T  │  only in one → copy over
   └───────────┘            └───────────┘

recurse on model:
   name "gpt"   (only in base)      → keep
   temp 0.7 vs 0.2 (both, leaf)     → override wins → 0.2
```

---

## Brute Force → Better → Optimal

### 🔴 Wrong — shallow update

```
merged = {**base, **override}       ← override's "model" REPLACES base's "model"
# → loses model.name entirely
```

**Why wrong?** `{**a, **b}` and `dict.update()` only merge the **top level**. Any nested dict from `override` overwrites the whole subtree.

---

### 🟢 Optimal — Recursive deep merge — O(total keys)

**Core insight:** Copy base, then for every key in override:
- key missing in base → just set it.
- **both** values are dicts → **recurse** (merge the subtrees).
- otherwise → override's value **wins** (leaf conflict).

---

## Pseudocode (Optimal)

```
function deep_merge(base, override):
    result = copy of base
    for key, val in override:
        if key in result and both result[key] and val are dicts:
            result[key] = deep_merge(result[key], val)   ← go deeper
        else:
            result[key] = val                            ← override wins / new key
    return result
```

---

## Python Code

```python
import copy

def deep_merge(base: dict, override: dict) -> dict:
    result = copy.deepcopy(base)          # don't mutate the caller's dict
    for key, val in override.items():
        if (key in result
                and isinstance(result[key], dict)
                and isinstance(val, dict)):
            result[key] = deep_merge(result[key], val)   # recurse into subtree
        else:
            result[key] = val                            # override wins / add new
    return result


# Test
base = {"model": {"name": "gpt", "temp": 0.7}, "retries": 3}
override = {"model": {"temp": 0.2}, "stream": True}
print(deep_merge(base, override))
# {'model': {'name': 'gpt', 'temp': 0.2}, 'retries': 3, 'stream': True}

# --- Bonus: reshape a list of records (common transform) ---
records = [
    {"user": "a", "score": 10},
    {"user": "b", "score": 20},
    {"user": "a", "score": 5},
]
# group scores by user
grouped = {}
for r in records:
    grouped.setdefault(r["user"], []).append(r["score"])
print(grouped)   # {'a': [10, 5], 'b': [20]}
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Deep merge | O(total keys across both) | O(depth) recursion + copy |

---

## Edge Cases to remember

- **Shallow trap** — say clearly that `{**a, **b}` / `update()` is shallow and loses nested data. This is the #1 thing they check.
- **List values** — do you *replace* the list, *concatenate*, or *merge by index/id*? No single right answer — state your rule.
- **Type conflict** — base has dict, override has a string for the same key → override wins (can't merge dict with non-dict).
- **Mutation** — `deepcopy` (or build fresh) so you don't silently change the caller's `base`.
- **`None` in override** — does it delete the key or set it to null? Decide explicitly.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Merge config layers (default→env→CLI) | Chain deep_merge |
| 2 | Merge two JSON API responses | Same |
| 3 | Flatten nested dict (Q1) | Recursion on one dict |
| 4 | Group / pivot list of records | `setdefault` aggregation |
| 5 | Rename / remap keys across a dataset | Transform pass |
| 6 | Diff two JSON objects | Parallel walk, collect differences |
| 7 | Merge dictionaries with summed values | `Counter` + / recurse |
| 8 | Recursively update state trees (Redux-style) | Deep merge |

---

## The ONE trick to remember

```
"BOTH DICTS → RECURSE. ELSE → OVERRIDE WINS.  (update() is SHALLOW!)"
```

The trap is that `{**a, **b}` only merges the top level. A real deep merge walks both dicts in parallel: when both sides hold a dict, recurse; otherwise the override replaces. And `deepcopy` the base so you never mutate the caller.

> Memory hook: "Two dicts, walk together — same subtree? blend it; leaf clash? newer wins."

---
