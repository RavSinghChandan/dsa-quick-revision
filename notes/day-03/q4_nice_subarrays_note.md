# ⚡ Quick Note · Day 3 — SW & Backtracking · Q4 · Nice Subarrays
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Count subarrays with exactly k odd numbers."

## Pattern
`Exactly K = atMost(K) − atMost(K−1)  (or prefix odd-count + map)`

## Visual in your head
```
odd→1, even→0. count subarrays with sum=k.
atMost(k): window, res += right-left+1 while odds≤k
exactly(k)=atMost(k)-atMost(k-1)
```

## The trick (say it out loud)
> "Exactly k is hard; at-most k is an easy window. Subtract. Or prefix odd-count with a map."

## Code skeleton
```python
def atmost(k):
    if k<0: return 0
    l=odd=res=0
    for r in range(n):
        odd+=nums[r]&1
        while odd>k: odd-=nums[l]&1; l+=1
        res+=r-l+1
    return res
return atmost(k)-atmost(k-1)
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
K Different Integers (992) · Binary Subarrays Sum (930) · Subarray Sum K (560) · Subarrays XOR K (Q1)
