# Q5 — Count Distinct Substrings Using Trie (2-min card)

**Count unique non-empty substrings of s. Every substring = prefix of some suffix.**

**Pattern:** Insert all n suffixes into Trie. Each new TrieNode created = 1 new distinct substring.

```
s = "aab"   suffixes: "aab", "ab", "b"

Insert "aab": root→a(new)→a(new)→b(new)  → 3 new nodes
Insert "ab":  root→a(exists)→b(new)       → 1 new node
Insert "b":   root→b(new)                 → 1 new node

Total = 5 distinct substrings: "a","aa","aab","ab","b" ✓

s = "abc" → 6 (4*5/2 = 6, all distinct)
s = "aaa" → 3 ("a","aa","aaa")
```

**The trick:** "New node = new substring not seen before. Hit existing node = duplicate prefix. Count new nodes."

```python
class TrieNode:
    def __init__(self): self.children = {}

def countDistinctSubstrings(s):
    root = TrieNode(); count = 0
    for i in range(len(s)):
        node = root
        for j in range(i, len(s)):
            ch = s[j]
            if ch not in node.children:
                node.children[ch] = TrieNode()
                count += 1          # new unique substring!
            node = node.children[ch]
    return count
```

**Complexity:** Time O(n^2) | Space O(n^2)

**Brute force:** set of all s[i:j] substrings — O(n^3) time, O(n^2) space.
**Optimal:** Suffix Automaton — O(n) time and space (complex to implement).

**Same pattern solves:** Implement Trie (LC 208), Count Distinct Substrings in Grid (GFG), Word Search II (LC 212), Replace Words (LC 648).
