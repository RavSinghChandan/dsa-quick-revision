# ⚡ Quick Note · Day 9 — DP · Q2 · House Robber
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Max money, no two adjacent houses."

## Pattern
`1-D take/skip DP — dp[i]=max(dp[i-1], dp[i-2]+nums[i])`

## Visual in your head
```
skip → dp[i-1]
rob  → dp[i-2] + nums[i]
roll prev1, prev2
```

## The trick (say it out loud)
> "Skip (keep dp[i-1]) or rob (dp[i-2]+nums[i]). Two rolling vars, O(1)."

## Code skeleton
```python
for x in nums:
    cur=max(prev1, prev2+x)
    prev2,prev1=prev1,cur
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
Robber II circular (213: two runs) · Robber III tree (337) · Delete and Earn (740) · Paint House (256)
