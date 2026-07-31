# Day 10 — Math, Bit & Trie · Q4 · Trie Implementation and Operations

---

## What is this question actually asking?

Implement a **Trie** (prefix tree) supporting `insert(word)`, `search(word)`, and `startsWith(prefix)`.

```
insert("apple"); search("apple")→True; search("app")→False; startsWith("app")→True
```

> The foundational string data structure — each node has up to 26 children (one per letter) and a flag marking a complete word. Powers autocomplete, spell-check, and word-search puzzles.

---

## Pattern

```
PATTERN: Prefix Tree — node per character, is_end flag for complete words
```

Whenever you see: *"prefix search / autocomplete / many-word lookups / word dictionary"* → a Trie stores words character by character down a tree. Insert/search/prefix all take O(word length), independent of how many words are stored.

---

## Understand with a diagram

```
insert "app", "apple":

root
 └ a
   └ p
     └ p (is_end=True: "app")
       └ l
         └ e (is_end=True: "apple")

search("app")  → walk a→p→p, node.is_end → True
search("ap")   → walk a→p, is_end False → False
startsWith("ap") → walk a→p exists → True
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — list of words + linear scan — O(N·L) per query
Store all words; scan for prefix matches.

### 🟢 Optimal — Trie — O(L) per operation
Each op walks L characters; shared prefixes are stored once. Fast for many queries.

---

## Pseudocode (Optimal)

```
class TrieNode:
    children = map char → TrieNode
    is_end = False

class Trie:
    insert(word):
        node = root
        for ch in word:
            node = node.children.setdefault(ch, TrieNode())
        node.is_end = True

    search(word):    return _find(word) and node.is_end
    startsWith(pre): return _find(pre) is not None
```

---

## Python Code

```python
class TrieNode:
    def __init__(self):
        self.children = {}          # char -> TrieNode
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        node = self.root
        for ch in word:
            node = node.children.setdefault(ch, TrieNode())
        node.is_end = True

    def _walk(self, s: str):
        node = self.root
        for ch in s:
            if ch not in node.children:
                return None
            node = node.children[ch]
        return node

    def search(self, word: str) -> bool:
        node = self._walk(word)
        return node is not None and node.is_end

    def starts_with(self, prefix: str) -> bool:
        return self._walk(prefix) is not None


# Test
t = Trie()
t.insert("apple")
print(t.search("apple"))       # True
print(t.search("app"))         # False
print(t.starts_with("app"))    # True
t.insert("app")
print(t.search("app"))         # True
```

---

## Complexity

| | Time | Space |
|---|---|---|
| insert / search / prefix | O(L) | O(total chars) |

---

## Edge Cases to remember

- **`is_end` flag distinguishes a word from a prefix** — "app" is a prefix of "apple" but only a *word* if inserted and flagged.
- **`setdefault`** cleanly creates-or-gets a child node.
- **Empty string** — inserting "" marks the root as `is_end`; decide if allowed.
- **Deletion** (advanced) needs to unmark `is_end` and prune childless nodes.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Implement Trie (LC 208) | This |
| 2 | Add and Search Word — wildcard (LC 211) | DFS on `.` |
| 3 | Word Search II (LC 212) | Trie + grid DFS |
| 4 | Replace Words (LC 648) | Trie prefix lookup |
| 5 | Longest Word All Prefixes (Q6) | Trie BFS/DFS |
| 6 | Autocomplete system (LC 642) | Trie + counts |
| 7 | Maximum XOR (Q8) | Bit trie |
| 8 | Prefix/suffix search | Trie |

---

## The ONE trick to remember

```
"CHARACTER-BY-CHARACTER TREE; is_end MARKS A COMPLETE WORD. ALL OPS O(L)."
```

Each node holds child pointers per letter. Insert walks/creates nodes and flags the last; search walks and checks `is_end`; prefix just checks the path exists. Every operation is O(word length), regardless of dictionary size.

> Memory hook: "Words share their common prefixes down one tree; a flag says 'a word ends here.'"

---
