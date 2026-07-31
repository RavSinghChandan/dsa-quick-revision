# Day 12 · Q12 · Load & Validate JSONL Records (streaming)  ·  [JSON]

---

## What is this question actually asking?

A **JSONL** file has one JSON object per line (the standard format for ML datasets and LLM fine-tuning). Read it, **parse each line**, **skip/collect bad lines**, and keep only records that pass a validation rule.

```
line 1: {"id": 1, "text": "hi"}        ✓ valid
line 2: {"id": 2}                        ✗ missing "text" → skip
line 3: {bad json                        ✗ parse error → skip
line 4: {"id": 4, "text": "yo"}        ✓ valid
→ [ {id:1,text:hi}, {id:4,text:yo} ],  bad = 2
```

> Real AI use: loading training/eval datasets, cleaning scraped JSON, prepping fine-tune files. Tests: parse per line, handle malformed data **without crashing**, validate schema.

---

## Pattern

```
PATTERN: Stream lines + per-line json.loads in try/except + schema check
```

Whenever you see: *"load a JSONL / dataset file, some rows may be broken"* → iterate lines, `json.loads` each inside a `try/except`, apply a validation predicate, count what you drop. Never let one bad line kill the whole load.

---

## Understand with a diagram

```
for line in file:                    ← stream
    try: rec = json.loads(line)      ← parse ONE object
    except: bad += 1; continue       ← malformed → skip, keep going
    if valid(rec): keep it
    else:          bad += 1          ← wrong shape → skip
→ (good_records, bad_count)
```

One broken line is isolated — the rest still load.

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — parse the whole file as one JSON
`json.load(f)` expects ONE json value, not many lines → fails immediately on JSONL. And one bad line would abort everything.

### 🟢 Optimal — line-by-line parse with guards — O(n)
Stream lines, `json.loads` each in a `try/except json.JSONDecodeError`, validate the parsed dict, collect the good ones and count the bad. Robust and memory-light (one line at a time).

---

## Pseudocode (Optimal)

```
function load_jsonl(path, required_keys):
    good = []
    bad = 0
    for line in file:
        line = strip; if empty: continue
        try:
            rec = json.loads(line)
        except JSONDecodeError:
            bad += 1; continue                    ← malformed JSON
        if rec is a dict and all required_keys present:
            good.append(rec)
        else:
            bad += 1                              ← wrong schema
    return good, bad
```

---

## Python Code

```python
import json

def load_jsonl(path: str, required=("id", "text")) -> tuple[list[dict], int]:
    good, bad = [], 0
    with open(path, encoding="utf-8") as f:
        for line in f:                              # stream
            line = line.strip()
            if not line:
                continue
            try:
                rec = json.loads(line)              # parse one object
            except json.JSONDecodeError:
                bad += 1                            # malformed line → skip
                continue
            if isinstance(rec, dict) and all(k in rec for k in required):
                good.append(rec)
            else:
                bad += 1                            # wrong shape → skip
    return good, bad


# Demonstrate on a temp file
import tempfile, os
content = '{"id":1,"text":"hi"}\n{"id":2}\n{bad json\n{"id":4,"text":"yo"}\n'
with tempfile.NamedTemporaryFile("w", suffix=".jsonl", delete=False) as tf:
    tf.write(content); path = tf.name
records, bad = load_jsonl(path)
print(records)   # [{'id': 1, 'text': 'hi'}, {'id': 4, 'text': 'yo'}]
print("bad:", bad)   # bad: 2
os.remove(path)
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Stream + parse | O(n lines) | O(kept records) — or yield to keep O(1) |

---

## Edge Cases to remember

- **JSONL ≠ JSON** — `json.load(whole_file)` fails; parse **per line**. Classic mistake.
- **Malformed line** → catch `json.JSONDecodeError`, count, `continue` — don't crash the whole load.
- **Schema validation** — check required keys / types; log or count rejects so you know your data quality.
- **Blank lines / trailing newline** → skip empty after `strip()`.
- **Huge dataset** → make it a **generator** (`yield rec`) so you never hold all records in memory.
- **Encoding / BOM** → open with `utf-8`; strip a BOM if present.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Load a fine-tuning dataset (.jsonl) | Exact use |
| 2 | Parse CSV rows, skip bad ones | Per-row try/except |
| 3 | Validate API payloads | Schema check |
| 4 | Stream + filter records | Generator + predicate |
| 5 | Count valid vs invalid rows | Aggregate rejects |
| 6 | Log parser (Day 11 Q3) | Per-line robust parse |
| 7 | Deduplicate records (Q10) | Stream + set |
| 8 | Convert JSONL → cleaned dataset | Parse + validate + write |

---

## The ONE trick to remember

```
"PARSE PER LINE IN try/except; SKIP+COUNT BAD ROWS; VALIDATE THE SHAPE."
```

JSONL = one object per line, so `json.loads` **each line** (not the whole file). Wrap it in `try/except json.JSONDecodeError` so a single broken line is skipped, not fatal, then check the schema. Yield instead of collect for huge files.

> Memory hook: "One object per line — parse each, drop the rotten, keep the good."

---
