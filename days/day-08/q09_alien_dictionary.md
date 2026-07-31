# Day 8 — Strings & Graphs · Q9 · Alien Dictionary

---

## What is this question actually asking?

Given a list of words **sorted** in an alien language's alphabet, deduce a valid **letter ordering** of that alphabet.

```
["wrt","wrf","er","ett","rftt"] → "wertf"
["z","x","z"] → ""   (invalid — contradiction)
```

> A beautiful applied **topological sort**: adjacent sorted words reveal one ordering constraint (the first differing character). Then topo-sort the letters.

---

## Pattern

```
PATTERN: Build a precedence graph from adjacent words → Topological Sort
```

Whenever you see: *"derive an order from sorted examples"* → compare each adjacent pair; the first position where they differ gives `c1 → c2` (c1 before c2). Topo-sort all letters; a cycle → no valid order.

---

## Understand with a diagram

```
Compare adjacent words, first differing char = an edge:
  "wrt" vs "wrf" → t → f
  "wrf" vs "er"  → w → e
  "er"  vs "ett" → r → t
  "ett" vs "rftt"→ e → r

graph edges: t→f, w→e, r→t, e→r  → topo order → w e r t f
Prefix rule: if word1 is longer and a prefix of word2 comes AFTER → invalid.
```

---

## Brute Force → Better → Optimal

### 🟢 Optimal — build edges + Kahn's topo — O(total chars)
Extract constraints from adjacent pairs, then topological sort. Detect cycles (contradiction) and the invalid-prefix case.

---

## Pseudocode (Optimal)

```
function alien_order(words):
    nodes = all unique letters
    for each adjacent (w1, w2):
        if w1 is longer and w2 is a prefix of w1: return ""   ← invalid
        find first differing char c1, c2 → add edge c1 → c2
    topo_sort(nodes)
    return order if it covers all letters else ""   ← cycle
```

---

## Python Code

```python
from collections import deque, defaultdict

def alien_order(words: list[str]) -> str:
    adj = defaultdict(set)
    indeg = {c: 0 for w in words for c in w}    # every letter is a node

    for w1, w2 in zip(words, words[1:]):
        # invalid: longer word is a prefix but comes first
        if len(w1) > len(w2) and w1.startswith(w2):
            return ""
        for a, b in zip(w1, w2):
            if a != b:                          # first difference → edge
                if b not in adj[a]:
                    adj[a].add(b)
                    indeg[b] += 1
                break                           # only the first diff matters

    q = deque([c for c in indeg if indeg[c] == 0])
    order = []
    while q:
        c = q.popleft()
        order.append(c)
        for nxt in adj[c]:
            indeg[nxt] -= 1
            if indeg[nxt] == 0:
                q.append(nxt)

    return "".join(order) if len(order) == len(indeg) else ""   # cycle → ""


# Test
print(alien_order(["wrt","wrf","er","ett","rftt"]))   # "wertf"
print(alien_order(["z","x"]))                          # "zx"
print(alien_order(["z","x","z"]))                      # ""  (cycle)
print(alien_order(["abc","ab"]))                       # ""  (invalid prefix)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Build + topo | O(total characters) | O(unique letters + edges) |

---

## Edge Cases to remember

- **Invalid prefix** — `["abc","ab"]` is illegal (a longer word can't sort before its own prefix) → return "".
- **Only the FIRST differing char** gives a constraint; `break` after it.
- **Every letter is a node** — even ones with no constraints must appear in the output.
- **Cycle** → contradiction → "".
- **Multiple valid orders** — any topological order is acceptable.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Course Schedule II (LC 210) | Topo order |
| 2 | Topological Sort (Q7) | Engine |
| 3 | Sequence Reconstruction (LC 444) | Unique topo |
| 4 | Verifying an alien dictionary (LC 953) | Order check |
| 5 | Build order from constraints | Topo |
| 6 | Reconstruct itinerary (LC 332) | Euler/topo-ish |
| 7 | Minimum height trees (LC 310) | Peel leaves |
| 8 | Task ordering | Topo |

---

## The ONE trick to remember

```
"ADJACENT WORDS' FIRST DIFF = AN EDGE; THEN TOPO-SORT. CYCLE/BAD-PREFIX ⇒ \"\"."
```

Each consecutive pair of sorted words yields exactly one ordering edge (their first differing letters). Topologically sort the letters. Return "" if there's a cycle or an invalid longer-then-prefix pair.

> Memory hook: "Where two sorted words first disagree, the earlier letter wins — then order them all."

---
