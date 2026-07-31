# ⚡ Quick Note · Day 12 · Q11 · Safe Nested JSON Access [JSON]
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Get value at 'a.b.c' from nested JSON, default if any key missing (no crash)."

## Pattern
`Split path + iterative safe descent with a default`

## Visual in your head
```
cur=data
for key in path.split("."):
  isinstance(cur,dict) and key in cur? → cur=cur[key]
  else → return default
```

## The trick (say it out loud)
> "Split on '.', walk key by key, bail to default on the first miss. Never chain [a][b][c]."

## Code skeleton
```python
cur=data
for key in path.split("."):
    if isinstance(cur,dict) and key in cur: cur=cur[key]
    else: return default
return cur
```

## Complexity
- Time O(depth) · Space O(1)

## This trick solves more
Flatten dict · Deep-set by path · Config resolver · Extract LLM JSON fields · glom/pydash.get · Deep-merge
