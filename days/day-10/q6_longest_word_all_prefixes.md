# Day 10 — Math, Bit & Trie · Q6 · Longest Word With All Prefixes

---

## What is this question actually asking?

Find the **longest word** in a dictionary such that **every prefix of it** is also a word in the dictionary. Tie → lexicographically smallest.

```
["a","banana","app","appl","ap","apply","apple"]
→ "apple"   (a, ap, app, appl, apple all present; "apply" also qualifies but "apple" < "apply")
```

> A Trie problem: a word qualifies if every node on its path is marked `is_end`. DFS the Trie, only descending through complete-word nodes.

---

## Pattern

```
PATTERN: Trie + DFS through is_end nodes only
```

Whenever you see: *"word buildable one letter at a time from dictionary words / all prefixes present"* → insert all words into a Trie; DFS from the root, but only recurse into a child if that child is itself a complete word. Track the best (longest, then smallest).

---

## Understand with a diagram

```
Insert all words. DFS from root:
   for each child in sorted order (for lexicographic tie-break):
       if child.is_end:              ← this prefix is a valid word
           update best with current path
           recurse deeper
       else: prune (can't extend — a prefix is missing)

Only paths where EVERY step is a complete word are explored.
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — for each word check all prefixes in a set — O(N·L)
Put words in a set; for each word verify every prefix is present. Simple and often fine.

### 🟢 Optimal — Trie DFS — O(total chars)
Build once; DFS only through `is_end` nodes; the deepest reachable path is the answer.

---

## Pseudocode (Optimal)

```
function longest_word(words):
    build Trie of all words
    best = ""
    dfs(node, path):
        # update best (longer wins; tie → lexicographically smaller)
        if len(path) > len(best) or (len(path)==len(best) and path < best):
            best = path
        for ch in sorted(node.children):
            child = node.children[ch]
            if child.is_end:              ← only extend through complete words
                dfs(child, path + ch)
    dfs(root, "")
    return best
```

---

## Python Code

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

def longest_word_all_prefixes(words: list[str]) -> str:
    root = TrieNode()
    for w in words:
        node = root
        for ch in w:
            node = node.children.setdefault(ch, TrieNode())
        node.is_end = True

    best = ""
    def dfs(node, path):
        nonlocal best
        if len(path) > len(best) or (len(path) == len(best) and path < best):
            best = path
        for ch in sorted(node.children):        # sorted → lexicographic tie-break
            child = node.children[ch]
            if child.is_end:                    # only descend through valid words
                dfs(child, path + ch)
    dfs(root, "")
    return best


# Test
print(longest_word_all_prefixes(
    ["a","banana","app","appl","ap","apply","apple"]))   # "apple"
print(longest_word_all_prefixes(["w","wo","wor","worl","world"]))  # "world"
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Set of prefixes | O(N·L) | O(N·L) |
| Trie DFS | O(total chars) | O(total chars) |

---

## Edge Cases to remember

- **Every prefix must be a word** — descend only into `is_end` children; a missing prefix prunes the whole branch.
- **Tie-break: longest, then lexicographically smallest** — iterate children in **sorted** order and compare on ties.
- **Empty answer** — if no single-letter word exists, nothing qualifies → "".
- **`path < best` comparison** — standard string comparison gives lexicographic order.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Longest Word in Dictionary (LC 720) | This problem |
| 2 | Implement Trie (Q4) | Base structure |
| 3 | Word Break (LC 139) | Buildable from dictionary |
| 4 | Concatenated Words (LC 472) | Trie + DP |
| 5 | Replace Words (LC 648) | Shortest root prefix |
| 6 | Stream of Characters (LC 1032) | Trie |
| 7 | Autocomplete ranking | Trie DFS |
| 8 | Camelcase matching | Prefix logic |

---

## The ONE trick to remember

```
"DFS THE TRIE THROUGH is_end NODES ONLY; TRACK LONGEST (THEN SMALLEST)."
```

A word has all prefixes present iff every node on its Trie path is a complete word. So DFS from the root, descending only into `is_end` children (in sorted order for tie-breaking), and keep the deepest path found.

> Memory hook: "Climb only where each step is already a real word."

---
