# ⚡ Quick Note · Day 9 — DP · Q10 · Matrix Chain Multiplication
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Min scalar multiplications to multiply a matrix chain (best parenthesization)."

## Pattern
`Interval DP — dp[i][j] = min over split k`

## Visual in your head
```
dp[i][j]=min_{k=i..j-1} dp[i][k]+dp[k+1][j]+dims[i-1]*dims[k]*dims[j]
fill by increasing interval length; dp[i][i]=0
```

## The trick (say it out loud)
> "Interval DP. Try every split k: left cost + right cost + merge cost. Fill by chain length."

## Code skeleton
```python
for length in range(2,n+1):
  for i in ...:
    j=i+length-1
    for k in range(i,j):
      dp[i][j]=min(dp[i][j], dp[i][k]+dp[k+1][j]+dims[i-1]*dims[k]*dims[j])
```

## Complexity
- Time O(n³) · Space O(n²)

## This trick solves more
Burst Balloons (312) · Palindrome Partition II (132) · Cut Stick (1547) · Guess Number II (375) · Optimal BST
