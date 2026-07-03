# Q4 — Burst Balloons (LeetCode 312)

## 1. What is this question actually asking?

Given an array of balloons with values, burst all balloons. When you burst balloon i, you earn nums[i-1]*nums[i]*nums[i+1] coins. Missing boundaries are treated as 1. Maximize total coins collected by choosing the optimal order to burst balloons.

Example: nums = [3, 1, 5, 8]
Burst 1: 3*1*5=15; [3,5,8]
Burst 5: 3*5*8=120; [3,8]
Burst 3: 1*3*8=24; [8]
Burst 8: 1*8*1=8; []
Total = 167

## 2. Pattern

Interval DP — reverse thinking: instead of "which balloon to burst first", think "which balloon to burst LAST" within an interval (i, j).

## 3. Understand with a Diagram

```
nums = [3, 1, 5, 8]
Add boundary 1s: arr = [1, 3, 1, 5, 8, 1]  (indices 0..5)

dp[i][j] = max coins by bursting all balloons strictly BETWEEN indices i and j
           (i and j are boundaries, not burst)

When balloon k is the LAST burst in range (i,j):
  coins at k = arr[i] * arr[k] * arr[j]
  (because everything else in (i,k) and (k,j) is already gone)
  
  dp[i][j] = max over k in (i+1..j-1) of:
    dp[i][k] + arr[i]*arr[k]*arr[j] + dp[k][j]
    
  left subproblem   last burst   right subproblem

Base: dp[i][i+1] = 0 (no balloons between adjacent indices)

Fill by interval length l = 2 to n+1:

l=2 (adjacent, no balloons between):
  dp[0][1]=0, dp[1][2]=0, dp[2][3]=0, dp[3][4]=0, dp[4][5]=0

l=3 (one balloon between):
  dp[0][2]: k=1: dp[0][1]+arr[0]*arr[1]*arr[2]+dp[1][2] = 0+1*3*1+0=3
  dp[1][3]: k=2: dp[1][2]+arr[1]*arr[2]*arr[3]+dp[2][3] = 0+3*1*5+0=15
  dp[2][4]: k=3: dp[2][3]+arr[2]*arr[3]*arr[4]+dp[3][4] = 0+1*5*8+0=40
  dp[3][5]: k=4: dp[3][4]+arr[3]*arr[4]*arr[5]+dp[4][5] = 0+5*8*1+0=40

l=4 (two balloons between):
  dp[0][3]: k=1: 0+1*3*5+15=30; k=2: 3+1*1*5+0=8 → max=30
  dp[1][4]: k=2: 15+3*1*8+40=79; k=3: 0+3*5*8+40=160 → max=160
  dp[2][5]: k=3: 40+1*5*1+40=81; k=4: 0+1*8*1+40=48 → max=81

l=5 (three balloons):
  dp[0][4]: k=1: 0+1*3*8+160=184; k=2: 30+1*1*8+40=78; k=3: 30+... 
  Wait: k goes 1..3
    k=1: dp[0][1]+1*3*8+dp[1][4] = 0+24+160=184
    k=2: dp[0][2]+1*1*8+dp[2][4] = 3+8+40=51
    k=3: dp[0][3]+1*5*8+dp[3][4] = 30+40+0=70
  dp[0][4] = 184

l=6:
  dp[0][5]: k=1..4
    k=1: dp[0][1]+1*3*1+dp[1][5] = 0+3+81=84
    k=2: dp[0][2]+1*1*1+dp[2][5] = 3+1+81=85
    k=3: dp[0][3]+1*5*1+dp[3][5] = 30+5+40=75
    k=4: dp[0][4]+1*8*1+dp[4][5] = 184+8+0=192
  dp[0][5] = max(84,85,75,192) = 192? 
  
  Hmm, but expected 167. Let me retrace...
  
Actually let me recount dp[1][4]:
  Balloons strictly between index 1 and 4 in arr=[1,3,1,5,8,1]: indices 2,3 = values 1,5
  k=2: dp[1][2]+arr[1]*arr[2]*arr[4]+dp[2][4] = 0+3*1*8+40=64
  k=3: dp[1][3]+arr[1]*arr[3]*arr[4]+dp[3][4] = 15+3*5*8+0=15+120=135
  dp[1][4] = 135

dp[0][5]:
    k=1: 0 + 1*3*1 + dp[1][5]
    dp[1][5]: balloons 2,3,4 = values 1,5,8
      k=2: dp[1][2]+3*1*1+dp[2][5]=0+3+81=84
      k=3: dp[1][3]+3*5*1+dp[3][5]=15+15+40=70
      k=4: dp[1][4]+3*8*1+dp[4][5]=135+24+0=159
      dp[1][5]=159
    k=1: 0+3+159=162
    k=2: dp[0][2]+1*1*1+dp[2][5]=3+1+81=85
    k=3: dp[0][3]+1*5*1+dp[3][5]=30+5+40=75
    k=4: dp[0][4]+1*8*1+dp[4][5]
    dp[0][4]: balloons 1,2,3 = values 3,1,5
      k=1: 0+1*3*8+135=159
      k=2: dp[0][2]+1*1*8+dp[2][4]=3+8+40=51
      k=3: dp[0][3]+1*5*8+dp[3][4]=30+40+0=70
      dp[0][4]=159
    k=4: 159+8+0=167
  dp[0][5] = max(162,85,75,167) = 167 ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — Permutations
**Idea:** Try all orderings of bursting balloons, compute coins for each.

O(n!) permutations. Infeasible for large n.

### Better — Recursion with memoization
**Idea:** Think of "last balloon to burst" in each interval. Cache dp[i][j].

O(n^3) time (n^2 states, n choices each), O(n^2) space.

### Optimal — Bottom-up Interval DP
**Idea:** Same O(n^3) but avoids recursion stack. Fill table by interval length.

## 5. Pseudocode (Optimal)

```
function maxCoins(nums[]):
    arr = [1] + nums + [1]   // add boundary 1s
    n = len(arr)
    dp = 2D array n x n, initialized to 0
    
    // l = interval length (gap between i and j)
    for l from 2 to n-1:
        for i from 0 to n-l-1:
            j = i + l
            for k from i+1 to j-1:
                coins = arr[i]*arr[k]*arr[j]
                dp[i][j] = max(dp[i][j], dp[i][k] + coins + dp[k][j])
    
    return dp[0][n-1]
