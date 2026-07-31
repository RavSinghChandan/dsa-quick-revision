# ⚡ Quick Note · Day 11 · Q5 · TF-IDF from scratch
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Score each word per doc: frequent HERE but rare across ALL docs = high."

---

## Pattern
`Two dict passes: TF (per doc) × IDF (across corpus)`

---

## The formula (memorize)
```
TF(w,d)  = count(w in d) / total words in d
IDF(w)   = log( N / docs_containing(w) )
TF-IDF   = TF × IDF
word in ALL docs → IDF = log(1) = 0 → killed
```

---

## The trick (say it out loud)
> "Compute DF once with set(tokens per doc). IDF = log(N/DF). Multiply by TF. Common word → 0."

---

## Code skeleton (3 lines to recall)
```python
tf = {w: c/total for w,c in Counter(toks).items()}
for w in set(toks): df[w] += 1        # once per doc
idf = {w: math.log(N/df[w]) for w in df}   # tfidf = tf[w]*idf[w]
```

---

## Complexity
- Time O(total tokens) · Space O(vocab × docs)

---

## This trick solves more questions
Bag-of-Words · Doc cosine similarity · Search ranking · Keyword extraction · Spam features · sklearn TfidfVectorizer
