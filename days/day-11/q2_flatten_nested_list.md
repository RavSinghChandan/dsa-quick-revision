# Day 11 · Q2 · Flatten a Nested List (arbitrary depth)

---

## What is this question actually asking?

You have a list that contains lists, which contain more lists, to any depth.
Return **one flat list** with all the numbers in order, no nesting.

```
Input:  [1, [2, [3, 4], 5], [6, [7, [8]]]]
Output: [1, 2, 3, 4, 5, 6, 7, 8]
```

> Real AI use: batched data, ragged token lists, nested JSON arrays from a scraper — you constantly need "just give me every leaf value in order."

---

## Pattern

```
PATTERN: Recursion (DFS)  —  OR  —  Iterative with an explicit stack
```

Same family as flatten-dict, but there are **no keys** — just values that may themselves be lists.
Rule: *"an element is either a value (keep it) or a list (dive in)."*

---

## Understand with a diagram

```
[1, [2, [3, 4], 5], [6, [7, [8]]]]
 |    |   |    |  |    |   |   |
 1    2  3 4   5  6    7   8
      └─ dive ─┘        └ dive ┘

Walk left→right. If element is a list → open it and walk inside FIRST,
then continue. That "inside first" is depth-first.
```

---

## Brute Force → Better → Optimal

### 🔴 Naïve — fixed levels

```
for x in lst:
    for y in x:          ← assumes exactly 2 levels
        result.append(y)
```

**Why bad?** Crashes on `[1, [2, [3]]]` — nesting is deeper than the loops. Also crashes on plain `1` (int not iterable).

---

### 🟢 Optimal A — Recursion — O(n)

**Core insight:** For each element: if it's a list → **recurse** and extend with the result; else → append the value.

```
flatten([1, [2, [3, 4], 5]])
  1        → append 1
  [2,[3,4],5] is list → recurse
       2      → append 2
       [3,4]  → recurse → 3, 4
       5      → append 5
```

### 🟢 Optimal B — Iterative with a stack (no recursion limit)

Push the whole list on a stack. Pop; if it's a list, push its items back; else collect it. Reverse at the end (or use a deque). Good when nesting could be 10,000 deep (recursion would overflow).

---

## Pseudocode (Recursive)

```
function flatten(lst):
    result = []
    for element in lst:
        if element is a list:
            result.extend( flatten(element) )   ← dive in
        else:
            result.append(element)              ← plain value
    return result
```

---

## Python Code

```python
def flatten_list(lst: list) -> list:
    result = []
    for element in lst:
        if isinstance(element, list):
            result.extend(flatten_list(element))   # recurse
        else:
            result.append(element)
    return result


# Iterative version (safe for very deep nesting)
def flatten_iter(lst: list) -> list:
    stack = [lst]
    out = []
    while stack:
        item = stack.pop()
        if isinstance(item, list):
            stack.extend(reversed(item))   # keep left-to-right order
        else:
            out.append(item)
    return out


# Test
data = [1, [2, [3, 4], 5], [6, [7, [8]]]]
print(flatten_list(data))   # [1, 2, 3, 4, 5, 6, 7, 8]
print(flatten_iter(data))   # [1, 2, 3, 4, 5, 6, 7, 8]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Recursive | O(n) — every element touched once | O(depth) stack |
| Iterative | O(n) | O(n) explicit stack |

---

## Edge Cases to remember

- **Empty list** `[]` and nested empties `[[], [[]]]` → return `[]`.
- **Strings are iterable but should stay whole** — use `isinstance(x, list)` (NOT "is it iterable"), else `"ab"` explodes into `'a','b'`.
- **Tuples / other containers** — decide if `(1,2)` should flatten too; default: only lists.
- **Very deep nesting** (10k+) → recursion hits Python's limit → use the iterative stack version.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Flatten Nested List Iterator (LeetCode 341) | Same, as an iterator class |
| 2 | Nested List Weight Sum (LeetCode 339) | DFS carrying depth |
| 3 | Flatten a Multilevel Doubly Linked List (LC 430) | DFS flatten |
| 4 | Flatten Binary Tree to Linked List (LC 114) | DFS |
| 5 | Flatten nested dict (Q1) | Recursion, with keys |
| 6 | Ragged token batches → 1D | Preprocessing |
| 7 | numpy `.ravel()` / `.flatten()` | This IS the idea |
| 8 | Directory walk → file list | Recursion on tree |

---

## The ONE trick to remember

```
"IS IT A LIST? DIVE IN (recurse). ELSE, KEEP IT."
```

The only decision per element is `isinstance(x, list)`. That single check drives the whole recursion. For unbounded depth, swap recursion for an explicit stack — same logic, no overflow.

> Memory hook: "Open every box until you find the item — collect items, dive into boxes."

---
