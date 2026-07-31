# ⚡ Quick Note · Day 3 — SW & Backtracking · Q1 · Subarrays XOR K
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Count contiguous subarrays whose XOR = k."

## Pattern
`Prefix XOR + hash map (NOT sliding window)`

## Visual in your head
```
px ^= x
want = px ^ k
count += freq[want]
freq[px] += 1   ; seed freq[0]=1
```

## The trick (say it out loud)
> "Subarray XOR = k ⟺ earlier prefix = px ^ k. Count those. Seed {0:1}. XOR isn't monotonic — no window."

## Code skeleton
```python
freq={0:1}; px=0; cnt=0
for x in nums:
    px^=x; cnt+=freq[px^k]; freq[px]+=1
```

## Complexity
- Time O(n) · Space O(n)

## This trick solves more
Subarray Sum = K (560) · Sum divisible by K (974) · Contiguous 0/1 (525) · Nice Subarrays · Binary subarray sum (930)
