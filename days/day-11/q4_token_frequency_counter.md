# Day 11 · Q4 · Token Frequency Counter + Top-K Tokens

---

## What is this question actually asking?

Given text (or a list of documents), count how often each **token** (word) appears, then return the **K most frequent** ones.

```
Input:  "the cat sat on the mat the cat"
Output freq: {the:3, cat:2, sat:1, on:1, mat:1}
Top-2:  [("the", 3), ("cat", 2)]
```

> This is the foundation of NLP: vocabulary building, stop-word detection, bag-of-words, and the term-frequency half of TF-IDF (Q5). Every LLM tokenizer starts here.

---

## Pattern

```
PATTERN: Hash Map count  +  Heap (or Counter.most_common) for Top-K
```

Two classic sub-problems glued together:
1. **Count** → hash map (dict / `Counter`) → O(n).
2. **Top-K** → a **min-heap of size K** → O(n log K), better than sorting everything O(n log n) when K ≪ n.

---

## Understand with a diagram

```
Tokens:  the cat sat on the mat the cat

Step 1 — count (one pass):
   the ●●●   (3)
   cat ●●    (2)
   sat ●     (1)   on ● (1)   mat ● (1)

Step 2 — Top-K=2 via min-heap of size 2:
   push (3,the) push (2,cat) → heap[(2,cat),(3,the)]
   push (1,sat): 1 < heap min(2)? yes → skip
   → heap holds the 2 biggest
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — count by scanning per word

```
for word in unique_words:
    count = 0
    for w in all_words: if w == word: count += 1   ← O(n) per word
```

**Why bad?** O(n × unique) ≈ O(n²).

---

### 🟡 Better — Count with hash map, then SORT all — O(n log n)

Count in one pass (O(n)), then `sorted(counts, reverse=True)[:k]`. Fine, but you sort the **entire** vocabulary even to get top-3.

---

### 🟢 Optimal — Hash map + Min-Heap of size K — O(n log K)

**Core insight:** You don't need the whole vocab sorted — just the **K biggest**. Keep a **min-heap of size K**: push each `(count, token)`; if it grows past K, pop the smallest. Whatever survives is the top K.

```
Counter → O(n)
heapq.nlargest(k, counts, key=count)  → O(n log k)
```

When K is tiny and vocab is millions, this is a big win.

---

## Pseudocode (Optimal)

```
function top_k_tokens(text, k):
    tokens = normalize(text).split()          ← lowercase, strip punctuation
    counts = hash map: token → frequency      ← one pass, O(n)
    heap = min-heap
    for token, freq in counts:
        push (freq, token) onto heap
        if size(heap) > k: pop smallest
    return heap sorted descending
```

---

## Python Code

```python
import re
import heapq
from collections import Counter

def tokenize(text: str) -> list[str]:
    # lowercase + keep word characters only
    return re.findall(r"[a-z0-9']+", text.lower())

def token_frequencies(text: str) -> Counter:
    return Counter(tokenize(text))            # O(n) one-pass count

def top_k_tokens(text: str, k: int) -> list[tuple[str, int]]:
    counts = token_frequencies(text)
    # nlargest uses a size-k min-heap internally → O(n log k)
    return heapq.nlargest(k, counts.items(), key=lambda kv: kv[1])


# Test
text = "The cat sat on the mat. The CAT!"
print(token_frequencies(text))
# Counter({'the': 3, 'cat': 2, 'sat': 1, 'on': 1, 'mat': 1})
print(top_k_tokens(text, 2))
# [('the', 3), ('cat', 2)]

# Counter also has this built in:
print(Counter(tokenize(text)).most_common(2))  # [('the', 3), ('cat', 2)]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Count | O(n) | O(unique tokens) |
| Top-K via heap | O(n log K) | O(K) |
| Top-K via full sort | O(u log u) | O(u) |

---

## Edge Cases to remember

- **Normalization** — lowercase + strip punctuation, else `"The"`, `"the"`, `"the."` count as 3 different tokens.
- **Stop words** — interviewer may want `the/a/is` removed before counting.
- **Ties at the K boundary** — which of two count-equal tokens wins? Define a tie-break (alphabetical) for determinism.
- **K > number of unique tokens** → just return all.
- **Streaming huge corpus** → update one `Counter` across files; don't hold all text.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Top K Frequent Elements (LeetCode 347) | Exact same |
| 2 | Top K Frequent Words (LeetCode 692) | + tie-break alphabetical |
| 3 | Word Frequency (LeetCode 192, bash) | Count + sort |
| 4 | Build a vocabulary for BoW / TF-IDF (Q5) | The count IS term frequency |
| 5 | Most common n-grams | Count tuples instead of words |
| 6 | Sort Characters By Frequency (LC 451) | Char counts |
| 7 | K closest / K largest anything | Size-K heap |
| 8 | Stop-word list from a corpus | Top-K then cut |

---

## The ONE trick to remember

```
"COUNT WITH A HASH MAP, TOP-K WITH A SIZE-K MIN-HEAP"
```

Counting is O(n) with `Counter`. For Top-K, don't sort everything — a **min-heap of size K** (or `heapq.nlargest` / `Counter.most_common`) gives O(n log K). Remember: **min-heap** for top-K (you pop the smallest to keep the biggest).

> Memory hook: "Count everything, but only keep K on the podium."

---
