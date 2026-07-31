# Day 12 · Q11 · Safe Nested JSON Access by Dotted Path  ·  [JSON]

---

## What is this question actually asking?

Given a nested JSON/dict and a **dotted path** like `"user.address.city"`, safely fetch the value — returning a default instead of crashing if any key along the way is missing.

```
data = {"user": {"address": {"city": "Pune"}}}
get_path(data, "user.address.city")  → "Pune"
get_path(data, "user.phone.mobile")  → None   (no crash)
```

> Real AI use: pulling fields out of messy LLM/API JSON where keys are often absent. Tests defensive traversal — the opposite of `data["a"]["b"]["c"]` which explodes on the first missing key.

---

## Pattern

```
PATTERN: Split path + iterative safe descent with a default
```

Whenever you see: *"reach deep into JSON that might be incomplete"* → split the path on `.`, walk key by key, and **bail to a default the moment** the current level isn't a dict or the key is missing.

---

## Understand with a diagram

```
path "user.address.city" → ["user","address","city"]

cur = data
 "user"    → cur = data["user"]         (dict? yes)
 "address" → cur = cur["address"]       (dict? yes)
 "city"    → cur = cur["city"] = "Pune"
return "Pune"

If at any step cur is not a dict or key missing → return default. No KeyError.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — chained indexing
```
data["user"]["phone"]["mobile"]   ← KeyError / TypeError if any link missing
```
Or a pyramid of `if "user" in data and "phone" in data["user"] ...` — unreadable.

### 🟢 Optimal — iterate the split path with a guard — O(depth)
Split once, loop the keys, check `isinstance(cur, dict)` and membership each step; return the default on the first miss. Clean, no exceptions for control flow.

---

## Pseudocode (Optimal)

```
function get_path(data, path, default=None):
    cur = data
    for key in path.split("."):
        if cur is a dict and key in cur:
            cur = cur[key]
        else:
            return default          ← missing link → default
    return cur
```

---

## Python Code

```python
def get_path(data: dict, path: str, default=None):
    cur = data
    for key in path.split("."):
        if isinstance(cur, dict) and key in cur:
            cur = cur[key]
        else:
            return default              # missing key or not a dict
    return cur


# Test
data = {"user": {"address": {"city": "Pune"}}}
print(get_path(data, "user.address.city"))     # Pune
print(get_path(data, "user.phone.mobile"))     # None
print(get_path(data, "user.phone.mobile", "-"))# -

# Try/except is also valid but hides which key failed:
def get_path_try(data, path, default=None):
    cur = data
    try:
        for key in path.split("."):
            cur = cur[key]
        return cur
    except (KeyError, TypeError):
        return default
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Iterative descent | O(depth of path) | O(1) |

---

## Edge Cases to remember

- **Missing intermediate key** → return default, don't raise. That's the whole point.
- **Non-dict along the path** (`cur` is a list or string) → `isinstance(cur, dict)` guard catches it.
- **List indices in the path** (`items.0.name`) → extend to handle digit segments on lists if the format needs it; state your scope.
- **Key literally contains a dot** → dotted paths become ambiguous; note the limitation.
- **`default` vs. genuine `None` value** → if the stored value can be `None`, you can't tell "missing" from "None" by return value alone.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Flatten nested dict (Day 11 Q1) | Same key-walking |
| 2 | Set a value by dotted path (deep set) | Walk + create missing dicts |
| 3 | Config resolver with defaults | Dotted lookup + fallback |
| 4 | Extract fields from LLM JSON output | Defensive access |
| 5 | `pydash.get` / `glom` behavior | This is the algorithm |
| 6 | JSONPath-lite queries | Path traversal |
| 7 | Safe attribute chains | Same guard idea |
| 8 | Deep-merge JSON (Day 11 Q6) | Recursion on dicts |

---

## The ONE trick to remember

```
"SPLIT ON '.', WALK KEY BY KEY, BAIL TO DEFAULT ON THE FIRST MISS."
```

Never chain `["a"]["b"]["c"]` on untrusted JSON — one missing key throws. Split the path, descend with an `isinstance(cur, dict) and key in cur` guard, and return the default the moment a link is missing.

> Memory hook: "Walk the path; step off the moment the ground disappears."

---
