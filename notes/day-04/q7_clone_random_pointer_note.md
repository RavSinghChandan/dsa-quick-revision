# ⚡ Quick Note · Day 4 — Linked List · Q7 · Clone w/ Random Ptr
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Deep copy a list where each node also has a random pointer."

## Pattern
`Interleave clones (A→A'→B→B') → set randoms → split`  (or hash map)

## Visual in your head
```
1→1'→2→2'→3→3'
copy.random = orig.random.next   (twin is right after)
then unzip into two lists
```

## The trick (say it out loud)
> "Weave each clone after its original. clone.random = orig.random.next. Then split (restore both)."

## Code skeleton
```python
# interleave, then:
if cur.random: cur.next.random = cur.random.next
# then detangle
copy=cur.next; cur.next=copy.next; copy.next=copy.next.next if copy.next else None
```

## Complexity
- Interleave O(n) time O(1) space · Hash map O(n)/O(n)

## This trick solves more
Copy List w/ Random (138) · Clone Graph (133) · Deep copy tree w/ parent ptr · Clone N-ary tree
