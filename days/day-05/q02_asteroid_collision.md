# Day 5 — Stack, Queue & Greedy · Q2 · Asteroid Collision

---

## What is this question actually asking?

Asteroids move on a line; value = size, sign = direction (`+` right, `-` left). Same-size opposite collision destroys both; bigger survives. Return the state after all collisions.

```
[5, 10, -5] → [5, 10]   (10 beats -5)
[8, -8]     → []         (equal → both explode)
[10, 2, -5] → [10]       (-5 kills 2, then 10 kills -5)
```

> A **stack simulation** — a right-mover on the stack may collide with an incoming left-mover.

---

## Pattern

```
PATTERN: Stack simulation (collisions resolve against the stack top)
```

Whenever you see: *"elements interact/cancel with the most recent survivor"* → push onto a stack; a new item may repeatedly collide with the top until it's destroyed, survives, or the top is destroyed.

---

## Understand with a diagram

```
Only collide when top > 0 (moving right) AND new < 0 (moving left).

[10, 2, -5]
push 10, push 2
-5 arrives: top=2 (right), -5 (left) → |−5|>2 → pop 2; still colliding
            top=10 (right) → |−5|<10 → -5 destroyed
result stack: [10]
```

Equal sizes → both destroyed. Same direction or `-` then `+` → no collision.

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — repeat passes until stable — O(n²)
Scan and cancel pairs repeatedly.

### 🟢 Optimal — one pass with a stack — O(n)
Each asteroid pushed/popped once. Collisions only when a positive is on top and a negative comes.

---

## Pseudocode (Optimal)

```
function collide(asteroids):
    stack = []
    for a in asteroids:
        alive = True
        while alive and a < 0 and stack and stack[-1] > 0:   ← collision
            top = stack[-1]
            if top < -a: stack.pop()          ← top loses, keep colliding
            elif top == -a: stack.pop(); alive = False  ← both die
            else: alive = False               ← a loses
        if alive: stack.push(a)
    return stack
```

---

## Python Code

```python
def asteroid_collision(asteroids: list[int]) -> list[int]:
    stack = []
    for a in asteroids:
        alive = True
        while alive and a < 0 and stack and stack[-1] > 0:
            top = stack[-1]
            if top < -a:            # top smaller → destroyed, a keeps going
                stack.pop()
            elif top == -a:         # equal → both destroyed
                stack.pop()
                alive = False
            else:                   # top bigger → a destroyed
                alive = False
        if alive:
            stack.append(a)
    return stack


# Test
print(asteroid_collision([5, 10, -5]))   # [5, 10]
print(asteroid_collision([8, -8]))       # []
print(asteroid_collision([10, 2, -5]))   # [10]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Stack | O(n) | O(n) |

---

## Edge Cases to remember

- **Collision ONLY when top > 0 and a < 0** — a right-mover already passed a left-mover in front of it; same signs never collide.
- **Equal sizes → both die**, and `a` must stop (`alive=False`).
- **`while` loop** — `a` may destroy several stack elements in a row.
- **`-a` is the magnitude** of a negative asteroid; compare magnitudes.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Asteroid Collision (LC 735) | This exact problem |
| 2 | Valid Parentheses (LC 20) | Stack cancellation |
| 3 | Remove All Adjacent Duplicates (LC 1047) | Stack collapse |
| 4 | Backspace String Compare (LC 844) | Stack sim |
| 5 | Car fleet (LC 853) | Stack of fleets |
| 6 | Simplify Path (LC 71) | Stack |
| 7 | Remove K Digits (Q7) | Monotonic stack |
| 8 | Baseball game (LC 682) | Stack sim |

---

## The ONE trick to remember

```
"STACK SIM: A NEGATIVE COLLIDES WITH POSITIVES ON TOP UNTIL RESOLVED."
```

Push each asteroid. A leftward (`-`) asteroid collides with rightward (`+`) tops: smaller top pops (keep going), equal → both die, bigger top → the newcomer dies. Only push if it survives.

> Memory hook: "The new left-mover fights the stack's right-movers one by one."

---
