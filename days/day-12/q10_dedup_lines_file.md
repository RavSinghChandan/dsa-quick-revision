# Day 12 · Q10 · Deduplicate File Lines (preserve first-seen order)  ·  [FILES]

---

## What is this question actually asking?

Read a file and write out its lines with **duplicates removed**, keeping the **order of first appearance**.

```
in:  apple / banana / apple / cherry / banana
out: apple / banana / cherry
```

> Real use: cleaning a dataset, deduping URLs to crawl, unique log entries. Tests the **"seen" set** trick and (the twist) preserving order — which `set()` alone does NOT do.

---

## Pattern

```
PATTERN: Streaming pass + "seen" set for O(1) membership
```

Whenever you see: *"remove duplicates / keep uniques / has this appeared"* → keep a `set` of what you've seen; emit an item only the first time. Stream the file so memory holds the unique set, not the whole file twice.

---

## Understand with a diagram

```
seen = {}
apple  → not seen → emit, seen={apple}
banana → not seen → emit, seen={apple,banana}
apple  → seen     → skip
cherry → not seen → emit
banana → seen     → skip

output order = first-seen order ✅  (a bare set() would LOSE this order)
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — `list(set(lines))`
`set()` removes dups but **destroys order** (and loads all lines). Wrong when order matters.

### 🔴 Also bad — check membership in a list
`if line not in output_list` is O(n) per check → O(n²) overall.

### 🟢 Optimal — stream + set — O(n), order preserved
Iterate lines; a `set` gives O(1) "have I seen this?". Emit on first sight, add to the set. Order is preserved because you emit in read order.

---

## Pseudocode (Optimal)

```
function dedup(in_path, out_path):
    seen = empty set
    open in_path for read, out_path for write:
        for line in input:                ← stream
            if line not in seen:
                write line to output
                add line to seen
```

---

## Python Code

```python
def dedup_lines(in_path: str, out_path: str) -> int:
    seen = set()
    written = 0
    with open(in_path, encoding="utf-8") as fin, \
         open(out_path, "w", encoding="utf-8") as fout:
        for line in fin:                     # stream, one line at a time
            key = line.rstrip("\n")
            if key not in seen:              # O(1) membership
                seen.add(key)
                fout.write(line)
                written += 1
    return written


# In-memory version (same idea, dict since 3.7 keeps order too)
def dedup(items):
    return list(dict.fromkeys(items))        # unique + order preserved


# Test
print(dedup(["apple","banana","apple","cherry","banana"]))
# ['apple', 'banana', 'cherry']
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Stream + set | O(n) | O(unique lines) |
| `in list` check | O(n²) | O(unique) |

---

## Edge Cases to remember

- **`set()` alone loses order** — use a `set` for membership but emit in read order, or `dict.fromkeys()` which preserves insertion order (Python 3.7+).
- **Trailing newline** — normalize (`rstrip("\n")`) so `"a\n"` and `"a"` aren't seen as different.
- **Case / whitespace** — decide if `"Apple"` == `"apple"`; normalize the key if so.
- **Huge unique set** — if uniques don't fit in RAM, sort the file externally then drop adjacent dups, or use a Bloom filter (approx).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Remove Duplicates from list preserving order | `dict.fromkeys` |
| 2 | Contains Duplicate (LC 217) | "seen" set |
| 3 | First unique element | Count / seen |
| 4 | Unique visitors from a log | Stream + set |
| 5 | Dedup URLs before crawling | Seen set |
| 6 | Intersection / difference of two files | Sets |
| 7 | Longest substring w/o repeating (LC 3) | Sliding window + set |
| 8 | Deduplicate near-identical rows | Signature + set |

---

## The ONE trick to remember

```
"SEEN SET FOR O(1) MEMBERSHIP; EMIT IN READ ORDER (or dict.fromkeys)."
```

A `set` answers "have I seen this?" in O(1). Emit each line the first time only, in the order you read them — that preserves first-seen order, which a plain `set()` cannot. `dict.fromkeys(items)` does both in one line.

> Memory hook: "Set remembers; you print the first time you meet it."

---
