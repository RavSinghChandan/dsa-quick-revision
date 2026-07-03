# Q4 — Z Algorithm for String Matching

## 1. What is this question actually asking?

The Z Algorithm computes the Z-array for a string, where Z[i] is the length of the longest substring starting at position i that is also a prefix of the entire string. This enables pattern matching in O(n+m): concatenate "pattern + '$' + text", compute Z-array, and any position where Z[i] >= len(pattern) is a match.

Z[0] is conventionally set to 0 (or n — the length of the whole string, but we set it to 0 by convention since the entire string trivially matches itself at position 0).

## 2. Pattern

Z Algorithm — precompute Z-array in O(n) using a window [l, r] of the current rightmost match. For each position, reuse previously computed information.

## 3. Understand with a Diagram

```
String: "aabxaa"  (length n=6)

Z-array computation:
Z[0] = 0 (convention — skip position 0)

Maintain window [l, r]: rightmost interval [l..r] where s[l..r] == s[0..r-l]

i=1:
  i > r, compute naively: s[0]='a'==s[1]='a'? Yes; s[1]='a'==s[2]='b'? No
  Z[1] = 1, update l=1, r=1

i=2:
  i > r, compute naively: s[0]='a'==s[2]='b'? No
  Z[2] = 0

i=3:
  i > r, compute naively: s[0]='a'==s[3]='x'? No
  Z[3] = 0

i=4:
  i > r, compute naively: s[0]='a'==s[4]='a'? Yes; s[1]='a'==s[5]='a'? Yes; s[2]='b'==s[6]? No (out of bounds)
  Z[4] = 2, update l=4, r=5

i=5:
  i <= r (5 <= 5). k = i-l = 5-4 = 1. Z[k] = Z[1] = 1. r-i+1 = 5-5+1 = 1.
  Z[1] < r-i+1? 1 < 1? No (equal). So Z[5] = Z[1] = 1.
  (No extension needed since Z[1] doesn't exceed window)

Z = [0, 1, 0, 0, 2, 1]
      |                |
      conv=0          "aa" matches prefix "aa"

Pattern Matching example:
pattern = "aab", text = "aabxaab"
s = "aab$aabxaab"  (concat with sentinel '$')
     0123456789...

Compute Z for "aab$aabxaab":
Z = [0, 1, 0, 0, 3, 1, 0, 0, 3, 1, 0]

Any Z[i] == len(pattern) = 3 → match at position i - (m+1)
  Z[4] = 3 → match at 4 - 4 = 0
  Z[8] = 3 → match at 8 - 4 = 4

Matches at positions 0 and 4 in text ✓

Window [l, r] optimization:
  When i is inside [l,r], we know s[i..r] == s[i-l..r-l]
  So Z[i] >= min(Z[i-l], r-i+1)
  - If Z[i-l] < r-i+1: Z[i] = Z[i-l] (entirely within known window)
  - If Z[i-l] >= r-i+1: Z[i] >= r-i+1, extend from r+1 naively, update l, r
```

## 4. Brute Force → Better → Optimal

### Brute Force — O(n^2)
**Idea:** For each position i, compare s[i..] with s[0..] character by character.

```
for i from 1 to n-1:
    Z[i] = 0
    while i + Z[i] < n and s[Z[i]] == s[i + Z[i]]:
        Z[i] += 1
```

O(n^2) worst case (e.g., "aaaa...a").

### Optimal — O(n) with window [l, r]
**Idea:** Reuse previous Z values when current position falls within the known rightmost window.

O(n) time, O(n) space.

## 5. Pseudocode (Optimal)

```
function computeZ(s):
    n = len(s)
    Z = [0] * n
    l = r = 0
    
    for i from 1 to n-1:
        if i <= r:
            Z[i] = min(r - i + 1, Z[i - l])
        
        // Extend naively from current Z[i]
        while i + Z[i] < n and s[Z[i]] == s[i + Z[i]]:
            Z[i] += 1
        
        if i + Z[i] - 1 > r:
            l = i
            r = i + Z[i] - 1
    
    return Z


function zPatternMatch(text, pattern):
    s = pattern + '$' + text
    Z = computeZ(s)
    m = len(pattern)
    results = []
    
    for i from m+1 to len(s)-1:
        if Z[i] == m:
            results.append(i - m - 1)
    
    return results
```

## 6. Python Code

