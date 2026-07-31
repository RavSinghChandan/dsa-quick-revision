# Day 12 · Q7 · Batch an Iterable with a Generator  ·  [GENERATORS]

---

## What is this question actually asking?

Write a function that takes any iterable (even an endless stream or a huge file) and yields it in **chunks of size N**, without loading it all into memory.

```
batched([1,2,3,4,5,6,7], 3) → [1,2,3], [4,5,6], [7]
```

> Real AI use: feeding data to a model in mini-batches, calling an embeddings/LLM API in batches of 100, processing a 10 GB file line by line. The whole point is **lazy** — one batch in memory at a time.

---

## Pattern

```
PATTERN: Generator (yield) — lazy evaluation over a stream
```

Whenever you see: *"process something too big to fit in memory"* or *"in chunks / batches / windows"* → write a **generator** with `yield`. It produces values on demand and holds only the current piece.

---

## Understand with a diagram

```
stream: 1 2 3 4 5 6 7   (could be infinite)

buffer fills to size 3 → YIELD [1,2,3], clear
                       → YIELD [4,5,6], clear
end of stream, buffer=[7] → YIELD [7]

At any moment only ONE batch (≤3 items) is in memory.
```

`yield` = "hand this out, pause here, resume when asked for the next."

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — build all batches in a list
`return [chunks...]` materializes every batch up front → O(n) memory, and can't handle infinite streams.

### 🟢 Optimal — Generator, one batch at a time — O(batch) memory
Accumulate items into a buffer; when it hits size N, `yield` it and reset. `yield` makes the function lazy: nothing is computed until the caller iterates, and only the current batch is held.

---

## Pseudocode (Optimal)

```
function batched(iterable, n):
    buffer = []
    for item in iterable:
        buffer.append(item)
        if len(buffer) == n:
            yield buffer        ← hand out a full batch, pause
            buffer = []
    if buffer:                  ← leftover partial batch
        yield buffer
```

---

## Python Code

```python
from typing import Iterable, Iterator

def batched(iterable: Iterable, n: int) -> Iterator[list]:
    if n < 1:
        raise ValueError("n must be >= 1")
    buffer = []
    for item in iterable:
        buffer.append(item)
        if len(buffer) == n:
            yield buffer            # lazy: only this batch is in memory
            buffer = []
    if buffer:                      # last, possibly-partial batch
        yield buffer


# Test
for batch in batched([1,2,3,4,5,6,7], 3):
    print(batch)
# [1, 2, 3]
# [4, 5, 6]
# [7]

# Works on a lazy/infinite source too — only pulls what you consume:
import itertools
gen = batched(itertools.count(), 4)   # 0,1,2,... forever
print(next(gen))   # [0, 1, 2, 3]
print(next(gen))   # [4, 5, 6, 7]

# Python 3.12+ has this built in: itertools.batched(iterable, n)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Generator | O(n) total, lazy | O(batch size) — NOT O(n) |

---

## Edge Cases to remember

- **Last partial batch** — don't forget to `yield` the leftover buffer after the loop.
- **`n < 1`** → raise, or you loop forever / yield nothing.
- **Infinite source** → works precisely *because* it's a generator; a list version would hang.
- **Consumed once** — a generator is exhausted after one pass; re-create it to reuse.
- **Empty iterable** → yields nothing (correct).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Read a huge file line-by-line | `for line in f` is a generator |
| 2 | Paginate API calls | Yield pages |
| 3 | Fibonacci / infinite sequence | `yield` in a loop |
| 4 | Stream JSONL records | Yield parsed objects |
| 5 | Sliding window (Q8) | Yield windows |
| 6 | Lazy pipeline (map→filter→batch) | Chained generators |
| 7 | Mini-batch training loop | Yield (X, y) batches |
| 8 | itertools.islice / groupby | Lazy iteration |

---

## The ONE trick to remember

```
"yield ONE PIECE AT A TIME; FLUSH THE LEFTOVER BUFFER AT THE END."
```

A generator holds only the current batch, so it scales to infinite streams and giant files. Fill a buffer, `yield` when full, reset — and remember the final `if buffer: yield buffer` for the partial last chunk.

> Memory hook: "Don't cook the whole meal — serve one plate at a time."

---
