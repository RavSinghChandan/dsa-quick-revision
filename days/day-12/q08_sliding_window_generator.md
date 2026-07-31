# Day 12 · Q8 · Sliding Window Generator (fixed size K)  ·  [GENERATORS]

---

## What is this question actually asking?

Given a sequence, yield every **contiguous window of size K** as it slides one step at a time.

```
sliding([1,2,3,4,5], 3) → [1,2,3], [2,3,4], [3,4,5]
```

> Real AI use: n-grams for NLP, rolling averages on a time series, windowed features, context windows over tokens. Combines the generator idea with the sliding-window pattern.

---

## Pattern

```
PATTERN: Generator + fixed-size deque (sliding window)
```

Whenever you see: *"every consecutive group of K", "rolling / moving", "n-grams"* → keep a `deque(maxlen=K)`. Push each new item; the deque auto-drops the oldest. Once it's full, `yield` a snapshot each step.

---

## Understand with a diagram

```
K=3, deque(maxlen=3):

1        → [1]           (not full)
2        → [1,2]         (not full)
3        → [1,2,3] FULL  → yield (1,2,3)
4        → [2,3,4]       → yield (2,3,4)   (1 auto-evicted)
5        → [3,4,5]       → yield (3,4,5)

deque with maxlen drops the oldest automatically when you append past K.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — slice for each window
`[seq[i:i+k] for i in range(len(seq)-k+1)]` → O(n·k) work and needs the whole sequence indexable (no streams). Fine for lists, not for streams.

### 🟢 Optimal — deque(maxlen=K) generator — O(n)
`deque(maxlen=K)` gives O(1) append with automatic eviction. Works on any iterable (including streams), yields a window each step once full, holds only K items.

---

## Pseudocode (Optimal)

```
function sliding(iterable, k):
    window = deque(maxlen=k)
    for item in iterable:
        window.append(item)          ← oldest auto-dropped if over k
        if len(window) == k:
            yield tuple(window)       ← emit a snapshot
```

---

## Python Code

```python
from collections import deque
from typing import Iterable, Iterator

def sliding_window(iterable: Iterable, k: int) -> Iterator[tuple]:
    if k < 1:
        raise ValueError("k must be >= 1")
    window = deque(maxlen=k)
    for item in iterable:
        window.append(item)              # auto-evicts oldest past k
        if len(window) == k:
            yield tuple(window)          # snapshot (copy, so caller is safe)


# Test
for w in sliding_window([1,2,3,4,5], 3):
    print(w)
# (1, 2, 3)
# (2, 3, 4)
# (3, 4, 5)

# Rolling average built on top:
def rolling_avg(nums, k):
    for w in sliding_window(nums, k):
        yield sum(w) / k
print(list(rolling_avg([1,2,3,4,5], 3)))   # [2.0, 3.0, 4.0]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| deque generator | O(n) | O(K) window |

Note: `sum(w)` per window makes rolling_avg O(n·k); for a true O(n) rolling sum, add the new item and subtract the one that left.

---

## Edge Cases to remember

- **k larger than the sequence** → yields nothing (window never fills). Decide if that's acceptable.
- **`yield tuple(window)` (a copy)** — if you yield the deque itself, the caller sees it mutate on the next step. Always snapshot.
- **k = 1** → each element as a 1-tuple.
- **True rolling sum** — recomputing `sum` each window is O(k); maintain a running total for O(1) per step.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Maximum sum subarray of size K | Running sum window |
| 2 | Sliding Window Maximum (LC 239) | Monotonic deque |
| 3 | Generate n-grams for NLP | Windows of tokens |
| 4 | Moving average of a data stream (LC 346) | Fixed window + sum |
| 5 | Longest substring w/o repeat (LC 3) | Variable window |
| 6 | Anagram substrings (LC 438) | Window + counts |
| 7 | Rolling std / rolling features | Window aggregate |
| 8 | Batch generator (Q7) | Same generator family |

---

## The ONE trick to remember

```
"deque(maxlen=K): APPEND AUTO-EVICTS. YIELD A tuple() SNAPSHOT WHEN FULL."
```

The `maxlen` deque is the whole trick — it keeps exactly the last K items with O(1) append and automatic eviction of the oldest. Yield a copy (`tuple`) so the emitted window doesn't change under the caller.

> Memory hook: "A queue that forgets its oldest — snapshot it each slide."

---
