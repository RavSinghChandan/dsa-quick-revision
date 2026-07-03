# Q2 — Word Break (2-min card) — LC 139

**Can s be segmented into words from wordDict? Words may be reused.**

**Pattern:** 1D DP — dp[i]=True if s[0..i-1] breakable; try all j < i where dp[j]=True and s[j:i] in wordSet

```
s="leetcode", wordDict={"leet","code"}

 i: 0  1  2  3  4  5  6  7  8
dp: T  F  F  F  T  F  F  F  T
              ↑               ↑
    j=0,dp[0]=T,s[0:4]="leet"✓  j=4,dp[4]=T,s[4:8]="code"✓

Answer: dp[8] = True
```

**The trick:** "dp[0]=True; for each i, if dp[j]=True and s[j:i] in wordSet → dp[i]=True."

```python
word_set = set(wordDict)
dp = [False] * (n + 1)
dp[0] = True
for i in range(1, n + 1):
    for j in range(i):
        if dp[j] and s[j:i] in word_set:
            dp[i] = True; break
return dp[n]
```

**Complexity:** Time O(n^2) | Space O(n)

**Trie optimization:** Build trie from wordDict; for each i where dp[i]=True, traverse trie from i; at each is_end, set dp[i+len]=True. O(n * max_word_len).

**Same pattern solves:** Word Break II (LC 140), Concatenated Words (LC 472), Palindrome Partitioning II (LC 132), Decode Ways (LC 91), Coin Change (LC 322).
