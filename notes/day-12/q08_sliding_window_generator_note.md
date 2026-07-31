# ⚡ Quick Note · Day 12 · Q8 · Sliding Window Generator [GENERATORS]
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Yield every contiguous window of size K as it slides."

## Pattern
`Generator + deque(maxlen=K)`

## Visual in your head
```
deque(maxlen=3): append auto-drops oldest
full? → yield tuple(window)
[1,2,3]→[2,3,4]→[3,4,5]
```

## The trick (say it out loud)
> "deque(maxlen=K) auto-evicts the oldest. Yield a tuple() snapshot when it's full."

## Code skeleton
```python
win=deque(maxlen=k)
for x in it:
    win.append(x)
    if len(win)==k: yield tuple(win)
```

## Complexity
- Time O(n) · Space O(K)

## This trick solves more
Max sum subarray K · Sliding Window Max (239) · n-grams · Moving average (346) · Rolling features · Batch gen
