# ⚡ Quick Note · Day 12 · Q12 · Load & Validate JSONL [JSON]
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Read JSONL (one object per line), skip bad/invalid rows, keep the good."

## Pattern
`Stream lines + per-line json.loads in try/except + schema check`

## Visual in your head
```
for line in f:
  try: rec=json.loads(line)
  except JSONDecodeError: bad+=1; continue
  if valid(rec): keep else bad+=1
```

## The trick (say it out loud)
> "JSONL ≠ JSON — parse EACH line in try/except. Skip+count bad rows, validate the shape."

## Code skeleton
```python
for line in f:
    try: rec=json.loads(line.strip())
    except json.JSONDecodeError: bad+=1; continue
    if all(k in rec for k in required): good.append(rec)
```

## Complexity
- Time O(n lines) · Space O(kept) (yield → O(1))

## This trick solves more
Load fine-tune dataset · Parse CSV skip-bad · Validate API payloads · Stream+filter · Log parser · Dedup records
