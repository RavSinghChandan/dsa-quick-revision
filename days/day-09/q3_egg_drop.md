# Q3 — Egg Drop Problem (LeetCode 887)

## 1. What is this question actually asking?

You have `k` eggs and `n` floors. An egg breaks if dropped from floor F or above (critical floor). Find the minimum number of attempts needed in the worst case to determine the critical floor F with certainty, regardless of where F is. You want to minimize the worst-case number of trials.

For example: 2 eggs, 10 floors → minimum 4 trials in worst case.

## 2. Pattern

Binary search on answer + DP — or DP with binary search optimization.
Key insight: dp[t][k] = maximum floors we can check with t trials and k eggs.

## 3. Understand with a Diagram

```
Approach 1: dp[i][j] = min trials for i floors, j eggs
Classic DP (O(kn^2)):
  dp[1][j] = 1 (one floor, 1 trial)
  dp[i][1] = i (one egg, must try linearly)
  dp[i][j] = min over x in 1..i of:
    1 + max(dp[x-1][j-1],    // egg breaks — check below
            dp[i-x][j])      // egg survives — check above

Approach 2: dp[t][k] = max floors checkable with t trials and k eggs
  dp[t][k] = dp[t-1][k-1] + 1 + dp[t-1][k]
            = (egg breaks: check dp[t-1][k-1] floors below)
            + (current floor)
            + (egg survives: check dp[t-1][k] floors above)

  Find minimum t such that dp[t][k] >= n

n=10, k=2:
t=1: dp[1][1]=1, dp[1][2]=1
t=2: dp[2][1]=2, dp[2][2]=dp[1][1]+1+dp[1][2]=1+1+1=3
t=3: dp[3][1]=3, dp[3][2]=dp[2][1]+1+dp[2][2]=2+1+3=6
t=4: dp[4][1]=4, dp[4][2]=dp[3][1]+1+dp[3][2]=3+1+6=10 ✓

Answer = 4 (minimum t where dp[t][2] >= 10)

Strategy visualization for 2 eggs, 10 floors:
Drop from floor 4:
  Break → 1 egg left, check floors 1-3 linearly (3 more trials max)
  Survive → drop from floor 7
    Break → check 5-6 (2 more trials max)
    Survive → drop from floor 9
      Break → check floor 8 (1 more trial)
      Survive → drop from floor 10
        Break/Survive → done
Max trials = 1 + 3 = 4 ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — Classic DP O(kn^2)
**Idea:** dp[i][j] = min trials for i floors, j eggs. For each floor x as drop point, worst case = 1 + max(breaks, survives).

```
dp = 2D array, dp[i][1] = i, dp[1][j] = 1
for j from 2 to k:
    for i from 2 to n:
        dp[i][j] = inf
        for x from 1 to i:
            cost = 1 + max(dp[x-1][j-1], dp[i-x][j])
            dp[i][j] = min(dp[i][j], cost)
```

O(k * n^2) time, O(k * n) space.

### Better — DP with binary search O(kn log n)
**Idea:** In the inner loop, dp[x-1][j-1] increases with x, dp[i-x][j] decreases with x. Their max is minimized at their intersection. Binary search for optimal x.

O(k * n * log n) time.

### Optimal — O(k log n): dp[t][k] approach
**Idea:** Instead of "min trials for n floors", ask "max floors checkable with t trials, k eggs".

```
dp[t][k] = dp[t-1][k-1] + 1 + dp[t-1][k]
Find minimum t where dp[t][k] >= n
```

O(k * log n) time total (outer: O(log n) values of t, inner: O(k) to compute each row).

## 5. Pseudocode (Optimal: dp[t][k])

```
function superEggDrop(k, n):
    // dp[t] = array of max floors checkable with t trials for each egg count 1..k
    
    t = 0
    dp = [0] * (k + 1)
    
    while dp[k] < n:
        t += 1
        // Update from right to left to avoid using updated values
        new_dp = [0] * (k + 1)
        for j from 1 to k:
            new_dp[j] = dp[j-1] + 1 + dp[j]
        dp = new_dp
    
    return t
