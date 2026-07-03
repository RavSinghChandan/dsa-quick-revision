# Q1 — Implement Trie (Prefix Tree) (LeetCode 208)

## 1. What is this question actually asking?

Implement a Trie data structure with three operations:
- `insert(word)`: Insert a word into the trie.
- `search(word)`: Return true if word is in the trie.
- `startsWith(prefix)`: Return true if any word in the trie starts with the given prefix.

A Trie (prefix tree) stores strings character-by-character, sharing common prefixes among words. Each node has up to 26 children (lowercase letters) and a flag marking end of a complete word.

## 2. Pattern

Tree data structure — each node holds children[26] and a boolean `is_end`. Navigate down character by character: insert creates nodes, search and startsWith traverse nodes.

## 3. Understand with a Diagram

```
Insert: "apple", "app", "application", "apt"

Root
 └─ a
     └─ p
         ├─ p [is_end=True]  ← "app"
         │   └─ l
         │       └─ e [is_end=True]  ← "apple"
         │           └─ i
         │               └─ c
         │                   └─ a
         │                       └─ t
         │                           └─ i
         │                               └─ o
         │                                   └─ n [is_end=True] ← "application"
         └─ t [is_end=True]  ← "apt"

Operations:
search("app")         → True  (node exists and is_end=True)
search("ap")          → False (node exists but is_end=False)
search("banana")      → False (no 'b' child at root)
startsWith("app")     → True  (can traverse "a→p→p")
startsWith("apt")     → True  (can traverse "a→p→t")
startsWith("xyz")     → False (no 'x' at root)

Node structure:
  children: dict or array of 26
  is_end:   bool

Insert "app":
  root → 'a' → create if missing
       → 'p' → create if missing
       → 'p' → create if missing, set is_end=True

Search "apple":
  root → 'a' (exists) → 'p' (exists) → 'p' (exists) → 'l' (exists) → 'e' (exists, is_end=True) → True
```

## 4. Brute Force → Better → Optimal

### Brute Force — Python set
**Idea:** Store all words in a set. Search is O(1) average. startsWith requires checking all words.

Search: O(1), startsWith: O(total_chars), insert: O(1). Not ideal for prefix queries.

### Better — Sorted list + binary search
**Idea:** Keep sorted list of words. Binary search for prefix queries.

Insert: O(n log n), search: O(log n * k), startsWith: O(log n * k). Space O(total_chars).

### Optimal — Trie
**Idea:** Each node = one character. Insert/search/startsWith all O(k) where k = word length. Prefix sharing saves space when many words share prefixes.

Insert: O(k), Search: O(k), StartsWith: O(k) — all proportional to key/prefix length.

## 5. Pseudocode (Optimal)

```
class TrieNode:
    children = {}  // or array[26]
    is_end = False

class Trie:
    root = TrieNode()
    
    function insert(word):
        node = root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True
    
    function search(word):
        node = root
        for char in word:
            if char not in node.children: return False
            node = node.children[char]
        return node.is_end
    
    function startsWith(prefix):
        node = root
        for char in prefix:
            if char not in node.children: return False
            node = node.children[char]
        return True
```

## 6. Python Code

```python
class TrieNode:
    def __init__(self):
        self.children = {}      # char → TrieNode
        self.is_end = False


class Trie:
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word: str) -> None:
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_end = True
    
    def search(self, word: str) -> bool:
        node = self.root
        for ch in word:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return node.is_end
    
    def startsWith(self, prefix: str) -> bool:
        node = self.root
        for ch in prefix:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return True


# Alternative: array-based children (faster, fixed 26)
class TrieNodeArray:
    def __init__(self):
        self.children = [None] * 26
        self.is_end = False


class TrieArray:
    def __init__(self):
        self.root = TrieNodeArray()
    
    def _idx(self, ch):
        return ord(ch) - ord('a')
    
    def insert(self, word: str) -> None:
        node = self.root
        for ch in word:
            i = self._idx(ch)
            if not node.children[i]:
                node.children[i] = TrieNodeArray()
            node = node.children[i]
        node.is_end = True
    
    def search(self, word: str) -> bool:
        node = self.root
        for ch in word:
            i = self._idx(ch)
            if not node.children[i]:
                return False
            node = node.children[i]
        return node.is_end
    
    def startsWith(self, prefix: str) -> bool:
        node = self.root
        for ch in prefix:
            i = self._idx(ch)
            if not node.children[i]:
                return False
            node = node.children[i]
        return True


# Test — LeetCode example
trie = Trie()
trie.insert("apple")
print(trie.search("apple"))       # True
print(trie.search("app"))         # False
print(trie.startsWith("app"))     # True
trie.insert("app")
print(trie.search("app"))         # True

# Test 2 — prefix not in trie
trie2 = Trie()
trie2.insert("apt")
trie2.insert("application")
print(trie2.startsWith("ap"))     # True
print(trie2.startsWith("xyz"))    # False
print(trie2.search("ap"))         # False (not inserted)
```

## 7. Complexity Table

| Operation    | Time  | Space                                    |
|--------------|-------|------------------------------------------|
| insert(word) | O(k)  | O(k * 26) worst case per word            |
| search(word) | O(k)  | O(1) extra                               |
| startsWith   | O(k)  | O(1) extra                               |

Total space: O(total_chars * 26) for array-based, O(total_chars) for dict-based (no empty children).

## 8. Edge Cases to Remember

- Empty string insert/search — traverse zero nodes, root.is_end matters (usually false)
- Prefix = full word — search returns True only if is_end=True; startsWith returns True always
- Word = prefix of another word — both "app" and "apple" work independently due to is_end flag
- All same words inserted — second insert just re-traverses, re-sets is_end=True (idempotent)
- Case sensitivity — lowercase only for LC 208; use dict for mixed case

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Design Add and Search Words           | Trie + DFS for wildcard '.' matching                | LC 211     |
| Word Search II                        | Trie + DFS on grid                                  | LC 212     |
| Replace Words                         | Trie to find shortest root prefix                   | LC 648     |
| Longest Word in Dictionary            | Trie BFS/DFS to find longest word built step-by-step| LC 720     |
| Maximum XOR of Two Numbers            | Binary Trie for XOR optimization                    | LC 421     |
| Palindrome Pairs                      | Trie for reverse lookup                             | LC 336     |
| Stream of Characters                  | Trie for multi-pattern matching                     | LC 1032    |
| Count Distinct Substrings             | Trie to count unique prefixes                       | GFG        |

## 10. The ONE Trick to Remember

**"Trie = one node per character. Insert: create missing nodes, set is_end at last char. Search: traverse, return is_end. StartsWith: traverse, return True if path exists."**

The is_end flag is what distinguishes search (full word must exist with is_end=True) from startsWith (only the path needs to exist). Both operations share the same traversal — just differ in what they return at the end. The two implementation choices are dict-based (flexible, space-efficient) vs array[26] (faster index arithmetic, wastes space on sparse characters).
