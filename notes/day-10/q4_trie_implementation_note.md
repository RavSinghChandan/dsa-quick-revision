# ⚡ Quick Note · Day 10 — Math, Bit & Trie · Q4 · Trie Implementation
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Prefix tree: insert, search, startsWith."

## Pattern
`Prefix tree — node per char, is_end flag`

## Visual in your head
```
root → a → p → p(is_end) → l → e(is_end)
insert: walk/create nodes, flag last is_end
search: walk + check is_end
prefix: walk exists
```

## The trick (say it out loud)
> "Char-by-char tree. is_end marks a complete word. All ops O(word length)."

## Code skeleton
```python
def insert(w):
    node=root
    for ch in w: node=node.children.setdefault(ch,TrieNode())
    node.is_end=True
```

## Complexity
- insert/search/prefix O(L) · Space O(total chars)

## This trick solves more
Implement Trie (208) · Wildcard Search (211) · Word Search II (212) · Replace Words (648) · Max XOR (bit trie)
