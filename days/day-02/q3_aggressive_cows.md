# Day 2 — Binary Search · Q3 · Aggressive Cows

---

## What is this question actually asking?

Place `k` cows in stalls at positions `stalls[]` so that the **minimum distance between any two cows is as LARGE as possible**. Return that largest minimum distance.

```
stalls = [1,2,4,8,9], k = 3 → 3   (place at 1, 4, 8/9 → min gap 3)
```

> "Maximize the minimum" — the mirror of Koko. Binary-search the answer (the distance), check feasibility greedily.

---

## Pattern

```
PATTERN: Binary Search on the Answer (maximize a min, greedy feasibility)
```

Whenever you see: *"maximize the minimum gap/distance"* → binary-search the candidate distance `d`; `feasible(d)` = "can we greedily place all k cows at least `d` apart?" Monotonic: if `d` works, any smaller `d` works.

---

## Understand with a diagram

```
sort stalls: [1,2,4,8,9]
distance range: [1 .. max-min = 8]

feasible(d): greedily place a cow at the first stall, then the next stall
             that is >= last placed + d. Count placed. placed >= k ?

d=3: place 1 → next >=4 → 4 → next >=7 → 8 → placed 3 >= k=3 ✓
d=4: place 1 → next >=5 → 8 → next >=12 → none → placed 2 < 3 ✗
→ largest feasible d = 3
```

Bigger `d` → fewer cows fit → monotonic.

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — try every distance — O(n · max)
For each candidate distance, greedily check. Linear over distances.

### 🟢 Optimal — binary search on distance — O(n log(max))
Sort stalls. Binary-search `d` in `[1, max-min]`; each `feasible(d)` is an O(n) greedy placement. Keep the largest feasible `d`.

---

## Pseudocode (Optimal)

```
function max_min_dist(stalls, k):
    sort stalls
    lo, hi = 1, stalls[-1] - stalls[0]
    ans = 0
    while lo <= hi:
        d = (lo+hi)//2
        if can_place(stalls, k, d):
            ans = d; lo = d+1        ← try a bigger gap
        else:
            hi = d-1
    return ans

function can_place(stalls, k, d):
    count = 1; last = stalls[0]
    for s in stalls[1:]:
        if s - last >= d:
            count++; last = s
    return count >= k
```

---

## Python Code

```python
def aggressive_cows(stalls: list[int], k: int) -> int:
    stalls.sort()

    def can_place(d: int) -> bool:
        count = 1
        last = stalls[0]
        for s in stalls[1:]:
            if s - last >= d:      # far enough → place a cow
                count += 1
                last = s
                if count == k:
                    return True
        return count >= k

    lo, hi = 1, stalls[-1] - stalls[0]
    ans = 0
    while lo <= hi:
        d = (lo + hi) // 2
        if can_place(d):
            ans = d                # feasible → try larger
            lo = d + 1
        else:
            hi = d - 1
    return ans


# Test
print(aggressive_cows([1,2,4,8,9], 3))   # 3
print(aggressive_cows([1,2,8,4,9], 3))   # 3  (unsorted input ok)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Binary search on answer | O(n log(max-min)) + O(n log n) sort | O(1) |

---

## Edge Cases to remember

- **Sort first** — greedy placement needs sorted positions.
- **"Maximize" template** — on feasible, record answer and go **larger** (`lo=d+1`); mirror of Koko's minimize.
- **Always place the first cow at `stalls[0]`** — greedy start is optimal.
- **k = 2** → simply `max - min`.
- **k > number of stalls** → impossible (guard if needed).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Koko Bananas (Q2) | Minimize version |
| 2 | Magnetic Force Between Balls (LC 1552) | Same problem |
| 3 | Split Array Largest Sum (LC 410) | Min largest |
| 4 | Book Allocation / Painter's | Min max load |
| 5 | Minimize Max Distance Gas Stations | BS on real |
| 6 | Divide chocolate (LC 1231) | Max min piece |
| 7 | Place k routers/servers | Max min spacing |
| 8 | Capacity/rate feasibility | Greedy check |

---

## The ONE trick to remember

```
"MAXIMIZE THE MIN → BINARY-SEARCH THE GAP, GREEDILY PLACE, GO BIGGER ON SUCCESS."
```

Sort, then binary-search the distance. `feasible(d)` greedily places cows ≥ d apart and checks you fit k. On success, try a larger gap; on failure, smaller.

> Memory hook: "Guess a gap, greedily seat the cows, push the gap as wide as it'll go."

---
