# Day 11 · Q3 · Log Parser (parse lines, aggregate, find top errors)

---

## What is this question actually asking?

You are given server / model logs as raw text lines. Parse each line into fields (timestamp, level, message), then answer questions like:
- How many `ERROR` lines are there?
- Which endpoint / message appears most?
- Count events per hour.

```
Input lines:
  2026-07-31 10:15:02 INFO  request /predict ok
  2026-07-31 10:15:03 ERROR /predict timeout
  2026-07-31 10:16:01 ERROR /embed  oom

Output: {"ERROR": 2, "INFO": 1}  ·  top endpoint: /predict
```

> Real AI use: debugging training runs, counting API failures, extracting latency from inference logs. Every ML infra interview has a version of this.

---

## Pattern

```
PATTERN: Streaming line scan + Hash Map counting  (+ regex to split fields)
```

Whenever you see: *"read lines, pull out a field, count/group by it"* → think **loop the lines, `.split()` or `re` to extract, `Counter`/`dict` to aggregate.** Never load the whole file into a list if it's huge — stream it.

---

## Understand with a diagram

```
FILE (streamed line by line — O(1) memory per line):

  line ──▶ parse ──▶ (ts, level, msg) ──▶ counts[level] += 1
                                     └──▶ endpoints[ep] += 1

After all lines:
  counts    = {INFO:1, ERROR:2}
  endpoints = {/predict:2, /embed:1}   →  max() → top endpoint
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — read all, scan repeatedly

```
lines = file.read().splitlines()      ← loads WHOLE file into memory
error_count = 0
for l in lines: if "ERROR" in l: error_count += 1   ← one pass per metric
for l in lines: ... top endpoint ...                 ← another full pass
```

**Why bad?** Loads a possibly-huge file into RAM, and re-scans it once per metric.

---

### 🟢 Optimal — Single streaming pass + Counter — O(n)

**Core insight:**
1. Iterate the file **line by line** (a file object is already a lazy iterator → constant memory).
2. Parse each line **once** — `split(maxsplit=...)` or a regex.
3. Update **all** aggregates in that single pass using `collections.Counter`.

```
for line in file:            ← lazy, one line in memory at a time
    ts, level, rest = parse(line)
    level_counts[level] += 1
    endpoint_counts[endpoint_of(rest)] += 1
# one pass, all metrics ready
```

---

## Pseudocode (Optimal)

```
function parse_logs(file):
    level_counts = Counter()
    endpoint_counts = Counter()
    for line in file:                 ← stream, don't load all
        parts = split line into (timestamp, level, message)
        if line is malformed: skip (or count as bad)
        level_counts[level] += 1
        endpoint = extract endpoint from message
        endpoint_counts[endpoint] += 1
    return level_counts, endpoint_counts.most_common(1)
```

---

## Python Code

```python
import re
from collections import Counter

LOG_RE = re.compile(r"^(?P<ts>\S+ \S+)\s+(?P<level>\w+)\s+(?P<msg>.*)$")

def parse_logs(path: str):
    level_counts = Counter()
    endpoint_counts = Counter()
    bad = 0

    with open(path) as f:
        for line in f:                       # streamed, O(1) memory per line
            line = line.strip()
            if not line:
                continue
            m = LOG_RE.match(line)
            if not m:                        # malformed line
                bad += 1
                continue
            level_counts[m["level"]] += 1
            # first token in message that looks like an endpoint
            for tok in m["msg"].split():
                if tok.startswith("/"):
                    endpoint_counts[tok] += 1
                    break

    top_endpoint = endpoint_counts.most_common(1)  # [('/predict', 2)]
    return dict(level_counts), top_endpoint, bad


# Test with in-memory lines
lines = [
    "2026-07-31 10:15:02 INFO  request /predict ok",
    "2026-07-31 10:15:03 ERROR /predict timeout",
    "2026-07-31 10:16:01 ERROR /embed oom",
]
lc = Counter()
ec = Counter()
for line in lines:                            # same logic, no file
    m = LOG_RE.match(line)
    lc[m["level"]] += 1
    for tok in m["msg"].split():
        if tok.startswith("/"):
            ec[tok] += 1; break
print(dict(lc))            # {'INFO': 1, 'ERROR': 2}
print(ec.most_common(1))   # [('/predict', 2)]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Streaming + Counter | O(n) lines | O(k) distinct keys (NOT O(n) — never holds the whole file) |

---

## Edge Cases to remember

- **Malformed lines** (missing fields, partial writes) → skip and count, don't crash. Interviewers love this.
- **Huge file** → MUST stream (`for line in f`), never `f.read()`.
- **Multi-line stack traces** → a single event can span lines; may need to join continuation lines.
- **Case sensitivity** of levels (`Error` vs `ERROR`) → normalize with `.upper()`.
- **`most_common` ties** → order among equal counts is insertion order; mention if determinism matters.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Top K frequent words in a file | Counter + heap |
| 2 | Count 404s per endpoint from access log | Group-by counting |
| 3 | Requests per hour / rate over time | Bucket by timestamp |
| 4 | Find the most active user | Counter on user field |
| 5 | Parse CSV without pandas | split + aggregate |
| 6 | Extract all emails/IPs from text | regex scan |
| 7 | Token frequency counter (Q4) | Same aggregation |
| 8 | Detect error spikes / anomalies | Count per window |

---

## The ONE trick to remember

```
"STREAM THE LINES, PARSE ONCE, COUNT WITH A Counter"
```

Two habits win this: **iterate the file object directly** (lazy → O(1) memory) and **`collections.Counter`** for aggregation. Regex is only for pulling fields out of one line — the counting is a plain hash map.

> Memory hook: "One pass, one line at a time, one Counter."

---
