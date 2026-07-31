# ⚡ Quick Note · Day 3 — SW & Backtracking · Q2 · Longest Substring No Repeat
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Longest substring with all distinct characters."

## Pattern
`Variable sliding window + last-seen index map`

## Visual in your head
```
expand right; last[ch]>=left? → left=last[ch]+1
last[ch]=right
best=max(best, right-left+1)
```

## The trick (say it out loud)
> "Expand right; on a repeat inside the window, jump left past the previous occurrence."

## Code skeleton
```python
for r,ch in enumerate(s):
    if ch in last and last[ch]>=left: left=last[ch]+1
    last[ch]=r; best=max(best,r-left+1)
```

## Complexity
- Time O(n) · Space O(alphabet)

## This trick solves more
≤K distinct (Q3) · Min Window Substring (76) · Char Replacement (424) · Fruit Baskets (904) · Max Ones III (1004)
