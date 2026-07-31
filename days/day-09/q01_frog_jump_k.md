# Day 9 — Dynamic Programming · Q1 · Frog Jump with K Distances

---

## What is this question actually asking?

A frog on stair 0 wants to reach stair `n-1`. From stair `i` it can jump to any of the next `K` stairs. Jumping from `i` to `j` costs `|height[i] − height[j]|`. Find the **minimum total cost**.

```
height = [10,30,40,50,20], K = 3 → 30
(0→1→4: |10-30| + |30-20| = 20+10 = 30)
```

> A 1-D DP where each state depends on up to K previous states. The generalization of the classic 1/2-jump frog problem.

---

## Pattern

```
PATTERN: 1-D DP — dp[i] = min over the last K reachable states
```

Whenever you see: *"reach the end minimizing cost, jumps up to K"* → `dp[i]` = min cost to reach stair `i`. Compute it from the up-to-K stairs before it: `dp[i] = min(dp[i-j] + |h[i]-h[i-j]|)` for `j` in `1..K`.

---

## Understand with a diagram

```
dp[0] = 0
dp[i] = min over j=1..K, i-j>=0:  dp[i-j] + |h[i] - h[i-j]|

heights [10,30,40,50,20], K=3
dp[1]=20, dp[2]=min(dp[0]+30, dp[1]+10)=30, ...
dp[4]=min(dp[1]+|20-30|, dp[2]+|20-40|, dp[3]+|20-50|)=min(30,50,80)=30
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — recursion — O(K^n)
Try every jump sequence. Exponential.

### 🟡 Memoized recursion — O(n·K)
Cache `dp[i]`.

### 🟢 Optimal — bottom-up DP — O(n·K), O(n) space
Fill `dp` left to right, each from its K predecessors. (Space can be O(K) with a rolling window.)

---

## Pseudocode (Optimal)

```
function frog_jump_k(height, K):
    n = len(height)
    dp = [inf]*n; dp[0] = 0
    for i in 1..n-1:
        for j in 1..K:
            if i-j >= 0:
                dp[i] = min(dp[i], dp[i-j] + abs(height[i]-height[i-j]))
    return dp[n-1]
```

---

## Python Code

```python
def frog_jump_k(height: list[int], K: int) -> int:
    n = len(height)
    INF = float('inf')
    dp = [INF] * n
    dp[0] = 0
    for i in range(1, n):
        for j in range(1, K + 1):
            if i - j >= 0:
                cost = dp[i - j] + abs(height[i] - height[i - j])
                dp[i] = min(dp[i], cost)
    return dp[n - 1]


# Test
print(frog_jump_k([10,30,40,50,20], 3))   # 30
print(frog_jump_k([10,20,10], 2))         # 0   (0->2, |10-10|=0)
print(frog_jump_k([30,10,60,10,60,50], 2))# 40
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Bottom-up | O(n·K) | O(n) (O(K) rolling) |

---

## Edge Cases to remember

- **`i - j >= 0` bound** — don't jump before the first stair.
- **K = 1** → must step one at a time; K = 2 is the classic frog.
- **Single stair** → cost 0.
- **Rolling window** → only the last K dp-values are needed → O(K) space.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Climbing Stairs (LC 70) | dp[i]=dp[i-1]+dp[i-2] |
| 2 | Min Cost Climbing Stairs (LC 746) | dp with cost |
| 3 | Jump Game II (LC 45) | Min jumps (greedy/BFS) |
| 4 | House Robber (Q2) | Pick/skip DP |
| 5 | Frog jump 1 or 2 steps | K=2 |
| 6 | Decode ways (LC 91) | 1-D DP |
| 7 | Minimum path cost | DP transitions |
| 8 | Coin change min coins | DP over choices |

---

## The ONE trick to remember

```
"dp[i] = min over the last K stairs of (dp[i-j] + |h[i]-h[i-j]|)."
```

Minimum cost to reach stair i comes from the cheapest of its up-to-K predecessors plus the jump cost. Fill left to right. Reduce to O(K) space with a rolling window if asked.

> Memory hook: "Each stair's cost = best of the K stairs it could have leapt from."

---
