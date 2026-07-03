# Q3 — KMP Pattern Matching (2-min card) — LC 28

**Find first occurrence of pattern in text in O(n+m). Never backtrack in text.**

**Pattern:** Precompute LPS (Longest Proper Prefix = Suffix). Use LPS to skip recomparisons.

```
needle = "aabxaaabx"
LPS  =  [0,1,0,0,1,2,2,3,4]

LPS[5]=2 means "aabxaa" has longest prefix=suffix "aa" of length 2.
On mismatch at j=6: j = lps[5] = 2 (skip to position 2, not 0)

Matching: i never decreases; j falls back via LPS on mismatch.
```

**The trick:** "Mismatch at j → j = lps[j-1] (not lps[j]!). Text pointer i never moves back."

```python
def computeLPS(pattern):
    m = len(pattern); lps = [0]*m; j = 0; i = 1
    while i < m:
        if pattern[i] == pattern[j]: j+=1; lps[i]=j; i+=1
        elif j > 0: j = lps[j-1]
        else: lps[i]=0; i+=1
    return lps

def strStr(haystack, needle):
    n,m = len(haystack),len(needle)
    if m==0: return 0
    lps = computeLPS(needle); i=j=0
    while i < n:
        if haystack[i]==needle[j]: i+=1; j+=1
        elif j > 0: j=lps[j-1]
        else: i+=1
        if j==m: return i-m
    return -1
```

**Complexity:** Time O(n+m) | Space O(m)

**Common mistake:** Use lps[j-1] not lps[j] when falling back.

**Same pattern solves:** Shortest Palindrome (LC 214), Repeated Substring (LC 459), All Occurrences (extend with lps on match), Z Algorithm (alternate approach).
