# ⚡ Quick Note · Day 4 — Linked List · Q4 · Reverse in K-Groups
### (Read this in 2 minutes while walking)

---

## Problem in one line
> "Reverse the list in blocks of k; leave a final short block as-is."

## Pattern
`Segment reversal + dummy + group-tail relinking`

## Visual in your head
```
dummy → group_prev
kth exists? no → stop
reverse segment [group_prev.next .. kth]
group_prev.next = kth (new head)
group_prev = old head (new tail)
```

## The trick (say it out loud)
> "Check k nodes exist, reverse the segment, stitch prev→newHead, set prev=oldHead."

## Code skeleton
```python
kth = kth_from(group_prev,k)
if not kth: break
prev,curr=kth.next,group_prev.next
while curr is not kth.next: nxt=curr.next; curr.next=prev; prev=curr; curr=nxt
new_tail=group_prev.next; group_prev.next=kth; group_prev=new_tail
```

## Complexity
- Time O(n) · Space O(1)

## This trick solves more
Reverse k-Group (25) · Reverse LL II (92) · Swap Pairs (24, k=2) · Rotate List (61)
