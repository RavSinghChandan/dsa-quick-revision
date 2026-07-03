# Q5 — Count Distinct Substrings Using Trie

## 1. What is this question actually asking?

Given a string s, count the number of distinct (unique) substrings of s, including the empty string (or not, depending on variant). A substring is a contiguous sequence of characters within the string.

For s = "abc": substrings = {"", "a", "b", "c", "ab", "bc", "abc"} → 7 (or 6 excluding empty).
For s = "aab": substrings = {"", "a", "aa", "aab", "ab", "b"} → 6 (or 5 excluding empty).

We use a Trie to count distinct substrings by inserting all suffixes — each new node created = 1 new distinct substring.

## 2. Pattern

Trie — insert all suffixes of s. Each new TrieNode created represents exactly one new distinct substring (the path from root to that node). Total new nodes = count of distinct non-empty substrings.

## 3. Understand with a Diagram

```
s = "abc"   (3 chars, so suffixes: "abc", "bc", "c")

Insert suffix "abc":
  root → a (new) → b (new) → c (new)
  new nodes: 3

Insert suffix "bc":
  root → b (new) → c (new)
  new nodes: 2

Insert suffix "c":
  root → c (new)
  new nodes: 1

Total new nodes = 3+2+1 = 6 = count of distinct non-empty substrings ✓
("a","ab","abc","b","bc","c")

s = "aab"   (suffixes: "aab", "ab", "b")

Insert "aab":
  root → a → a → b
  new nodes: 3

Insert "ab":
  root → a (exists!) → b (new)
  new nodes: 1

Insert "b":
  root → b (new)
  new nodes: 1

Total = 3+1+1 = 5 distinct non-empty substrings ✓
("a","aa","aab","ab","b")

Why does this work?
Each unique path from root to any node = one unique substring
(the characters along the path spell out that substring)
Creating a new node = finding a substring we haven't seen before.

If a node already exists, that prefix (and thus that substring segment) 
was already counted when we inserted a previous suffix.

Total distinct substrings (formula): n*(n+1)/2 - duplicates
Or just: count new nodes during suffix insertion.

For "aab": n*(n+1)/2 = 3*4/2 = 6 total substrings, 1 duplicate ("a" appears in both "aab" and "ab"), so 6-1=5 distinct.
```

## 4. Brute Force → Better → Optimal

### Brute Force — HashSet O(n^2 * k)
**Idea:** Generate all n*(n+1)/2 substrings, insert into a set. Return set size.

```
seen = set()
for i from 0 to n-1:
    for j from i+1 to n:
        seen.add(s[i:j])
return len(seen)
```

O(n^2) substrings × O(k) for hashing = O(n^3) or O(n^2) with rolling hash. Space O(n^2).

### Better — Suffix Array + LCP
**Idea:** Sort all suffixes, compute LCP (Longest Common Prefix) array. Answer = n*(n+1)/2 - sum(LCP).

O(n log n) or O(n) for suffix array construction + O(n) for LCP.

### Optimal for this pattern — Trie
**Idea:** Insert all suffixes into a Trie. Count new nodes created = distinct non-empty substrings.

O(n^2) time and space (n suffixes, each up to length n). The Trie approach is O(n^2) but is the clearest and most interview-friendly way to think about it.

## 5. Pseudocode (Trie)

```
function countDistinctSubstrings(s):
    root = TrieNode()
    count = 0
    
    // Insert each suffix
    for i from 0 to len(s)-1:
        node = root
        for j from i to len(s)-1:
            ch = s[j]
            if ch not in node.children:
                node.children[ch] = TrieNode()
                count += 1   // new node = new distinct substring
            node = node.children[ch]
    
    return count   // distinct non-empty substrings
    // return count + 1  // if including empty string
```

## 6. Python Code

