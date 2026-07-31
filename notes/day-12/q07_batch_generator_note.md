# ⚡ Quick Note · Day 12 · Q7 · Batch Generator [GENERATORS]
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Yield an iterable in chunks of N, lazily (huge/infinite safe)."

## Pattern
`Generator (yield) — lazy, one batch in memory`

## Visual in your head
```
buffer fills to N → yield buffer, reset
end → yield leftover partial buffer
only ONE batch held at a time
```

## The trick (say it out loud)
> "Fill a buffer, yield when full, reset. Don't forget to yield the leftover at the end."

## Code skeleton
```python
buf=[]
for x in it:
    buf.append(x)
    if len(buf)==n: yield buf; buf=[]
if buf: yield buf
```

## Complexity
- Time O(n) lazy · Space O(batch size)

## This trick solves more
Read huge file line-by-line · Paginate API · Infinite Fibonacci · Stream JSONL · Sliding window · Mini-batch training
(Python 3.12+: itertools.batched)
