# ⚡ Quick Note · Day 10 — Math, Bit & Trie · Q2 · Prime Factorization
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "List prime factors of n with multiplicity."

## Pattern
`Trial division to √n (or SPF sieve for many queries)`

## Visual in your head
```
divide out 2s
for d=3,5,7... while d*d<=n: divide out d
leftover n>1 → prime factor
```

## The trick (say it out loud)
> "Divide out factors up to √n; a leftover > 1 is itself prime. SPF sieve for many queries."

## Code skeleton
```python
while n%2==0: f.append(2); n//=2
d=3
while d*d<=n:
    while n%d==0: f.append(d); n//=d
    d+=2
if n>1: f.append(n)
```

## Complexity
- Trial O(√n) · SPF O(log n)/query · Space O(1)/O(N)

## This trick solves more
Distinct prime factors · #divisors · GCD/LCM · Ugly Number II (264) · Four Divisors (1390)
