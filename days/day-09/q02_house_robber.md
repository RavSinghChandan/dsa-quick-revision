# Day 9 — Dynamic Programming · Q2 · House Robber

---

## What is this question actually asking?

Rob houses in a row for maximum money, but you **can't rob two adjacent** houses.

```
[2,7,9,3,1] → 12   (rob 2 + 9 + 1)
[2,1,1,2]   → 4    (rob 2 + 2)
```

> The archetypal "pick or skip" DP. At each house you either take it (plus best up to two back) or skip it (best up to one back).

---

## Pattern

```
PATTERN: 1-D "take or skip" DP  →  dp[i] = max(skip, take)
```

Whenever you see: *"max sum with no two adjacent / non-adjacent selection"* → `dp[i] = max(dp[i-1], dp[i-2] + nums[i])`. Only the last two values matter → O(1) space.

---

## Understand with a diagram

```
dp[i] = max( dp[i-1],            ← skip house i
             dp[i-2] + nums[i] ) ← rob house i (can't use i-1)

[2,7,9,3,1]
dp: 2, 7, max(7,2+9)=11, max(11,7+3)=11, max(11,11+1)=12  → 12

Rolling: keep prev (dp[i-1]) and prev2 (dp[i-2]).
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — try all subsets — O(2^n)
Enumerate valid selections.

### 🟢 Optimal — DP, O(1) space
Track two rolling values; each house is take-vs-skip.

---

## Pseudocode (Optimal)

```
function rob(nums):
    prev2 = 0    ← dp[i-2]
    prev1 = 0    ← dp[i-1]
    for x in nums:
        cur = max(prev1, prev2 + x)
        prev2 = prev1
        prev1 = cur
    return prev1
```

---

## Python Code

```python
def rob(nums: list[int]) -> int:
    prev2 = prev1 = 0
    for x in nums:
        cur = max(prev1, prev2 + x)   # skip vs rob this house
        prev2, prev1 = prev1, cur
    return prev1


# House Robber II (circular row): can't rob both first and last house.
def rob_circular(nums: list[int]) -> int:
    if len(nums) == 1:
        return nums[0]
    return max(rob(nums[1:]), rob(nums[:-1]))   # exclude first OR last


# Test
print(rob([2,7,9,3,1]))          # 12
print(rob([2,1,1,2]))            # 4
print(rob_circular([2,3,2]))     # 3   (can't take both 2s)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| DP | O(n) | O(1) |

---

## Edge Cases to remember

- **Empty / single house** → 0 / that value; `prev1=prev2=0` init handles empty.
- **House Robber II (circular)** → run twice: once excluding the first house, once the last, take the max.
- **House Robber III (tree)** → post-order returning (rob, notRob) pairs.
- **All negative** — if values can be negative, decide whether robbing nothing (0) is allowed.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | House Robber II (LC 213) | Circular: two runs |
| 2 | House Robber III (LC 337) | Tree DP |
| 3 | Delete and Earn (LC 740) | Transform to robber |
| 4 | Max sum non-adjacent | Same DP |
| 5 | Paint House (LC 256) | State DP |
| 6 | Climbing stairs (LC 70) | 1-D DP |
| 7 | Frog jump (Q1) | 1-D DP |
| 8 | Stickers/coins subset | Take/skip |

---

## The ONE trick to remember

```
"dp[i] = max(SKIP dp[i-1], ROB dp[i-2] + nums[i]). ROLL TWO VALUES."
```

Either skip the current house (keep the best up to the previous) or rob it (add it to the best up to two houses back). Only two rolling variables are needed → O(1) space.

> Memory hook: "Rob it and skip the neighbor, or skip it and keep the best so far."

---
