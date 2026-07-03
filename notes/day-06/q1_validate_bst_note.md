# Q1 — Validate BST (2-min card) — LC 98

**Is this binary tree a valid BST? (Every node must satisfy entire subtree constraints, not just parent.)**

**Pattern:** DFS with valid range — pass (lo, hi) bounds; node must satisfy lo < val < hi

```
      5   range=(-inf, +inf) ✓
     / \
    1   4   range of 4 = (5, +inf) → 4 < 5 → INVALID ✗

Valid BST:
    5   (-inf,+inf)✓
   / \
  3   7   3:(-inf,5)✓  7:(5,+inf)✓
 / \
1   4   1:(-inf,3)✓  4:(3,5)✓
```

**The trick:** "Going left: tighten max to node.val. Going right: tighten min to node.val."

```python
def isValidBST(root):
    def validate(node, lo, hi):
        if not node: return True
        if node.val <= lo or node.val >= hi: return False
        return validate(node.left, lo, node.val) and \
               validate(node.right, node.val, hi)
    return validate(root, float('-inf'), float('inf'))
```

**Complexity:** Time O(n) | Space O(h)

**Same pattern solves:** Kth Smallest BST (LC 230), LCA of BST (LC 235), Insert BST (LC 701), Delete BST (LC 450), Search BST (LC 700), Convert Array to BST (LC 108), Recover BST (LC 99).
