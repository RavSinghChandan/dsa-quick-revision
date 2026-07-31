# Day 10 — Math, Bit & Trie · Q1 · Print All Primes Till N (Sieve of Eratosthenes)

---

## What is this question actually asking?

List all prime numbers **up to N** efficiently.

```
N = 20 → [2, 3, 5, 7, 11, 13, 17, 19]
```

> The **Sieve of Eratosthenes** — mark multiples of each prime as composite. O(N log log N), far faster than testing each number.

---

## Pattern

```
PATTERN: Sieve — mark multiples of each prime starting from p*p
```

Whenever you see: *"all primes up to N / count primes / smallest prime factor"* → make a boolean array; for each prime p, mark `p*p, p*p+p, ...` as composite. Whatever stays true is prime.

---

## Understand with a diagram

```
is_prime = [True]*(N+1); 0,1 = False
for p from 2 to √N:
    if is_prime[p]:
        for multiple m = p*p, p*p+p, ... <= N:
            is_prime[m] = False        ← composite

Start at p*p because smaller multiples (2p,3p...) were marked by smaller primes.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — trial-divide each number — O(N·√N)
Test primality of every number individually.

### 🟢 Optimal — Sieve — O(N log log N)
Mark composites in bulk. Start each prime's marking at `p*p`.

---

## Pseudocode (Optimal)

```
function sieve(N):
    is_prime = [True]*(N+1); is_prime[0]=is_prime[1]=False
    p = 2
    while p*p <= N:
        if is_prime[p]:
            for m from p*p to N step p:
                is_prime[m] = False
        p += 1
    return [i for i in 2..N if is_prime[i]]
```

---

## Python Code

```python
def sieve_of_eratosthenes(N: int) -> list[int]:
    if N < 2:
        return []
    is_prime = [True] * (N + 1)
    is_prime[0] = is_prime[1] = False
    p = 2
    while p * p <= N:
        if is_prime[p]:
            for m in range(p * p, N + 1, p):    # start at p*p
                is_prime[m] = False
        p += 1
    return [i for i in range(2, N + 1) if is_prime[i]]


# Test
print(sieve_of_eratosthenes(20))   # [2, 3, 5, 7, 11, 13, 17, 19]
print(len(sieve_of_eratosthenes(100)))   # 25 primes below/at 100
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Trial division | O(N·√N) | O(1) |
| Sieve | O(N log log N) | O(N) |

---

## Edge Cases to remember

- **Start marking at `p*p`** — multiples below `p*p` were already marked by smaller primes; this is the key optimization.
- **Only loop `p` up to √N** — beyond that, all composites are already marked.
- **0 and 1 are not prime** — set them false explicitly.
- **Segmented sieve** for very large N (memory) — sieve in blocks.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Count Primes (LC 204) | Sieve then count |
| 2 | Smallest Prime Factor (SPF) sieve | Store factor while marking |
| 3 | Prime Factorization (Q2) | Use SPF for fast factoring |
| 4 | Distinct prime factors count | Sieve variant |
| 5 | Ugly Numbers | Multiples generation |
| 6 | Closest prime pair (LC 2523) | Sieve then scan |
| 7 | Euler's totient sieve | Sieve-based |
| 8 | Prime subtraction ops | Precompute primes |

---

## The ONE trick to remember

```
"MARK MULTIPLES FROM p*p; LOOP p ONLY TO √N. SURVIVORS ARE PRIME."
```

Cross out composites in bulk. Each prime starts marking at its square (smaller multiples already handled), and you only need primes up to √N to catch every composite. O(N log log N).

> Memory hook: "Strike out every multiple; what's never struck is prime."

---
