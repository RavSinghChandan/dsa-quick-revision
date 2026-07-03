# Q2 — Word Break (LeetCode 139)

## 1. What is this question actually asking?

Given a string `s` and a dictionary `wordDict`, return true if `s` can be segmented into a space-separated sequence of one or more dictionary words. Words in the dictionary can be reused. This is a classic DP problem asking: can we partition string s into valid dictionary words?

Example: s = "leetcode", wordDict = ["leet","code"] → True ("leet" + "code")
Example: s = "applepenapple", wordDict = ["apple","pen"] → True ("apple"+"pen"+"apple")
Example: s = "catsandog", wordDict = ["cats","dog","sand","and","cat"] → False

## 2. Pattern

1D Dynamic Programming — dp[i] = True if s[0..i-1] can be segmented using wordDict. Transition: dp[i] = True if any dp[j] is True and s[j..i-1] is in wordDict.

## 3. Understand with a Diagram

```
s = "leetcode"    wordDict = {"leet", "code"}

dp[i] = True means s[0..i-1] is breakable

dp[0] = True  (empty string is always breakable — base case)

i=1: check all j < 1:
  j=0: dp[0]=True, s[0:1]="l" ∈ dict? No → dp[1]=False

i=2: j=0: dp[0]=T, s[0:2]="le" ∈ dict? No
     j=1: dp[1]=F → skip
     dp[2] = False

i=3: similar, dp[3]=False

i=4: j=0: dp[0]=T, s[0:4]="leet" ∈ dict? YES → dp[4]=True

i=5: j=0: dp[0]=T, s[0:5]="leetc" ∈ dict? No
     j=4: dp[4]=T, s[4:5]="c" ∈ dict? No
     dp[5] = False

i=6: j=4: dp[4]=T, s[4:6]="co" ∈ dict? No → dp[6]=False

i=7: j=4: dp[4]=T, s[4:7]="cod" ∈ dict? No → dp[7]=False

i=8: j=0: dp[0]=T, s[0:8]="leetcode" ∈ dict? No
     j=4: dp[4]=T, s[4:8]="code" ∈ dict? YES → dp[8]=True

Answer: dp[8] = True ✓

Transition table:
 i: 0  1  2  3  4  5  6  7  8
dp: T  F  F  F  T  F  F  F  T
         ↑                   ↑
         leet matches here   code matches here
```

## 4. Brute Force → Better → Optimal

### Brute Force — Recursion without memoization
**Idea:** Try to split s at every position; recurse on remaining string.

O(2^n) — exponential due to recomputation of overlapping subproblems.

### Better — Recursion with memoization
**Idea:** Cache result for each starting index. O(n^2 * k) where k = average word length.

### Optimal — Bottom-up DP
**Idea:** dp[i] = True if s[0..i-1] is breakable. Iterate i from 1 to n, check all j from 0 to i-1.

O(n^2) time (or O(n * max_word_len) with optimization), O(n) space.

### With Trie optimization
**Idea:** Insert wordDict into a Trie. For each position i, traverse Trie from i; at each is_end node, mark dp[i + len] = True. Avoids checking all j; processes common prefixes once.

O(n * max_word_len) time.

## 5. Pseudocode (Optimal)

```
function wordBreak(s, wordDict):
    n = len(s)
    word_set = set(wordDict)
    dp = [False] * (n + 1)
    dp[0] = True
    
    for i from 1 to n:
        for j from 0 to i-1:
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break   // no need to check more j values
    
    return dp[n]
```

## 6. Python Code

```python
def wordBreak(s, wordDict):
    """
    Return True if s can be segmented into words from wordDict.
    O(n^2) DP with set lookup.
    """
    word_set = set(wordDict)
    n = len(s)
    dp = [False] * (n + 1)
    dp[0] = True
    
    for i in range(1, n + 1):
        for j in range(i):
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break
    
    return dp[n]


def wordBreak_trie(s, wordDict):
    """Trie-based approach for efficiency with many words."""
    # Build Trie
    trie = {}
    for word in wordDict:
        node = trie
        for ch in word:
            node = node.setdefault(ch, {})
        node['#'] = True   # end marker
    
    n = len(s)
    dp = [False] * (n + 1)
    dp[0] = True
    
    for i in range(n):
        if not dp[i]:
            continue
        node = trie
        for j in range(i, n):
            ch = s[j]
            if ch not in node:
                break
            node = node[ch]
            if '#' in node:
                dp[j + 1] = True
    
    return dp[n]


def wordBreak_II(s, wordDict):
    """Return all valid segmentations (Word Break II — LC 140)."""
    word_set = set(wordDict)
    from functools import lru_cache
    
    @lru_cache(maxsize=None)
    def backtrack(start):
        if start == len(s):
            return [[]]
        results = []
        for end in range(start + 1, len(s) + 1):
            word = s[start:end]
            if word in word_set:
                for rest in backtrack(end):
                    results.append([word] + rest)
        return results
    
    return [' '.join(parts) for parts in backtrack(0)]


# Test 1 — LeetCode example 1
print(wordBreak("leetcode", ["leet", "code"]))               # True

# Test 2 — LeetCode example 2
print(wordBreak("applepenapple", ["apple", "pen"]))          # True

# Test 3 — LeetCode example 3
print(wordBreak("catsandog", ["cats","dog","sand","and","cat"]))  # False

# Test 4 — single character
print(wordBreak("a", ["a"]))                                  # True

# Test 5 — Trie version
print(wordBreak_trie("leetcode", ["leet", "code"]))          # True

# Test 6 — Word Break II
print(wordBreak_II("catsanddog", ["cat","cats","and","sand","dog"]))
# ["cat sand dog", "cats and dog"]
```

## 7. Complexity Table

| Approach            | Time              | Space   | Notes                           |
|---------------------|-------------------|---------|---------------------------------|
| Brute Recursion     | O(2^n)            | O(n)    | Exponential                     |
| Memo Recursion      | O(n^2 * k)        | O(n)    | k = avg word len                |
| Bottom-up DP        | O(n^2)            | O(n)    | Optimal for typical inputs      |
| Trie + DP           | O(n * max_len)    | O(n+W)  | W = total chars in wordDict     |

## 8. Edge Cases to Remember

- Empty string — dp[0]=True, return True
- Word not achievable even with all words — return False (like "catsandog")
- Single-letter words — still work with DP
- Same word reused — allowed (e.g., "appleapple" with ["apple"])
- Break early with `break` once dp[i] = True (don't keep checking j)
- Word Break II (LC 140) — enumerate all sentences; use backtracking with memoization

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Word Break II                         | Return all valid sentences (backtracking + memo)    | LC 140     |
| Concatenated Words                    | Word Break on each word using others                | LC 472     |
| Palindrome Partitioning               | DP to check if substrings are palindromes           | LC 131     |
| Palindrome Partitioning II            | DP for min cuts                                     | LC 132     |
| Decode Ways                           | DP on string segmentation                           | LC 91      |
| Integer Break                         | DP for splitting integers                           | LC 343     |
| Coin Change                           | DP analogy — coins = words, amount = string length  | LC 322     |
| Perfect Squares                       | DP — squares as "words", n as string length         | LC 279     |

## 10. The ONE Trick to Remember

**"dp[0]=True (empty breakable); for each i, if any dp[j]=True and s[j:i] in wordSet → dp[i]=True."**

The insight: dp[i] represents "can I reach position i?" If I can reach position j (dp[j]=True) and the substring from j to i is a valid word, then I can reach position i. The base case dp[0]=True seeds the entire process — it means "the empty prefix is trivially segmentable." Scan all j < i for each i; once dp[i] is set True, break early.
