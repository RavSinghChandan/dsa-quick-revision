# ⚡ Quick Note · Day 10 — Math, Bit & Trie · Q1 · Sieve of Primes
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "All primes up to N."

## Pattern
`Sieve of Eratosthenes — mark multiples from p*p`

## Visual in your head
```
is_prime[0..N]=True; 0,1=False
for p while p*p<=N:
  if is_prime[p]: mark p*p, p*p+p, ... False
survivors = primes
```

## The trick (say it out loud)
> "Mark multiples starting at p*p; loop p only to √N. Survivors are prime."

## Code skeleton
```python
p=2
while p*p<=N:
    if is_prime[p]:
        for m in range(p*p,N+1,p): is_prime[m]=False
    p+=1
```

## Complexity
- Time O(N log log N) · Space O(N)

## This trick solves more
Count Primes (204) · SPF sieve · Prime Factorization · Totient sieve · Closest Prime Pair (2523)
