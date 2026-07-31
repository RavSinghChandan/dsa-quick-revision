# Day 10 — Math, Bit & Trie · Q2 · Prime Factorization of a Number

---

## What is this question actually asking?

Break a number into its **prime factors** (with multiplicity).

```
60 → [2, 2, 3, 5]   (2² × 3 × 5)
97 → [97]           (prime)
```

> Tests the √N trial-division idea: divide out each prime factor; anything left over above 1 is itself prime. With a precomputed **smallest-prime-factor sieve**, factoring becomes O(log N).

---

## Pattern

```
PATTERN: Trial division up to √N (or SPF sieve for many queries)
```

Whenever you see: *"factorize a number / list prime factors"* → divide out 2s, then odd numbers up to √N. Any remaining value > 1 is a prime factor. For many queries, precompute smallest prime factors and divide repeatedly.

---

## Understand with a diagram

```
n = 60
divide by 2: 60→30→15  → factors [2, 2]
try 3: 15→5           → factor [3]
try 5 (5*5>5? loop ends when d*d>n): remaining n=5 > 1 → factor [5]
result [2,2,3,5]

Key: only trial-divide up to √n; a leftover > 1 must be prime.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — try all divisors up to n — O(n)
Test every number.

### 🟢 Trial division to √N — O(√n)
Divide out each factor; the leftover is prime if > 1.

### 🟢 SPF sieve (many queries) — O(log n) per query after O(N log log N) sieve
Precompute smallest prime factor; repeatedly divide by `spf[n]`.

---

## Pseudocode (Trial division)

```
function factorize(n):
    factors = []
    while n % 2 == 0: factors.append(2); n //= 2
    d = 3
    while d*d <= n:
        while n % d == 0: factors.append(d); n //= d
        d += 2
    if n > 1: factors.append(n)      ← leftover prime
    return factors
```

---

## Python Code

```python
def prime_factorize(n: int) -> list[int]:
    factors = []
    while n % 2 == 0:
        factors.append(2)
        n //= 2
    d = 3
    while d * d <= n:                  # only up to √n
        while n % d == 0:
            factors.append(d)
            n //= d
        d += 2
    if n > 1:                          # leftover is prime
        factors.append(n)
    return factors


# SPF sieve version (fast for many queries)
def build_spf(N: int) -> list[int]:
    spf = list(range(N + 1))
    i = 2
    while i * i <= N:
        if spf[i] == i:                # i is prime
            for j in range(i * i, N + 1, i):
                if spf[j] == j:
                    spf[j] = i
        i += 1
    return spf

def factorize_with_spf(n: int, spf: list[int]) -> list[int]:
    factors = []
    while n > 1:
        factors.append(spf[n])
        n //= spf[n]
    return factors


# Test
print(prime_factorize(60))   # [2, 2, 3, 5]
print(prime_factorize(97))   # [97]
spf = build_spf(100)
print(factorize_with_spf(60, spf))   # [2, 2, 3, 5]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Trial division | O(√n) | O(1) |
| SPF sieve per query | O(log n) after O(N log log N) sieve | O(N) |

---

## Edge Cases to remember

- **Handle factor 2 separately**, then step odd `d` by 2 — halves the work.
- **Leftover `n > 1` is prime** — after dividing all factors ≤ √n, any remainder is a single prime.
- **Loop condition `d*d <= n`** — n shrinks as you divide, so this stays tight.
- **n = 1** → no prime factors (empty list).

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Count distinct prime factors | Factorize + set |
| 2 | Number of divisors | From factor exponents |
| 3 | Sum of divisors | From factorization |
| 4 | GCD / LCM via factors | Factor intersection |
| 5 | Ugly Number II (LC 264) | Prime multiples |
| 6 | Smallest multiple / LCM of range | Factor max powers |
| 7 | Four Divisors (LC 1390) | Factorization |
| 8 | Closest divisors (LC 1362) | Factor pairs |

---

## The ONE trick to remember

```
"DIVIDE OUT FACTORS UP TO √n; A LEFTOVER > 1 IS ITSELF PRIME."
```

Peel off 2s, then odd divisors up to √n, each as many times as it divides. Whatever remains above 1 is a prime factor. For many queries, an SPF sieve factors each number in O(log n).

> Memory hook: "Keep dividing by the smallest factor; the last piece standing is prime."

---
