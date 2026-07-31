# Day 11 · Q5 · Implement TF-IDF from scratch (no sklearn)

---

## What is this question actually asking?

Given a small set of documents, compute a **TF-IDF score** for each word in each document.
TF-IDF answers: *"How important is this word to THIS document, compared to all documents?"*
Common words (the, is) get low scores; rare-but-present words get high scores.

```
Docs:
  d1: "the cat sat"
  d2: "the dog sat"
  d3: "the cat ran"

"cat" in d1 → appears in d1 & d3 (2 of 3 docs) → medium score
"the" in d1 → appears in ALL docs → score ≈ 0 (useless word)
```

> This is THE classic ML preprocessing question. It tests: can you turn a plain-English formula into working code, and do you understand WHY common words get killed.

---

## Pattern

```
PATTERN: Two hash-map passes  →  TF (per doc)  ×  IDF (across corpus)
```

Not a graph/DP trick — it's **"translate a formula into loops over dicts."** The skill under test is decomposing a formula into countable pieces.

---

## The formula (in plain English)

```
TF(word, doc)  = (times word appears in doc) / (total words in doc)
               → "how frequent here"

IDF(word)      = log( total_docs / (docs that contain word) )
               → "how RARE across the corpus"  (rare = high, common = low)

TF-IDF         = TF × IDF
               → high only when frequent HERE but rare ELSEWHERE
```

Why the `log`? It softens the effect — a word in 1/1000 docs isn't 1000× more important than one in 1/2, just meaningfully more.
Why does "the" die? It's in every doc → `total_docs / all_docs = 1` → `log(1) = 0` → TF-IDF = 0. 

---

## Understand with a diagram

```
             d1        d2        d3
"cat"     appears    -         appears    → in 2 of 3 docs → IDF = log(3/2) ≈ 0.405
"the"     appears  appears   appears      → in 3 of 3 docs → IDF = log(3/3) = 0  ✗ killed
"sat"     appears  appears     -          → in 2 of 3      → IDF ≈ 0.405

TF("cat", d1) = 1/3   →  TF-IDF = (1/3) × 0.405 ≈ 0.135   (idf=log(3/2)≈0.405)
TF("the", d1) = 1/3   →  TF-IDF = (1/3) × 0     = 0        (common word ⇒ 0)
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — recompute IDF for every word every time

For each word in each doc, re-scan ALL docs to count how many contain it → O(V × D × words) — very wasteful, IDF recomputed repeatedly.

### 🟢 Optimal — precompute Document Frequency once — O(total tokens)

**Core insight:** Split into clean stages:
1. **TF** per doc — one Counter per document.
2. **DF** (document frequency) — for each unique word, how many docs contain it — compute **once** for the whole corpus.
3. **IDF** — `log(N / DF[word])`, computed once per word.
4. **TF-IDF** = multiply. One pass each. No repeated scanning.

---

## Pseudocode (Optimal)

```
function tfidf(docs):
    N = number of docs
    tf = []                          ← list of {word: count/total} per doc
    df = hash map word → #docs containing it

    for each doc:
        counts = Counter(tokens)
        total  = len(tokens)
        tf.append({w: c/total for w,c in counts})
        for w in set(tokens): df[w] += 1     ← set() so each doc counts a word once

    idf = { w: log(N / df[w]) for w in df }

    result = []
    for doc_tf in tf:
        result.append({ w: doc_tf[w] * idf[w] for w in doc_tf })
    return result
```

---

## Python Code

```python
import math
from collections import Counter

def tokenize(text: str) -> list[str]:
    return text.lower().split()

def compute_tfidf(docs: list[str]) -> list[dict]:
    N = len(docs)
    tokenized = [tokenize(d) for d in docs]

    # 1) TF per doc
    tf = []
    for toks in tokenized:
        counts = Counter(toks)
        total = len(toks)
        tf.append({w: c / total for w, c in counts.items()})

    # 2) DF: how many docs contain each word (count word once per doc → set)
    df = Counter()
    for toks in tokenized:
        for w in set(toks):
            df[w] += 1

    # 3) IDF
    idf = {w: math.log(N / df[w]) for w in df}

    # 4) TF-IDF = TF × IDF
    return [{w: round(tf_w * idf[w], 4) for w, tf_w in doc_tf.items()}
            for doc_tf in tf]


# Test
docs = ["the cat sat", "the dog sat", "the cat ran"]
for i, scores in enumerate(compute_tfidf(docs)):
    print(f"d{i+1}:", scores)
# d1: {'the': 0.0, 'cat': 0.1352, 'sat': 0.1352}
# d2: {'the': 0.0, 'dog': 0.3662, 'sat': 0.1352}
# d3: {'the': 0.0, 'cat': 0.1352, 'ran': 0.3662}
# note: 'the' = 0 everywhere (in all docs); 'dog'/'ran' (in 1 doc) score highest
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Optimal | O(total tokens) | O(vocabulary × docs) |

---

## Edge Cases to remember

- **Word in ALL docs** → IDF = log(N/N) = 0 → TF-IDF = 0. This is the point (kills stop words).
- **Division by zero** — a word always has DF ≥ 1 if it exists, so `N/DF` is safe; but guard `total_words > 0` for empty docs.
- **Smoothing** — sklearn uses `log((1+N)/(1+DF)) + 1` to avoid zero IDF; mention you know the smoothed variant.
- **Log base** — natural log vs log10 only scales results; be consistent.
- **Normalization** — sklearn L2-normalizes each doc vector; note it if asked to match sklearn exactly.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Bag-of-Words vectorizer | Just the TF / count step |
| 2 | Cosine similarity between docs | Build TF-IDF vectors, then dot product |
| 3 | Simple search-engine ranking | Score docs by query-term TF-IDF |
| 4 | Keyword extraction from a doc | Top TF-IDF words per doc |
| 5 | Spam filter features | TF-IDF features → classifier |
| 6 | Token frequency counter (Q4) | TF is exactly this |
| 7 | n-gram TF-IDF | Same, tokens = n-grams |
| 8 | Reproduce sklearn TfidfVectorizer | Add smoothing + L2 norm |

---

## The ONE trick to remember

```
"TF = frequent HERE.  IDF = rare EVERYWHERE.  Multiply."
```

Compute **DF once** for the whole corpus (using `set(tokens)` so each doc counts a word once), turn it into IDF with a `log`, then multiply by per-doc TF. A word in every document has IDF = 0 → that's the built-in stop-word killer.

> Memory hook: "Important word = says a lot about THIS doc, little about the others."

---
