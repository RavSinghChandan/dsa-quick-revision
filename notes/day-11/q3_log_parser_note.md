# ⚡ Quick Note · Day 11 · Q3 · Log Parser
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Read log lines, extract a field, count/group by it (errors, endpoints, per hour)."

---

## Pattern
`Streaming line scan + Counter (+ regex to split fields)`

---

## Visual in your head
```
for line in file:          ← lazy, O(1) memory
    ts, level, msg = parse(line)
    counts[level] += 1
    endpoints[ep] += 1
→ Counter.most_common(1) = top
```

---

## The trick (say it out loud)
> "Stream the file (for line in f), parse ONCE, aggregate with Counter. Skip bad lines."

---

## Code skeleton (3 lines to recall)
```python
for line in f:
    m = LOG_RE.match(line)
    if m: counts[m["level"]] += 1
```

---

## Complexity
- Time: O(n) lines · Space: O(distinct keys) — never the whole file

---

## This trick solves more questions
Top-K words · 404 per endpoint · Requests per hour · Most active user · Token frequency · Emails/IPs from text
