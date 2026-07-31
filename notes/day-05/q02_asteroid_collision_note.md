# ⚡ Quick Note · Day 5 — Stack & Greedy · Q2 · Asteroid Collision
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Simulate asteroid collisions (sign=direction, value=size)."

## Pattern
`Stack simulation`

## Visual in your head
```
collide only when top>0 and a<0
top < |a| → pop top, keep going
top == |a| → both die
top > |a| → a dies
survive → push
```

## The trick (say it out loud)
> "Stack sim. A negative fights positive tops until it dies, survives, or eats them. Equal → both die."

## Code skeleton
```python
while alive and a<0 and stack and stack[-1]>0:
    if stack[-1]<-a: stack.pop()
    elif stack[-1]==-a: stack.pop(); alive=False
    else: alive=False
if alive: stack.append(a)
```

## Complexity
- Time O(n) · Space O(n)

## This trick solves more
Asteroid Collision (735) · Valid Parentheses (20) · Adjacent Duplicates (1047) · Car Fleet (853)
