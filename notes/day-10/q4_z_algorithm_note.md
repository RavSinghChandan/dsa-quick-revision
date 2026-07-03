# Q4 — Z Algorithm for String Matching (2-min card)

**Z[i] = length of longest substring starting at i that matches prefix of s. O(n) via window [l,r].**

**Pattern:** Concat "pattern$text", compute Z, positions where Z[i]==len(pattern) are matches.

```
s = "aabxaa"
Z = [0, 1, 0, 0, 2, 1]
         ↑           ↑
      s[1..]="abxaa" prefix "a" len=1
                  s[4..]="aa" prefix "aa" len=2

Pattern match "aab" in "aabxaab":
s = "aab$aabxaab"
Z = [0,1,0,0,3,1,0,0,3,1,0]
              ↑         ↑
              Z[4]=3=m → match at 4-4=0
              Z[8]=3=m → match at 8-4=4
```

**The trick:** "Z[i]=min(r-i+1, Z[i-l]) inside window, then extend. Match: concat with '$', find Z[i]==m."

```python
def computeZ(s):
    n = len(s); Z = [0]*n; l = r = 0
    for i in range(1, n):
        if i <= r: Z[i] = min(r-i+1, Z[i-l])
        while i+Z[i] < n and s[Z[i]] == s[i+Z[i]]: Z[i]+=1
        if i+Z[i]-1 > r: l=i; r=i+Z[i]-1
    return Z

def zSearch(text, pattern):
    m = len(pattern); s = pattern+'$'+text; Z = computeZ(s)
    return [i-m-1 for i in range(m+1, len(s)) if Z[i]==m]
```

**Complexity:** Time O(n+m) | Space O(n+m)

**KMP vs Z:** KMP uses failure function lps[], Z uses explicit window [l,r]. Both O(n+m).

**Same pattern solves:** Repeated Substring (LC 459), Shortest Palindrome (LC 214), Longest Happy Prefix (LC 1392), strStr (LC 28).
