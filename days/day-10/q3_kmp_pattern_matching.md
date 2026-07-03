# Q3 — KMP Pattern Matching (LeetCode 28)

## 1. What is this question actually asking?

Given a text string `haystack` and a pattern string `needle`, find the index of the first occurrence of `needle` in `haystack`. Return -1 if not found. The KMP (Knuth-Morris-Pratt) algorithm solves this in O(n+m) by precomputing a "failure function" (also called LPS — Longest Proper Prefix which is also Suffix) for the pattern.

## 2. Pattern

KMP — Precompute LPS array for needle. Use LPS to skip unnecessary comparisons when a mismatch occurs during matching (never move back in the text).

## 3. Understand with a Diagram

```
haystack = "aabxaabxcaabxaabxaaabx"
needle   = "aabxaaabx"

Step 1: Build LPS (Longest Proper Prefix = Suffix) for needle:

needle: a a b x a a a b x
index:  0 1 2 3 4 5 6 7 8

LPS[0] = 0 (single char, no proper prefix)
LPS[1]: "aa" → longest prefix = suffix = "a" → LPS[1]=1
LPS[2]: "aab" → no prefix = suffix → LPS[2]=0
LPS[3]: "aabx" → no prefix = suffix → LPS[3]=0
LPS[4]: "aabxa" → "a" = "a" → LPS[4]=1
LPS[5]: "aabxaa" → "aa" = "aa" → LPS[5]=2
LPS[6]: "aabxaaa" → "a" matches "aabxaaa"[6]='a', but then aa? → "aaa"... prefix "aa" != suffix "aaa"[1:3]="aa" → LPS[6]=2... actually:
  j=lps[j-1]=2, needle[2]='b' ≠ needle[6]='a', j=lps[1]=1, needle[1]='a'==needle[6]='a' → LPS[6]=2
LPS[7]: needle[7]='b', j=2, needle[2]='b'==needle[7]='b' → LPS[7]=3
LPS[8]: needle[8]='x', j=3, needle[3]='x'==needle[8]='x' → LPS[8]=4

LPS = [0,1,0,0,1,2,2,3,4]

Step 2: Match using LPS (i=text pointer, j=pattern pointer):
When mismatch: j = lps[j-1] (don't reset j to 0, skip to where pattern still matches)
When j == len(needle): found! return i - j

Matching "aabxaabxcaabxaabxaaabx" against "aabxaaabx":
         i=0123456789...

i=0,j=0: a==a → j=1,i=1
i=1,j=1: a==a → j=2,i=2
i=2,j=2: b==b → j=3,i=3
i=3,j=3: x==x → j=4,i=4
i=4,j=4: a==a → j=5,i=5
i=5,j=5: a==a → j=6,i=6
i=6,j=6: b≠a  → j=lps[5]=2, compare again (don't increment i)
i=6,j=2: b==b → j=3,i=7
i=7,j=3: x==x → j=4,i=8
i=8,j=4: c≠a  → j=lps[3]=0, compare again
i=8,j=0: c≠a  → j=0, i=9 (increment i, j stays 0)
i=9,j=0: a==a → j=1, i=10
...continue...find match at i=22-9=13

Key: we never backtrack in the text (i never decreases).
```

## 4. Brute Force → Better → Optimal

### Brute Force — Naive O(nm)
**Idea:** For each position i in text, compare pattern from that position. If mismatch, slide pattern by 1.

```
for i from 0 to n-m:
    if text[i:i+m] == pattern:
        return i
return -1
```

O(n*m) worst case (e.g., "aaaa...aab" with pattern "aab").

### Optimal — KMP O(n+m)
**Idea:** Precompute LPS in O(m). Use LPS to avoid re-checking matched characters in text. Text pointer never goes back.

O(n+m) time, O(m) space.

## 5. Pseudocode (Optimal)

```
function computeLPS(pattern):
    m = len(pattern)
    lps = [0] * m
    j = 0   // length of previous longest prefix suffix
    
    for i from 1 to m-1:
        while j > 0 and pattern[i] != pattern[j]:
            j = lps[j-1]   // fall back
        if pattern[i] == pattern[j]:
            j += 1
        lps[i] = j
    
    return lps


function KMPSearch(text, pattern):
    n, m = len(text), len(pattern)
    if m == 0: return 0
    
    lps = computeLPS(pattern)
    
    j = 0   // pattern pointer
    for i from 0 to n-1:
        while j > 0 and text[i] != pattern[j]:
            j = lps[j-1]   // fall back using LPS
        if text[i] == pattern[j]:
            j += 1
        if j == m:
            return i - m + 1   // found at index i-m+1
    
    return -1
```

