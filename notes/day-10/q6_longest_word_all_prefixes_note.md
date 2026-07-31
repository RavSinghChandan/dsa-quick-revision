# ⚡ Quick Note · Day 10 — Math, Bit & Trie · Q6 · Longest Word All Prefixes
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Longest word whose every prefix is also a word (tie → smallest)."

## Pattern
`Trie + DFS through is_end nodes only`

## Visual in your head
```
insert all words
DFS from root, sorted children:
  descend only if child.is_end
  track longest path (tie → lexicographically smaller)
```

## The trick (say it out loud)
> "DFS the Trie only through is_end children. Deepest path = answer. Sorted children for tie-break."

## Code skeleton
```python
for ch in sorted(node.children):
    child=node.children[ch]
    if child.is_end: dfs(child, path+ch)
```

## Complexity
- Trie DFS O(total chars) · Space O(total chars)

## This trick solves more
Longest Word in Dict (720) · Word Break (139) · Concatenated Words (472) · Replace Words (648)
