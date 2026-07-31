# Day 12 · Q2 · Group Anagrams (Dictionary keyed by signature)  ·  [DICTS]

---

## What is this question actually asking?

Given a list of words, group together all words that are **anagrams** of each other (same letters, reordered).

```
["eat","tea","tan","ate","nat","bat"]
→ [["eat","tea","ate"], ["tan","nat"], ["bat"]]
```

> Tests the key idea behind bucketing: turn each item into a **canonical signature**, then group by that signature in a dict.

---

## Pattern

```
PATTERN: Hash Map grouping by a canonical key (signature)
```

Whenever you see: *"group items that are 'the same' under some rule"* → compute a **signature** that's identical for members of a group, use it as a dict key, append items into buckets.

---

## Understand with a diagram

```
"eat" → sort letters → "aet"  ┐
"tea" → "aet"                  ├─ same key "aet" → one bucket
"ate" → "aet"                  ┘
"tan" → "ant"  ┐
"nat" → "ant"  ┘ bucket "ant"
"bat" → "abt"    bucket "abt"

groups = {aet:[eat,tea,ate], ant:[tan,nat], abt:[bat]}
```

---

## Brute Force → Better → Optimal

### 🔴 Brute Force — compare every pair
For each word, compare against every group's representative by sorting → O(n² · k log k).

### 🟢 Optimal — Signature dict — O(n · k log k)
Compute each word's **sorted-letters** signature once, use `defaultdict(list)` to drop it into the right bucket. One pass. (Even faster signature: a 26-length letter-count tuple → O(n·k).)

---

## Pseudocode (Optimal)

```
function group_anagrams(words):
    groups = defaultdict(list)
    for word in words:
        key = sorted(word) as string     ← signature
        groups[key].append(word)
    return list(groups.values())
```

---

## Python Code

```python
from collections import defaultdict

def group_anagrams(words: list[str]) -> list[list[str]]:
    groups = defaultdict(list)
    for word in words:
        key = "".join(sorted(word))        # signature: "eat" -> "aet"
        groups[key].append(word)
    return list(groups.values())


# Faster signature (no sort): count of each letter
def group_anagrams_count(words: list[str]) -> list[list[str]]:
    groups = defaultdict(list)
    for word in words:
        count = [0] * 26
        for ch in word:
            count[ord(ch) - ord('a')] += 1
        groups[tuple(count)].append(word)   # tuple is hashable
    return list(groups.values())


# Test
print(group_anagrams(["eat","tea","tan","ate","nat","bat"]))
# [['eat','tea','ate'], ['tan','nat'], ['bat']]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Sort key | O(n · k log k) | O(n · k) |
| Count key | O(n · k) | O(n · k) |

(k = word length)

---

## Edge Cases to remember

- **Empty string** → its own valid group (key `""`).
- **Case / unicode** → normalize case first if needed.
- **Order of groups / within groups** not guaranteed → sort if the spec requires determinism.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Valid Anagram (LC 242) | Compare two signatures |
| 2 | Group by any rule (length, first letter) | Signature = the rule |
| 3 | Find duplicate files by content | Signature = file hash |
| 4 | Bucket records by category | defaultdict grouping |
| 5 | Isomorphic strings | Canonical mapping |
| 6 | Group shifted strings | Signature = shift pattern |
| 7 | Partition by feature in ML data | Group-by key |
| 8 | Deduplicate near-identical rows | Signature key |

---

## The ONE trick to remember

```
"TURN EACH ITEM INTO A CANONICAL KEY, THEN BUCKET IN A defaultdict."
```

Anagrams share the same sorted letters (or same letter-count). Make that the dict key and `append` — everything with the same signature lands in the same list automatically.

> Memory hook: "Same fingerprint → same bucket."

---
