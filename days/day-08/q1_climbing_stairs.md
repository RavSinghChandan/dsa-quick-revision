# Q1 — Climbing Stairs (LeetCode 70)

## 1. What is this question actually asking?

You're climbing a staircase with n steps. Each time you can either climb 1 or 2 steps. How many distinct ways can you reach the top? For n=3, you can go: (1,1,1), (1,2), (2,1) — 3 ways. This is the simplest DP problem and is secretly the Fibonacci sequence.

## 2. Pattern

Dynamic Programming (1D) — dp[i] = ways to reach step i = dp[i-1] + dp[i-2]

## 3. Understand with a Diagram

```
n=5

To reach step i, you either:
  - came from step i-1 (took 1 step), or
  - came from step i-2 (took 2 steps)
So: ways[i] = ways[i-1] + ways[i-2]

Base cases:
  ways[1] = 1  (only one way: one step of 1)
  ways[2] = 2  (1+1 or 2)

Building up:
step:  1    2    3    4    5
ways:  1    2    3    5    8
          ↗   ↗   ↗   ↗
      each = prev two summed

This is Fibonacci: F(n+1) where F(1)=1, F(2)=1
Or equivalently: climbStairs(n) = fib(n+1)

Diagram (decision tree for n=4):
                  start
                /       \
            take 1     take 2
            /    \      /    \
         (1)1   (1)2  (2)1  (2)2
         /  \   / \    \     \
      (2)1 (2)2 (3)1  (3)2  (4)done
       \    \   done  done
      (3)1 (3)2
      done  done
Paths: (1,1,1,1),(1,1,2),(1,2,1),(2,1,1),(2,2) = 5 ways
```

## 4. Brute Force → Better → Optimal

### Brute Force — Recursion
**Idea:** Recursively count ways. From step 0, you can go to 1 or 2.

```
function ways(n):
    if n == 0 or n == 1: return 1
    return ways(n-1) + ways(n-2)
```

Why slow: O(2^n) — exponential, same subproblems recomputed.

### Better — Memoization (Top-Down DP)
**Idea:** Cache results of ways(n) to avoid recomputation.

```
memo = {}
function ways(n):
    if n <= 1: return 1
    if n in memo: return memo[n]
    memo[n] = ways(n-1) + ways(n-2)
    return memo[n]
```

O(n) time, O(n) space.

### Optimal — Bottom-Up DP with O(1) space
**Idea:** Compute iteratively. Only need previous two values.

```
if n == 1: return 1
prev2 = 1  // ways(1)
prev1 = 2  // ways(2)

for i from 3 to n:
    curr = prev1 + prev2
    prev2 = prev1
    prev1 = curr

return prev1
```

O(n) time, O(1) space.

**Trace for n=5:**
```
prev2=1, prev1=2
i=3: curr=3, prev2=2, prev1=3
i=4: curr=5, prev2=3, prev1=5
i=5: curr=8, prev2=5, prev1=8
return 8
```

## 5. Pseudocode (Optimal)

```
function climbStairs(n):
    if n <= 2: return n
    
    prev2 = 1
    prev1 = 2
    
    for i from 3 to n:
        curr = prev1 + prev2
        prev2 = prev1
        prev1 = curr
    
    return prev1
```

## 6. Python Code

```python
def climbStairs(n):
    if n <= 2:
        return n
    
    prev2, prev1 = 1, 2
    
    for _ in range(3, n + 1):
        curr = prev1 + prev2
        prev2, prev1 = prev1, curr
    
    return prev1


# Test all values 1-10
for i in range(1, 11):
    print(f"n={i}: {climbStairs(i)}")
# n=1: 1
# n=2: 2
# n=3: 3
# n=4: 5
# n=5: 8
# n=6: 13
# n=7: 21
# n=8: 34
# n=9: 55
# n=10: 89

# These are Fibonacci numbers: 1,2,3,5,8,13,21,34,55,89
```

## 7. Complexity Table

| Approach          | Time  | Space | Notes                         |
|-------------------|-------|-------|-------------------------------|
| Recursion         | O(2^n)| O(n)  | Exponential — too slow        |
| Memoization       | O(n)  | O(n)  | Top-down DP                   |
| Bottom-Up DP      | O(n)  | O(n)  | Full array                    |
| Two Variables     | O(n)  | O(1)  | Optimal — just prev2, prev1   |

## 8. Edge Cases to Remember

- n = 1 — return 1 (only 1 way: take one step)
- n = 2 — return 2 (1+1 or 2)
- n = 0 — return 1 (or 0 depending on problem definition; "1 way: take 0 steps")
- Large n — Python handles big integers natively; in other languages check overflow
- Generalization: k steps allowed — dp[i] = sum of dp[i-1] to dp[i-k]

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Fibonacci Number                      | Same recurrence, simpler base case                  | LC 509     |
| Min Cost Climbing Stairs              | Same DP + choose minimum cost                       | LC 746     |
| House Robber                          | Same 1D DP: dp[i] = max(dp[i-2]+num[i], dp[i-1])  | LC 198     |
| Decode Ways                           | Fibonacci-like DP with conditions                   | LC 91      |
| N-th Tribonacci Number                | Extend to 3 previous values                         | LC 1137    |
| Jump Game                             | Can you reach the end? DP or greedy                 | LC 55      |
| Jump Game II                          | Min jumps to reach end                              | LC 45      |
| Count Ways to Reach nth Stair (k steps)| Generalized: dp[i] = sum dp[i-1..i-k]            | —          |

## 10. The ONE Trick to Remember

**"Ways to reach step i = ways from i-1 + ways from i-2 — it's Fibonacci in disguise."**

You can only arrive at step i from step i-1 (one step) or step i-2 (two steps). So the number of ways to reach i equals the sum of ways to reach those two previous steps. With base cases ways[1]=1 and ways[2]=2, this is exactly the Fibonacci recurrence shifted by one. The optimal implementation uses just two variables, updating them like a sliding window.
