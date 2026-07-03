# Q1 — Implement Trie (2-min card) — LC 208

**Prefix tree: insert, search (exact word), startsWith (prefix check).**

**Pattern:** Each node has children dict + is_end flag. Navigate char by char.

```
Insert "app", "apple":
root → a → p → p [is_end=True]
                └─ l → e [is_end=True]

search("app")       → True  (is_end=True at last char)
search("ap")        → False (is_end=False)
startsWith("app")   → True  (path exists regardless of is_end)
startsWith("xyz")   → False (path doesn't exist)
```

**The trick:** "Insert: create missing nodes + set is_end. Search: traverse + check is_end. StartsWith: traverse + return True if path exists."

```python
class TrieNode:
    def __init__(self): self.children = {}; self.is_end = False

class Trie:
    def __init__(self): self.root = TrieNode()
    def insert(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children: node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_end = True
    def search(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children: return False
            node = node.children[ch]
        return node.is_end
    def startsWith(self, prefix):
        node = self.root
        for ch in prefix:
            if ch not in node.children: return False
            node = node.children[ch]
        return True
```

**Complexity:** insert/search/startsWith all O(k) where k = word length

**Same pattern solves:** Design Search Words (LC 211), Word Search II (LC 212), Replace Words (LC 648), Max XOR (LC 421), Longest Word Dict (LC 720), Count Distinct Substrings (GFG).
