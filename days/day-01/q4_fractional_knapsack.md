# Q4 — Fractional Knapsack

## 1. What is this question actually asking?

You have a bag (knapsack) with a weight capacity, and a list of items each with a weight and a value. Unlike the 0/1 knapsack where you either take an item or leave it, here you can take a fraction of any item. Your goal is to maximize the total value you put in the bag without exceeding the weight limit. Think of the items as bags of gold, silver, or grain — you can scoop out exactly how much you need.

## 2. Pattern

Greedy — sort by value-per-unit-weight ratio, take greedily from highest ratio to lowest

## 3. Understand with a Diagram

```
Items:
Item  Weight  Value  Value/Weight (ratio)
 A      10     60       6.0   ← best
 B      20    100       5.0
 C      30    120       4.0

Knapsack capacity = 50

Sort by ratio descending: A(6.0), B(5.0), C(4.0)

Knapsack filling:
┌────────────────────────────────────────────────────┐
│ capacity = 50                                      │
│                                                    │
│  Take all of A (10 kg) → value += 60, remain = 40 │
│  [AAAAAAAAAA                                    ]  │
│                                                    │
│  Take all of B (20 kg) → value += 100, remain = 20│
│  [AAAAAAAAAABBBBBBBBBBBBBBBBBBBB                ]  │
│                                                    │
│  Take 20/30 of C → value += 120*(20/30) = 80      │
│  [AAAAAAAAAABBBBBBBBBBBBBBBBBBBBCCCCCCCCCCCCCCCC]  │
│                                                    │
│  Total value = 60 + 100 + 80 = 240                │
└────────────────────────────────────────────────────┘

Ratio visualization:
A: ██████ (6.0)
B: █████  (5.0)
C: ████   (4.0)
   Take highest ratio first!
```

## 4. Brute Force → Better → Optimal

### Brute Force — Try all combinations
**Idea:** For n items, try every possible combination of whole and fractional amounts. This is essentially a continuous optimization problem with exponential search space.

```
for every possible combination of (fraction_1, fraction_2, ..., fraction_n):
    where each fraction_i is between 0 and 1:
        total_weight = sum(fraction_i * weight_i)
        if total_weight <= capacity:
            total_value = sum(fraction_i * value_i)
            answer = max(answer, total_value)
```

Why hopeless: Continuous space, infinitely many combinations. Not practically enumerable.

### Better — Greedy without sorting
**Idea:** Scan items linearly, always picking the current best available item each round. O(n²) because for each unit of capacity you scan all items.

```
while remaining_capacity > 0:
    find item with best remaining ratio
    take as much of it as possible
    reduce remaining_capacity
```

Why better but still slow: O(n²) — you scan all items for each selection round.

### Optimal — Sort by ratio then greedy
**Idea:** Sort all items by value/weight ratio in descending order. Then fill the knapsack greedily: take entire items as long as capacity allows, and take a fraction of the first item that doesn't fully fit.

```
sort items by (value/weight) descending

remaining = capacity
total_value = 0

for each item in sorted order:
    if item.weight <= remaining:
        take entire item
        remaining -= item.weight
        total_value += item.value
    else:
        fraction = remaining / item.weight
        total_value += fraction * item.value
        remaining = 0
        break

return total_value
```

**Trace:**
```
Sorted: A(w=10,v=60,r=6.0), B(w=20,v=100,r=5.0), C(w=30,v=120,r=4.0)
capacity=50, remaining=50, total=0

Item A: weight=10 <= remaining=50 → take all
  remaining = 50-10 = 40, total = 0+60 = 60

Item B: weight=20 <= remaining=40 → take all
  remaining = 40-20 = 20, total = 60+100 = 160

Item C: weight=30 > remaining=20 → take fraction 20/30
  total = 160 + (20/30)*120 = 160 + 80 = 240
  remaining = 0, break

Answer: 240.0
```

## 5. Pseudocode (Optimal)

```
function fractionalKnapsack(weights[], values[], capacity):
    n = len(weights)
    items = [(values[i]/weights[i], weights[i], values[i]) for i in 0..n-1]
    sort items by ratio descending
    
    remaining = capacity
    total = 0.0
    
    for (ratio, weight, value) in items:
        if weight <= remaining:
            total += value
            remaining -= weight
        else:
            total += ratio * remaining
            break
    
    return total
```

## 6. Python Code

```python
def fractional_knapsack(weights, values, capacity):
    n = len(weights)
    # Create (ratio, weight, value) tuples
    items = sorted(
        zip([v/w for v, w in zip(values, weights)], weights, values),
        reverse=True
    )
    
    remaining = capacity
    total_value = 0.0
    
    for ratio, weight, value in items:
        if weight <= remaining:
            total_value += value
            remaining -= weight
        else:
            # Take fraction
            total_value += ratio * remaining
            remaining = 0
            break
    
    return total_value


# Test 1 — standard example
weights = [10, 20, 30]
values  = [60, 100, 120]
capacity = 50
print(f"Max value: {fractional_knapsack(weights, values, capacity)}")
# Max value: 240.0

# Test 2 — capacity larger than all items
weights2 = [5, 10]
values2  = [50, 60]
capacity2 = 100
print(f"All items fit: {fractional_knapsack(weights2, values2, capacity2)}")
# All items fit: 110.0

# Test 3 — capacity = 0
print(f"Zero capacity: {fractional_knapsack(weights, values, 0)}")
# Zero capacity: 0.0

# Test 4 — single item, take fraction
print(f"Single partial: {fractional_knapsack([10], [100], 5)}")
# Single partial: 50.0
```

## 7. Complexity Table

| Approach      | Time       | Space | Notes                             |
|---------------|------------|-------|-----------------------------------|
| Brute Force   | O(∞)       | O(n)  | Continuous space, not feasible    |
| Greedy no sort| O(n²)      | O(1)  | Re-scan for best each round       |
| Optimal       | O(n log n) | O(n)  | Sorting dominates                 |

## 8. Edge Cases to Remember

- Capacity is 0 — return 0.0 immediately
- All items heavier than capacity — take fraction of the best-ratio item only
- One item exactly fits — take it whole (no fraction needed)
- Two items with same ratio — order between them doesn't matter
- Very large values / small weights — ratio can overflow in some languages, use float carefully
- Items with weight 0 — undefined ratio, skip or handle separately (guard against divide by zero)
- Capacity larger than total weight of all items — take everything, return sum of all values

## 9. Similar Questions

| Question                              | Where Pattern Applies                                | LeetCode # |
|---------------------------------------|------------------------------------------------------|------------|
| 0/1 Knapsack                          | Same setup but no fractions allowed, needs DP        | —          |
| Minimum Cost to Hire K Workers        | Ratio-based greedy (wage/quality)                    | LC 857     |
| Task Scheduler                        | Greedily assign most frequent tasks                  | LC 621     |
| Gas Station                           | Greedy traversal with running sum                    | LC 134     |
| Assign Cookies                        | Match greedily by size ratio                         | LC 455     |
| Reorganize String                     | Greedy by frequency ratio                            | LC 767     |
| Largest Number                        | Custom sort by ratio of concatenation                | LC 179     |
| Maximum Units on a Truck              | Sort by units/box, greedy fill — same as this!       | LC 1710    |

## 10. The ONE Trick to Remember

**"Densest item first — value per kg is your currency."**

The ratio value/weight tells you how much bang you get per unit of weight. Always consume the highest-density item first. Once it's gone, move to the next best. If the knapsack runs out of room mid-item, just scoop out exactly what fits. This works because fractions are allowed — unlike 0/1 knapsack where greedily picking highest ratio can fail.
