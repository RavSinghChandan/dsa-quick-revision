# Q2 — Kth Smallest in BST (2-min card) — LC 230

**Find the kth smallest element in a BST.**

**Pattern:** BST In-order Traversal — in-order gives ascending sorted order; count to k

```
BST:    5
       / \
      3   6
     / \
    2   4
   /
  1

In-order: 1→2→3→4→5→6
k=3 → answer = 3

Iterative trace (stack):
push 5→3→2→1, pop 1 (cnt=1), pop 2 (cnt=2), pop 3 (cnt=3=k) → return 3 ✓
```

**The trick:** "In-order of BST = sorted array — the kth node visited is the kth smallest."

```python
stack, curr, count = [], root, 0
while stack or curr:
    while curr: stack.append(curr); curr = curr.left
    curr = stack.pop(); count += 1
    if count == k: return curr.val
    curr = curr.right
```

**Complexity:** Time O(k+h) | Space O(h)

**Same pattern solves:** Validate BST (LC 98), Inorder Successor in BST (LC 285), Find Mode in BST (LC 501), Convert BST to Greater Tree (LC 538), Recover BST (LC 99), Balance BST (LC 1382), Binary Tree Inorder (LC 94).
