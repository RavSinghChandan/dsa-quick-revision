# Q4 — Fractional Knapsack (2-min card)

**Fill a weight-limited bag with items (fractions allowed) to maximize total value.**

**Pattern:** Greedy — sort by value/weight ratio descending, fill greedily

```
Items: w=[10,20,30]  v=[60,100,120]  cap=50
Ratios: 6.0   5.0    4.0

Take A fully (10kg, +60)  remaining=40
Take B fully (20kg, +100) remaining=20
Take 20/30 of C  (+80)    remaining=0

Total = 60+100+80 = 240.0
```

**The trick:** "Densest item first" — sort by value-per-kg; take as much of each as possible; fraction the last one.

```python
items = sorted(zip([v/w for v,w in zip(values,weights)], weights, values), reverse=True)
for ratio, w, v in items:
    if w <= rem: total += v; rem -= w
    else: total += ratio * rem; break
```

**Complexity:** Time O(n log n) | Space O(n)

**Same pattern solves:** Maximum Units on Truck (LC 1710), Min Cost Hire K Workers (LC 857), Assign Cookies (LC 455), 0/1 Knapsack (DP variant), Gas Station (LC 134), Task Scheduler (LC 621), Largest Number (LC 179).