```

## 6. Python Code

```python
def maxCoins(nums):
    """
    Burst all balloons to maximize coins.
    Coins for bursting i = nums[i-1] * nums[i] * nums[i+1].
    Missing boundaries = 1.
    """
    # Add boundary 1s
    arr = [1] + nums + [1]
    n = len(arr)
    
    # dp[i][j] = max coins from bursting all balloons strictly between i and j
    dp = [[0] * n for _ in range(n)]
    
    # l = distance between i and j (interval length)
    for l in range(2, n):
        for i in range(n - l):
            j = i + l
            for k in range(i + 1, j):
                # k is the LAST balloon to burst in (i, j)
                coins = arr[i] * arr[k] * arr[j]
                dp[i][j] = max(dp[i][j], dp[i][k] + coins + dp[k][j])
    
    return dp[0][n-1]


# Test 1 — LeetCode example 1
print(maxCoins([3, 1, 5, 8]))   # 167

# Test 2 — LeetCode example 2
print(maxCoins([1, 5]))          # 10

# Test 3 — single balloon
print(maxCoins([5]))             # 5

# Test 4 — two same balloons
print(maxCoins([2, 2]))          # 2*2*1 + 1*2*1 = 6 or 1*2*2 + 1*2*1 = 6 → 6
print(maxCoins([3, 3]))          # 1*3*3+1*3*1=9+3=12 or 1*3*3+1*3*1=same → 12

# Test 5 — already decreasing
print(maxCoins([8, 5, 3, 1]))   # burst in order: 8 last for left boundary effect
```

## 7. Complexity Table

| Approach          | Time    | Space   | Notes                       |
|-------------------|---------|---------|-----------------------------|
| Brute Force       | O(n!)   | O(n)    | All permutations            |
| Memoized DFS      | O(n^3)  | O(n^2)  | Top-down                    |
| Interval DP       | O(n^3)  | O(n^2)  | Bottom-up, preferred        |

## 8. Edge Cases to Remember

- Add boundary 1s: pad both sides with 1. Index goes from 0 to n+1.
- dp[i][j] represents bursting everything STRICTLY between i and j (i and j are boundaries)
- k is the LAST balloon burst (not first) — this is the key insight
- Single balloon: arr = [1, val, 1], dp[0][2] = 1*val*1 = val
- Base: dp[i][i+1] = 0 (adjacent, empty interval, no balloons to burst)

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Matrix Chain Multiplication           | Same interval DP "last operation" trick             | Classic    |
| Strange Printer                       | Interval DP on character merging                    | LC 664     |
| Minimum Cost Merge Stones             | Interval DP on merging groups                       | LC 1000    |
| Remove Boxes                          | DP with extra dimension for extra boxes             | LC 546     |
| Zuma Game                             | Interval DP on color sequences                      | LC 488     |
| Stone Game VII                        | Interval DP on subsets                              | LC 1690    |
| Minimum Cost to Cut a Stick           | Interval DP on cut positions                        | LC 1547    |
| Score Triangulation of Polygon        | Interval DP on triangles                            | LC 1039    |

## 10. The ONE Trick to Remember

**"Think of the LAST balloon to burst, not the first. dp[i][j] = max coins between i and j when k is last: dp[i][k] + arr[i]*arr[k]*arr[j] + dp[k][j]."**

The brilliant insight: if you think about which balloon to burst first, removing it changes neighbor indices and makes the problem recursive and messy. Instead, think about which balloon is burst LAST within interval (i,j). When k is burst last, i and j are already the boundaries (everything else is gone), so coins = arr[i]*arr[k]*arr[j]. This decouples the left and right subproblems cleanly. Add boundary 1s to handle edge effects.
