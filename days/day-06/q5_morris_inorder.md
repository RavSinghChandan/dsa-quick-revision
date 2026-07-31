# Day 6 — Binary Tree · Q5 · Morris Inorder Traversal

---

## What is this question actually asking?

Traverse a binary tree **in-order** using **O(1) extra space** — no recursion stack, no explicit stack.

```
     1
      \
       2
      /
     3      inorder → 1 3 2
```

> The elegant threaded-tree trick. You temporarily create "threads" from a subtree's rightmost node back to its ancestor, then remove them — visiting each node in order with no stack.

---

## Pattern

```
PATTERN: Morris Threading — temporary predecessor links, O(1) space
```

Whenever you see: *"inorder/preorder traversal in O(1) space"* → for each node with a left child, find its **inorder predecessor** (rightmost node of the left subtree); thread it back to the current node so you can return after exploring left, then unthread.

---

## Understand with a diagram

```
At node cur:
  if cur has NO left child:
      visit cur; go right
  else:
      pred = rightmost of cur.left subtree
      if pred.right is None:
          pred.right = cur      ← make thread; go left
      else:                     ← thread exists → left subtree done
          pred.right = None     ← remove thread
          visit cur; go right
```

The thread lets you climb back up without a stack.

---

## Brute Force → Better → Optimal

### 🔴 Recursive / stack — O(n) time, O(h) space
Standard inorder uses recursion or an explicit stack — O(h) space.

### 🟢 Optimal — Morris — O(n) time, O(1) space
Temporary threads replace the stack. Each edge is traversed at most a constant number of times → still O(n).

---

## Pseudocode (Optimal)

```
function morris_inorder(root):
    result = []
    cur = root
    while cur:
        if cur.left is None:
            result.append(cur.val); cur = cur.right
        else:
            pred = cur.left
            while pred.right and pred.right != cur:
                pred = pred.right          ← rightmost of left subtree
            if pred.right is None:
                pred.right = cur           ← thread up
                cur = cur.left
            else:
                pred.right = None          ← unthread
                result.append(cur.val)
                cur = cur.right
    return result
```

---

## Python Code

```python
class TreeNode:
    def __init__(self, val, left=None, right=None):
        self.val = val; self.left = left; self.right = right

def morris_inorder(root) -> list[int]:
    result = []
    cur = root
    while cur:
        if cur.left is None:
            result.append(cur.val)         # visit
            cur = cur.right
        else:
            pred = cur.left
            while pred.right and pred.right is not cur:
                pred = pred.right           # inorder predecessor
            if pred.right is None:
                pred.right = cur            # create thread, dive left
                cur = cur.left
            else:
                pred.right = None           # remove thread (restore tree)
                result.append(cur.val)      # visit after left done
                cur = cur.right
    return result


# Test
n1 = TreeNode(1); n2 = TreeNode(2); n3 = TreeNode(3)
n1.right = n2; n2.left = n3
print(morris_inorder(n1))   # [1, 3, 2]

# balanced example
a = {v: TreeNode(v) for v in [4,2,6,1,3,5,7]}
a[4].left, a[4].right = a[2], a[6]
a[2].left, a[2].right = a[1], a[3]
a[6].left, a[6].right = a[5], a[7]
print(morris_inorder(a[4]))   # [1,2,3,4,5,6,7]
```

---

## Complexity

| | Time | Space |
|---|---|---|
| Recursion/stack | O(n) | O(h) |
| Morris | O(n) | O(1) |

---

## Edge Cases to remember

- **Always remove the thread** — restore `pred.right = None`, or you corrupt the tree (and loop forever).
- **`pred.right != cur` check** — distinguishes "no thread yet" from "thread already made".
- **Preorder Morris** — visit the node when you *create* the thread (before diving left), not after.
- **Tree mutated mid-traversal** — threads temporarily change the structure; it's restored by the end.

---

## Similar Questions — same pattern solves all of these

| # | Question | Where pattern applies |
|---|---|---|
| 1 | Binary Tree Inorder Traversal (LC 94) | This, O(1) space |
| 2 | Preorder Traversal O(1) (LC 144) | Morris preorder |
| 3 | Recover BST (LC 99) | Morris to find swapped nodes |
| 4 | Kth Smallest in BST (LC 230) | Inorder streaming |
| 5 | Flatten tree to linked list (LC 114) | Threading |
| 6 | Validate BST | Inorder monotonic |
| 7 | Convert BST to sorted DLL | Inorder links |
| 8 | Increasing order search tree (LC 897) | Inorder rewire |

---

## The ONE trick to remember

```
"THREAD THE INORDER PREDECESSOR BACK TO cur; VISIT ON RETURN; THEN UNTHREAD."
```

For a node with a left child, find the rightmost node of its left subtree and point it to the current node — a temporary thread that lets you return without a stack. On the second visit, remove the thread and output the node. O(1) space.

> Memory hook: "Tie a string from the left subtree's end back to me, so I can find my way home without a stack."

---
