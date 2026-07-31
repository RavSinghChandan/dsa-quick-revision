# ⚡ Quick Note · Day 10 — Math, Bit & Trie · Q5 · Trie Advanced (Wildcard)
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Trie search where '.' matches any single character."

## Pattern
`Trie + DFS backtracking on '.'`

## Visual in your head
```
dfs(node, i):
  i==len → node.is_end
  '.' → any(dfs(child, i+1) for child)
  else → ch in children and dfs(child, i+1)
```

## The trick (say it out loud)
> "Letter follows one child; '.' forks into every child. Match ends on is_end."

## Code skeleton
```python
if ch=='.': return any(dfs(c,i+1) for c in node.children.values())
return ch in node.children and dfs(node.children[ch], i+1)
```

## Complexity
- add O(L) · search O(L) typical, O(26^dots·L) worst

## This trick solves more
Add & Search Word (211) · Word Search II (212) · Autocomplete (642) · Stream of Chars (1032)