```python
def computeZ(s):
    """
    Compute Z-array: Z[i] = length of longest substring starting at i
    that matches a prefix of s. Z[0] = 0 by convention.
    O(n) time.
    """
    n = len(s)
    Z = [0] * n
    l = r = 0
    
    for i in range(1, n):
        if i <= r:
            # We're inside known match window [l, r]
            Z[i] = min(r - i + 1, Z[i - l])
        
        # Try to extend from s[Z[i]] vs s[i + Z[i]]
        while i + Z[i] < n and s[Z[i]] == s[i + Z[i]]:
            Z[i] += 1
        
        # Update rightmost window
        if i + Z[i] - 1 > r:
            l = i
            r = i + Z[i] - 1
    
    return Z


def zSearch(text, pattern):
    """
    Find all occurrences of pattern in text using Z algorithm.
    Concatenate: pattern + '$' + text, then find positions where Z[i] == len(pattern).
    """
    if not pattern:
        return list(range(len(text)))
    
    m = len(pattern)
    s = pattern + '$' + text
    Z = computeZ(s)
    
    results = []
    for i in range(m + 1, len(s)):
        if Z[i] == m:
            results.append(i - m - 1)  # position in text
    
    return results


def zSearchFirst(haystack, needle):
    """Return index of first occurrence or -1 (like LC 28)."""
    if not needle:
        return 0
    m = len(needle)
    s = needle + '$' + haystack
    Z = computeZ(s)
    
    for i in range(m + 1, len(s)):
        if Z[i] == m:
            return i - m - 1
    
    return -1


# Test 1 — Z-array computation
print(computeZ("aabxaa"))        # [0,1,0,0,2,1]
print(computeZ("aaaa"))          # [0,3,2,1]
print(computeZ("abcabc"))        # [0,0,0,3,0,0]
print(computeZ("aabxaaabx"))     # [0,1,0,0,1,2,2,3,4]

# Test 2 — pattern matching
print(zSearch("aabxaab", "aab"))                 # [0, 4]
print(zSearch("AABABAB", "AB"))                  # [1, 3, 5]

# Test 3 — first occurrence only
print(zSearchFirst("sadbutsad", "sad"))          # 0
print(zSearchFirst("leetcode", "leeto"))         # -1
print(zSearchFirst("hello", "ll"))               # 2

# Test 4 — all same chars
print(zSearch("aaaa", "aa"))   # [0, 1, 2]
```

## 7. Complexity Table

| Approach       | Time    | Space   | Notes                               |
|----------------|---------|---------|-------------------------------------|
| Naive Z-array  | O(n^2)  | O(n)    | Without window optimization         |
| Z Algorithm    | O(n)    | O(n)    | With [l,r] window, optimal          |
| KMP            | O(n+m)  | O(m)    | Similar; uses failure function      |

## 8. Edge Cases to Remember

- Z[0] = 0 by convention (whole string matches itself — undefined/trivial)
- Sentinel character '$' prevents false matches where pattern matches cross the boundary
- If pattern has '$' → use a character not in text (any separator works)
- Single character string — Z = [0]
- When Z[i-l] < r-i+1: Z[i] = Z[i-l] exactly (no need to extend)
- When Z[i-l] == r-i+1: extend from r+1 (could be more, update l, r)
- Z Algorithm vs KMP: similar concept, Z uses explicit window, KMP uses failure function

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Implement strStr() (KMP)              | Alternative to Z for pattern matching               | LC 28      |
| Repeated Substring Pattern            | Z[i]+i==n for some i in (0,n) → true              | LC 459     |
| Shortest Palindrome                   | Z on reversed string to find palindrome prefix      | LC 214     |
| Find All Anagrams                     | Sliding window (frequency map comparison)           | LC 438     |
| Longest Happy Prefix                  | KMP/Z — longest prefix = suffix                     | LC 1392    |
| String Matching in Array              | Z / KMP for multi-pattern                           | LC 1408    |
| Detect Pattern of Length M Repeated K | Pattern detection in arrays                        | LC 1566    |
| Number of Distinct Substrings         | Z helps count unique strings                        | GFG        |

## 10. The ONE Trick to Remember

**"Z[i] = longest match of s[i..] with prefix s[0..]. For matching: concat 'pattern$text', find Z[i]==len(pattern). Use [l,r] window: Z[i] = min(r-i+1, Z[i-l]) then extend."**

The window [l, r] tracks the rightmost substring that matches a prefix of s. When i falls inside this window, Z[i-l] tells us the match length at the corresponding prefix position — if it's smaller than the remaining window (r-i+1), we know exactly Z[i]; otherwise we may need to extend. This reuse of computed information gives O(n) amortized — each character is compared at most twice (once to establish the window, once potentially when extending a new window).