```python
class TrieNode:
    def __init__(self):
        self.children = {}


def countDistinctSubstrings(s):
    """
    Count distinct non-empty substrings of s using Trie.
    Insert all suffixes; each new TrieNode = one new distinct substring.
    """
    root = TrieNode()
    count = 0
    
    n = len(s)
    for i in range(n):          # Start of each suffix
        node = root
        for j in range(i, n):   # Extend suffix character by character
            ch = s[j]
            if ch not in node.children:
                node.children[ch] = TrieNode()
                count += 1       # New node = new unique substring
            node = node.children[ch]
    
    return count


def countDistinctSubstringsHashSet(s):
    """Brute force: HashSet approach for verification."""
    seen = set()
    n = len(s)
    for i in range(n):
        for j in range(i + 1, n + 1):
            seen.add(s[i:j])
    return len(seen)


def countDistinctSubstringsWithEmpty(s):
    """Include empty string in count."""
    return countDistinctSubstrings(s) + 1


def longestDistinctSubstring(s):
    """
    Related: find the length of longest distinct-character substring.
    (Different problem — uses sliding window, not trie)
    """
    char_idx = {}
    left = 0
    max_len = 0
    for right, ch in enumerate(s):
        if ch in char_idx and char_idx[ch] >= left:
            left = char_idx[ch] + 1
        char_idx[ch] = right
        max_len = max(max_len, right - left + 1)
    return max_len


# Test 1
s = "abc"
print(countDistinctSubstrings(s))       # 6
print(countDistinctSubstringsHashSet(s)) # 6

# Test 2
s = "aab"
print(countDistinctSubstrings(s))       # 5
print(countDistinctSubstringsHashSet(s)) # 5

# Test 3 — single char
print(countDistinctSubstrings("a"))     # 1

# Test 4 — all same chars
s = "aaa"
print(countDistinctSubstrings(s))       # 3 ("a","aa","aaa")
print(countDistinctSubstringsHashSet(s)) # 3

# Test 5 — longer string
s = "abcd"
print(countDistinctSubstrings(s))       # 10 = 4*5/2 (all distinct)

# Test 6 — with repetition
s = "abab"
print(countDistinctSubstrings(s))       # 7 ("a","ab","aba","abab","b","ba","bab")
print(countDistinctSubstringsHashSet(s)) # 7
```

## 7. Complexity Table

| Approach         | Time       | Space      | Notes                              |
|------------------|------------|------------|------------------------------------|
| HashSet          | O(n^3)     | O(n^2)     | Simple, slow                       |
| Suffix Array+LCP | O(n log n) | O(n)       | n*(n+1)/2 - sum(LCP)               |
| Trie (this)      | O(n^2)     | O(n^2)     | Clear, interview-friendly          |
| Suffix Automaton | O(n)       | O(n)       | Optimal but complex                |

## 8. Edge Cases to Remember

- Empty string — return 0 (no non-empty substrings)
- Single character — return 1
- All same characters "aaa" — k distinct: "a","aa","aaa"... → n distinct (since each longer run is unique)
- All distinct characters "abcd" — n*(n+1)/2 distinct substrings (no duplicates possible)
- Formula verification: Trie count should equal n*(n+1)/2 - duplicates
- The Trie node count equals the number of distinct non-empty substrings (each path = one substring)

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Implement Trie                        | Foundation data structure                           | LC 208     |
| Longest Substring Without Repeating   | Distinct characters, sliding window                 | LC 3       |
| Number of Distinct Substrings in Grid | 2D extension                                        | GFG        |
| Distinct Subsequences                 | Count distinct subsequences (DP, not substrings)    | LC 115     |
| Count Unique Characters               | Contribution counting on substrings                 | LC 828     |
| Count Substrings with K Distinct      | Sliding window with at-most-k trick                 | LC 992     |
| Palindromic Substrings                | Count palindromic substrings                        | LC 647     |
| Minimum Window Substring              | String search problem                               | LC 76      |

## 10. The ONE Trick to Remember

**"Insert all n suffixes into Trie. Each new TrieNode created = exactly one new distinct substring. Count of new nodes = answer."**

Why does this work? Every substring of s is a prefix of some suffix of s. When you insert suffix s[i..], you traverse character by character; each new node you create represents the substring s[i..j] for some j — a substring you haven't seen before (otherwise that node would already exist). If you hit an existing node, that prefix was seen in a previous suffix, so it's not new. This is the elegant connection: new Trie nodes ↔ new distinct substrings.