## 6. Python Code

```python
def computeLPS(pattern):
    """
    Compute the LPS (Longest Proper Prefix which is also Suffix) array.
    lps[i] = length of longest proper prefix of pattern[0..i] that is also a suffix.
    """
    m = len(pattern)
    lps = [0] * m
    j = 0   # length of previous longest prefix-suffix
    i = 1
    
    while i < m:
        if pattern[i] == pattern[j]:
            j += 1
            lps[i] = j
            i += 1
        elif j > 0:
            j = lps[j-1]   # don't increment i, fall back
        else:
            lps[i] = 0
            i += 1
    
    return lps


def strStr(haystack, needle):
    """
    KMP string search: find first occurrence of needle in haystack.
    Returns start index or -1.
    """
    n, m = len(haystack), len(needle)
    if m == 0:
        return 0
    if m > n:
        return -1
    
    lps = computeLPS(needle)
    
    i = j = 0  # i = text pointer, j = pattern pointer
    while i < n:
        if haystack[i] == needle[j]:
            i += 1
            j += 1
            if j == m:
                return i - m  # found
        elif j > 0:
            j = lps[j-1]  # fall back (don't change i)
        else:
            i += 1  # no match, advance text
    
    return -1


def findAllOccurrences(text, pattern):
    """Find all starting indices of pattern in text."""
    n, m = len(text), len(pattern)
    lps = computeLPS(pattern)
    results = []
    
    i = j = 0
    while i < n:
        if text[i] == pattern[j]:
            i += 1; j += 1
            if j == m:
                results.append(i - m)
                j = lps[j-1]   # continue searching
        elif j > 0:
            j = lps[j-1]
        else:
            i += 1
    
    return results


# Test 1 — LeetCode example 1
print(strStr("sadbutsad", "sad"))         # 0

# Test 2 — LeetCode example 2
print(strStr("leetcode", "leeto"))        # -1

# Test 3 — pattern not in text
print(strStr("hello", "ll"))             # 2

# Test 4 — empty needle
print(strStr("hello", ""))               # 0

# Test 5 — LPS array verification
print(computeLPS("aabxaaabx"))           # [0,1,0,0,1,2,2,3,4]
print(computeLPS("aaaa"))                # [0,1,2,3]
print(computeLPS("abcabc"))              # [0,0,0,1,2,3]

# Test 6 — all occurrences
print(findAllOccurrences("aababab", "ab"))  # [1, 3, 5]
```

## 7. Complexity Table

| Approach     | Time    | Space   | Notes                              |
|--------------|---------|---------|------------------------------------|
| Naive        | O(n*m)  | O(1)    | Simple but slow on worst case      |
| KMP          | O(n+m)  | O(m)    | Optimal; never backtracks in text  |
| Rabin-Karp   | O(n+m)  | O(1)    | Hash-based; worst case O(nm)       |
| Z Algorithm  | O(n+m)  | O(n+m)  | Similar idea, different precompute |

## 8. Edge Cases to Remember

- Empty pattern — return 0 (LC convention)
- Pattern longer than text — return -1
- Pattern = text — return 0
- All same characters — LPS = [0,1,2,...,m-1]; many partial matches but O(n+m) overall
- LPS[0] is always 0 (single character has no proper prefix/suffix)
- When j falls back: use lps[j-1] (not lps[j]) — this is the most common mistake

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Shortest Palindrome                   | KMP/LPS to find palindromic prefix                  | LC 214     |
| Repeated Substring Pattern            | KMP — string is rotation of itself                  | LC 459     |
| Find All Anagrams in String           | Sliding window (similar string search)              | LC 438     |
| Minimum Window Substring              | Sliding window on strings                           | LC 76      |
| String Compression                    | Pattern-based string manipulation                   | LC 443     |
| Implement strStr()                    | This exact problem                                  | LC 28      |
| Index of First Occurrence             | Same as LC 28                                       | LC 28      |
| Z Algorithm for Pattern Matching      | Alternative to KMP, same complexity                 | Classic    |

## 10. The ONE Trick to Remember

**"KMP = precompute LPS (failure function), then scan text: match → advance both; mismatch → j = lps[j-1] (never backtrack i)."**

LPS[i] tells us: when the pattern mismatches at position j, we can reset j to lps[j-1] because those first lps[j-1] characters of the pattern still match the text at the current position. This avoids re-comparing characters we already know match. The LPS computation itself uses the same logic recursively. The most common mistake: using lps[j] instead of lps[j-1] when falling back.