```

## 6. Python Code

```python
def superEggDrop(k, n):
    """
    k = number of eggs, n = number of floors
    Returns minimum number of trials in worst case.
    O(k * log n) approach using dp[t][k].
    """
    # dp[j] = max floors we can check with current t trials and j eggs
    dp = [0] * (k + 1)
    t = 0
    
    while dp[k] < n:
        t += 1
        new_dp = [0] * (k + 1)
        for j in range(1, k + 1):
            # dp[j-1] floors below (egg breaks, j-1 eggs, t-1 trials)
            # 1 current floor
            # dp[j]   floors above (egg survives, j eggs, t-1 trials)
            new_dp[j] = dp[j-1] + 1 + dp[j]
        dp = new_dp
    
    return t


def superEggDrop_classic(k, n):
    """
    Classic O(kn^2) DP for understanding.
    dp[i][j] = min trials for i floors and j eggs.
    """
    dp = [[0] * (k + 1) for _ in range(n + 1)]
    
    # Base cases
    for j in range(1, k + 1):
        dp[1][j] = 1   # 1 floor always needs 1 trial
        dp[0][j] = 0   # 0 floors needs 0 trials
    
    for i in range(1, n + 1):
        dp[i][1] = i   # 1 egg: must try every floor linearly
    
    for j in range(2, k + 1):
        for i in range(2, n + 1):
            dp[i][j] = float('inf')
            for x in range(1, i + 1):
                breaks = dp[x-1][j-1]     # egg breaks, check below
                survives = dp[i-x][j]     # egg survives, check above
                worst = 1 + max(breaks, survives)
                dp[i][j] = min(dp[i][j], worst)
    
    return dp[n][k]


# Test 1 — LeetCode example 1
print(superEggDrop(1, 2))    # 2 (linear search)

# Test 2 — LeetCode example 2
print(superEggDrop(2, 6))    # 3

# Test 3 — LeetCode example 3
print(superEggDrop(3, 14))   # 4

# Test 4 — 2 eggs, 10 floors
print(superEggDrop(2, 10))   # 4

# Test 5 — many eggs
print(superEggDrop(10, 100)) # 7 (log2(100) ≈ 7)

# Verify classic gives same answers
print(superEggDrop_classic(2, 6))   # 3
print(superEggDrop_classic(3, 14))  # 4
```

## 7. Complexity Table

| Approach              | Time         | Space    | Notes                       |
|-----------------------|--------------|----------|-----------------------------|
| Classic DP            | O(k * n^2)  | O(k * n) | Simple to understand        |
| DP + Binary Search    | O(k n log n)| O(k * n) | Moderate                    |
| dp[t][k] reverse      | O(k * log n)| O(k)     | Optimal                     |

## 8. Edge Cases to Remember

- 1 egg — must check every floor linearly → answer = n
- Many eggs (k >= log2(n)) — binary search → answer = ceil(log2(n+1))
- 1 floor — always 1 trial
- dp[t][k] approach: update from right to left (or use new array) to avoid using updated values
- The recurrence dp[t][k] = dp[t-1][k-1] + 1 + dp[t-1][k] means: with one trial, the floor we drop from divides the search space

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Egg Drop Problem (original)           | This exact problem                                  | LC 887     |
| Burst Balloons                        | Interval DP, minimize/maximize with choices         | LC 312     |
| Strange Printer                       | DP with choices to minimize operations              | LC 664     |
| Jump Game IV                          | Minimize jumps — BFS/DP hybrid                     | LC 1345    |
| Super Ugly Number                     | Heap-based DP for minimum operations                | LC 313     |
| Minimum Cost to Hire K Workers        | Optimization DP                                     | LC 857     |
| Minimum Number of Refueling Stops     | Greedy/DP for minimum stops                         | LC 871     |
| Frog Jump                             | DP with state space                                 | LC 403     |

## 10. The ONE Trick to Remember

**"Flip the question: dp[t][k] = max floors checkable with t trials, k eggs = dp[t-1][k-1] + 1 + dp[t-1][k]. Find min t where dp[t][k] >= n."**

Instead of asking "minimum trials for n floors", ask "maximum floors I can guarantee checking with t trials and k eggs". If I have t trials and k eggs and I drop from floor x: egg breaks (t-1 trials, k-1 eggs, check dp[t-1][k-1] floors below), egg survives (t-1 trials, k eggs, check dp[t-1][k] floors above). Total = dp[t-1][k-1] + 1 + dp[t-1][k]. Increment t until dp[t][k] >= n.
