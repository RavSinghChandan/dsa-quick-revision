# Day 5 — Stack, Queue & Greedy · Q7 · Remove K Digits

---

## What is this question actually asking?

From a numeric string, remove exactly `k` digits to make the **smallest possible number**.

```
"1432219", k=3 → "1219"
"10200",   k=1 → "200"
"10",      k=2 → "0"
```

> A greedy + **monotonic stack** gem. To minimize, remove a digit whenever it's bigger than the one after it (a "descent").

---

## Pattern

```
PATTERN: Monotonic increasing stack (greedy digit removal)
```

Whenever you see: *"remove k to make smallest/largest number/subsequence"* → build a stack; while the top is larger than the incoming digit and you still have removals left, pop. Leftover removals trim from the end.

---

## Understand with a diagram

```
"1432219", k=3   build increasing stack, pop on a descent while k>0

1 → [1]
4 → [1,4]
3 → 4>3 pop(k2) → [1,3]
2 → 3>2 pop(k1) → [1,2]
2 → [1,2,2]
1 → 2>1 pop(k0) → [1,2,1]... wait k=0 now, keep → [1,2,2,1]
9 → [1,2,2,1,9]
k=0 done. Trim leading zeros. → "1219"
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — try all removals — exponential
Choose which k digits to remove: C(n,k). Infeasible.

### 🟢 Optimal — greedy monotonic stack — O(n)
Removing a digit that's bigger than its successor always lowers the number the most (at the most significant position). Stack does this in one pass.

---

## Pseudocode (Optimal)

```
function remove_k_digits(num, k):
    stack = []
    for d in num:
        while k > 0 and stack and stack[-1] > d:
            stack.pop(); k -= 1        ← remove a bigger leading digit
        stack.push(d)
    stack = stack[:len(stack)-k]       ← leftover removals: trim from end
    result = "".join(stack).lstrip("0")
    return result or "0"
```

---

## Python Code

```python
def remove_k_digits(num: str, k: int) -> str:
    stack = []
    for d in num:
        while k > 0 and stack and stack[-1] > d:
            stack.pop()                # digit bigger than next → remove
            k -= 1
        stack.append(d)

    stack = stack[:len(stack) - k]     # if k left, remove from the end
    result = "".join(stack).lstrip("0")
    return result if result else "0"


# Test
print(remove_k_digits("1432219", 3))   # "1219"
print(remove_k_digits("10200", 1))     # "200"
print(remove_k_digits("10", 2))        # "0"
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Greedy stack | O(n) | O(n) |

---

## Edge Cases to remember

- **Leftover k** — if the number is already non-decreasing, no pops happen; trim the last `k` digits.
- **Leading zeros** — strip them after building (`"10200",k=1 → "0200" → "200"`).
- **Empty result** → return `"0"` (removed everything meaningful).
- **k == len(num)** → `"0"`.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Remove Duplicate Letters (LC 316) | Monotonic + seen set |
| 2 | Smallest Subsequence Distinct (LC 1081) | Same |
| 3 | Create Maximum Number (LC 321) | Monotonic pick |
| 4 | Next Greater Element (Q1) | Monotonic stack |
| 5 | 132 Pattern (LC 456) | Monotonic |
| 6 | Largest number after k swaps | Greedy |
| 7 | Monotone Increasing Digits (LC 738) | Greedy digits |
| 8 | Remove nodes to make list smaller | Monotonic |

---

## The ONE trick to remember

```
"POP A DIGIT WHEN IT'S BIGGER THAN THE NEXT (WHILE k>0); TRIM THE REST FROM THE END."
```

The smallest number wants small digits up front — so whenever a digit exceeds the one after it, removing it helps most. A monotonic increasing stack does this greedily; any unused removals come off the tail.

> Memory hook: "Kill the tall digit standing before a shorter one."

---
