# Day 10 — Math, Bit & Trie · Q5 · Trie Advanced Operations (Wildcard Search)

---

## What is this question actually asking?

Extend a Trie to support search where `.` matches **any single character** (a `WordDictionary`).

```
addWord("bad"); addWord("dad"); addWord("mad")
search("pad")→False ; search("bad")→True ; search(".ad")→True ; search("b..")→True
```

> Adds a **DFS with branching** on top of a normal Trie: a `.` forks the search into all children.

---

## Pattern

```
PATTERN: Trie + DFS backtracking on the wildcard '.'
```

Whenever you see: *"search with wildcards / pattern matching over a dictionary"* → store words in a Trie; for a normal char, descend one child; for `.`, recursively try **every** child. Match succeeds if a path ends at an `is_end` node.

---

## Understand with a diagram

```
search(word, node, i):
    if i == len(word): return node.is_end
    ch = word[i]
    if ch == '.':
        for child in node.children.values():
            if search(word, child, i+1): return True   ← try all
        return False
    else:
        return ch in node.children and search(word, node.children[ch], i+1)
```

A `.` branches into every child; a letter follows a single edge.

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — store words, regex/scan each — O(N·L) per query
Match the pattern against every stored word.

### 🟢 Optimal — Trie + DFS — O(L) typical, O(26^k · L) worst with k dots
Normal chars are O(1) descents; each `.` multiplies branching. Shared prefixes keep most queries fast.

---

## Pseudocode (Optimal)

```
class WordDictionary:
    addWord(word): insert into Trie (like Q4)

    search(word):
        return dfs(root, 0)

    dfs(node, i):
        if i == len(word): return node.is_end
        ch = word[i]
        if ch == '.':
            return any(dfs(child, i+1) for child in node.children.values())
        return ch in node.children and dfs(node.children[ch], i+1)
```

---

## Python Code

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class WordDictionary:
    def __init__(self):
        self.root = TrieNode()

    def addWord(self, word: str) -> None:
        node = self.root
        for ch in word:
            node = node.children.setdefault(ch, TrieNode())
        node.is_end = True

    def search(self, word: str) -> bool:
        def dfs(node, i):
            if i == len(word):
                return node.is_end
            ch = word[i]
            if ch == '.':
                return any(dfs(child, i + 1)          # wildcard → try all children
                           for child in node.children.values())
            return ch in node.children and dfs(node.children[ch], i + 1)
        return dfs(self.root, 0)


# Test
wd = WordDictionary()
for w in ["bad", "dad", "mad"]:
    wd.addWord(w)
print(wd.search("pad"))   # False
print(wd.search("bad"))   # True
print(wd.search(".ad"))   # True
print(wd.search("b.."))   # True
```

---

## Complexity

| | Time | Space |
|---|---|---|
| addWord | O(L) | O(total chars) |
| search | O(L) typical, O(26^k·L) worst (k dots) | O(L) recursion |

---

## Edge Cases to remember

- **`.` branches to ALL children** — the DFS must try each and succeed if any path matches.
- **Match ends at `is_end`** — reaching the end of the pattern isn't enough; the node must mark a complete word.
- **Leading/trailing dots** — handled naturally by the recursion.
- **Worst case with many dots** — exponential branching; acceptable for interview-sized inputs.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Add and Search Word (LC 211) | This problem |
| 2 | Word Search II (LC 212) | Trie + grid DFS |
| 3 | Implement Trie (Q4) | Base |
| 4 | Regex/wildcard matching | DFS branching |
| 5 | Design Search Autocomplete (LC 642) | Trie DFS |
| 6 | Replace Words (LC 648) | Trie prefix |
| 7 | Stream of characters (LC 1032) | Suffix Trie |
| 8 | Concatenated words (LC 472) | Trie + DP |

---

## The ONE trick to remember

```
"NORMAL CHAR → ONE CHILD; '.' → DFS INTO EVERY CHILD; END ON is_end."
```

Build a plain Trie, then make `search` a DFS: a letter follows its single edge, a `.` recurses into all children. A pattern matches if some path reaches an `is_end` node exactly when the pattern is consumed.

> Memory hook: "A dot is a fork — try every branch; a letter is a single road."

---
