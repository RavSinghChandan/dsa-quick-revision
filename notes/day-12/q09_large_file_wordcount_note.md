# ⚡ Quick Note · Day 12 · Q9 · Huge File Word Count [FILES]
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Count words in a file bigger than RAM; return top K."

## Pattern
`Stream file line-by-line + Counter + Top-K`

## Visual in your head
```
with open(path) as f:
  for line in f:              ← lazy, O(1)/line
    for w in line.split(): counts[w]+=1
counts.most_common(k)
```

## The trick (say it out loud)
> "with open(...) as f: for line in f. STREAM — never read() it all. Counter + most_common."

## Code skeleton
```python
with open(path) as f:
    for line in f:
        for w in line.lower().split(): counts[w]+=1
return counts.most_common(k)
```

## Complexity
- Time O(total words) · Space O(distinct words), not file size

## This trick solves more
Log parser · Top-K words · grep big file · External sort · Dedup lines · Giant CSV · Unique visitors
