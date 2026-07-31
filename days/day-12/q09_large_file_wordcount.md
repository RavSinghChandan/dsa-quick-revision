# Day 12 · Q9 · Word Count on a Huge File (streaming)  ·  [FILES]

---

## What is this question actually asking?

Count word frequencies in a file that is **too big to fit in memory** (many GB). Return the top words.

```
file (10 GB) → {"the": 5_000_000, "data": 42_000, ...} → top 10
```

> Tests the #1 file-handling reflex: **stream line by line**, never `read()` the whole thing. Combines files + dict counting + Top-K heap.

---

## Pattern

```
PATTERN: Stream file line-by-line + Counter + Top-K
```

Whenever you see: *"process a file larger than RAM"* → iterate the file object directly (`for line in f`) — it's a lazy generator that reads one line at a time. Aggregate as you go.

---

## Understand with a diagram

```
open file
  │
  └─ for line in f:          ← ONE line in memory at a time (lazy)
        for word in line.split():
            counts[word] += 1
  │
  └─ counts.most_common(10)  ← Top-K without sorting all

Memory used ≈ size of the vocabulary, NOT the file size.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — read it all
```
text = open(path).read()          ← loads 10 GB into RAM → crash
words = text.split()
```
Also leaks the file handle (no `with`).

### 🟢 Optimal — stream + Counter — O(total words), O(vocab) memory
`with open(path) as f:` then `for line in f:` reads lazily. Update a `Counter`. Memory scales with the number of **distinct** words, not the file. `most_common(k)` gives Top-K via an internal heap.

---

## Pseudocode (Optimal)

```
function word_count(path, k):
    counts = Counter()
    open file with context manager:
        for line in file:                 ← lazy, one line at a time
            for word in normalize(line).split():
                counts[word] += 1
    return counts.most_common(k)
```

---

## Python Code

```python
import re
from collections import Counter

def word_count_top_k(path: str, k: int = 10) -> list[tuple[str, int]]:
    counts = Counter()
    with open(path, encoding="utf-8") as f:      # context mgr → auto-closes
        for line in f:                           # lazy: one line at a time
            for word in re.findall(r"[a-z0-9']+", line.lower()):
                counts[word] += 1
    return counts.most_common(k)                 # Top-K via internal heap


# Demonstrate on a temp file
import tempfile, os
text = "the cat the dog\nthe cat sat\n"
with tempfile.NamedTemporaryFile("w", suffix=".txt", delete=False) as tf:
    tf.write(text); path = tf.name
print(word_count_top_k(path, 2))   # [('the', 3), ('cat', 2)]
os.remove(path)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Stream + Counter | O(total words) | O(distinct words) — NOT file size |

---

## Edge Cases to remember

- **NEVER `f.read()` on a huge file** — stream with `for line in f`. This is the whole point.
- **Always use `with open(...)`** — guarantees the file closes even on error.
- **Encoding** — pass `encoding="utf-8"`; real files have unicode. Handle `errors="ignore"` if corrupt.
- **Normalization** — lowercase + strip punctuation, or `"The"`/`"the."` split into different counts.
- **Even the vocab too big?** → shard/spill to disk or use a probabilistic count (Count-Min Sketch) — mention it.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Log parser (Day 11 Q3) | Stream + count |
| 2 | Top K frequent words in file (LC 692-ish) | Counter + heap |
| 3 | Count lines / grep a big file | Stream scan |
| 4 | External sort of a huge file | Chunk + k-way merge |
| 5 | Deduplicate lines (Q10) | Stream + set |
| 6 | Parse a giant CSV without pandas | Stream rows |
| 7 | Find unique visitors from access log | Stream + set |
| 8 | Stream JSONL and aggregate | Line generator |

---

## The ONE trick to remember

```
"with open(...) as f:  for line in f  — STREAM, never read() it all."
```

The file object is already a lazy line generator: iterating it uses O(1) memory per line. Count into a `Counter`, take `most_common(k)`. Memory tracks the vocabulary, not the file — so 10 GB is fine.

> Memory hook: "Sip the file line by line; don't swallow it whole."

---
