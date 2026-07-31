# ⚡ Quick Note · Day 11 · Q4 · Token Frequency Counter
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Count word frequencies, return the K most frequent."

---

## Pattern
`Hash map count + size-K min-heap (Top-K)`

---

## Visual in your head
```
tokens → Counter → {the:3, cat:2, ...}   (O(n))
Top-K → min-heap of size K → keep biggest (O(n log K))
```

---

## The trick (say it out loud)
> "Count with Counter. Top-K with a MIN-heap of size K — pop the smallest to keep the biggest."

---

## Code skeleton (3 lines to recall)
```python
counts = Counter(re.findall(r"[a-z0-9']+", text.lower()))
top = heapq.nlargest(k, counts.items(), key=lambda kv: kv[1])
# or: counts.most_common(k)
```

---

## Complexity
- Count O(n) · Top-K O(n log K) · Space O(unique)

---

## This trick solves more questions
Top-K Frequent Elements (347) · Top-K Words (692) · n-grams · Sort chars by freq (451) · TF for TF-IDF · Stop-word list
