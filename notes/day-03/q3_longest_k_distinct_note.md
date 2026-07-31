# ⚡ Quick Note · Day 3 — SW & Backtracking · Q3 · Longest ≤K Distinct
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Longest substring with at most K distinct characters."

## Pattern
`Variable sliding window + count map (shrink while distinct>k)`

## Visual in your head
```
add s[right] to count
while len(count)>k: remove s[left]; del key if 0; left++
best=max(best, right-left+1)
```

## The trick (say it out loud)
> "Grow right; while distinct > k, shrink left and delete zero-count keys. Exactly K = atMost(K)−atMost(K−1)."

## Code skeleton
```python
count[ch]+=1
while len(count)>k:
    count[s[left]]-=1
    if count[s[left]]==0: del count[s[left]]
    left+=1
best=max(best,right-left+1)
```

## Complexity
- Time O(n) · Space O(k)

## This trick solves more
No-repeat (Q2) · Fruit Baskets (904) · K Different Integers (992) · Max Ones III (1004) · Min Window (76)
