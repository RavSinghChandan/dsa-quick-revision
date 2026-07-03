# Q4 — Middle of Linked List (LeetCode 876)

## 1. What is this question actually asking?

Given a singly linked list, find and return the middle node. If the list has an even number of nodes, return the second of the two middle nodes. For example, for 1→2→3→4→5, the middle is node 3. For 1→2→3→4, the middle is node 3 (the second middle node).

## 2. Pattern

Slow and Fast Pointers — when fast reaches the end, slow is at the middle

## 3. Understand with a Diagram

```
Odd length: 1 → 2 → 3 → 4 → 5 → None

slow: 1 step per round
fast: 2 steps per round

        slow  fast
start:   1     1
step 1:  2     3
step 2:  3     5
fast.next = None → stop
slow = 3 = middle ✓

Even length: 1 → 2 → 3 → 4 → None

        slow  fast
start:   1     1
step 1:  2     3
step 2:  3     None (fast = 4, fast.next = None)
           → stop when fast is None
slow = 3 = second middle ✓

Visual progress:
1   2   3   4   5
S           F         (start)
    S       F     →   (step 1: S moves to 2, F moves to 3)
        S       F     (step 2: S moves to 3, F moves to 5)
F.next = None → done! S = 3

For even (1→2→3→4):
1   2   3   4
S       F         (step 1: S=2, F=3)
    S       F     (step 2: S=3, F=None? F=4, F.next=None → stop)
No wait: fast=4, fast.next = None → loop exits. S=3 ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — Count then re-traverse
**Idea:** First pass counts the total nodes. Second pass traverses to position n//2.

```
count = 0
node = head
while node:
    count += 1
    node = node.next

node = head
for i in range(count // 2):
    node = node.next
return node
```

Why not optimal: Two passes. Correct but unnecessary.

### Optimal — Slow and Fast Pointers
**Idea:** Use two pointers starting at head. Fast moves 2 steps, slow moves 1 step. When fast reaches the end, slow is at the middle.

```
slow = fast = head

while fast is not None and fast.next is not None:
    slow = slow.next
    fast = fast.next.next

return slow
```

One pass, O(1) space.

**Why does it work?**
When fast has traveled 2k steps, slow has traveled k steps.
For odd n: fast stops at last node (2*(n//2) = n-1 steps), slow is at n//2 position = middle.
For even n: fast stops at None (n steps), slow is at n//2 = second middle.

## 5. Pseudocode (Optimal)

```
function middleNode(head):
    slow = head
    fast = head
    
    while fast is not None and fast.next is not None:
        slow = slow.next
        fast = fast.next.next
    
    return slow
```

## 6. Python Code

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next


def middleNode(head):
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    return slow


# Helpers
def build(arr):
    if not arr: return None
    head = ListNode(arr[0])
    curr = head
    for v in arr[1:]:
        curr.next = ListNode(v)
        curr = curr.next
    return head

def to_list(node):
    res = []
    while node:
        res.append(node.val)
        node = node.next
    return res


# Test 1 — odd length
head = build([1, 2, 3, 4, 5])
print(to_list(middleNode(head)))   # [3, 4, 5] (from middle to end)

# Test 2 — even length (return 2nd middle)
head = build([1, 2, 3, 4, 5, 6])
print(to_list(middleNode(head)))   # [4, 5, 6]

# Test 3 — single node
head = build([1])
print(to_list(middleNode(head)))   # [1]

# Test 4 — two nodes
head = build([1, 2])
print(to_list(middleNode(head)))   # [2] (second middle)
```

## 7. Complexity Table

| Approach          | Time  | Space | Notes                          |
|-------------------|-------|-------|--------------------------------|
| Count + re-traverse| O(n) | O(1)  | Two passes                     |
| Slow/Fast Pointers | O(n) | O(1)  | One pass — optimal             |

## 8. Edge Cases to Remember

- Odd list — slow ends at the exact center (e.g., index n//2)
- Even list — slow ends at the second middle (e.g., index n//2)
- Single node — while loop doesn't execute, returns head
- Two nodes — fast.next is the last node, fast.next.next is None; slow moves once to node 2
- If problem wants first middle for even list — use: `while fast.next and fast.next.next`

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Palindrome Linked List               | Find middle, reverse second half                    | LC 234     |
| Reorder List                          | Find middle, reverse, interleave                    | LC 143     |
| Linked List Cycle                     | Fast/slow to detect cycle                           | LC 141     |
| Linked List Cycle II                  | Fast/slow then reset to find start                  | LC 142     |
| Sort List                             | Find middle for merge sort                          | LC 148     |
| Delete Node in Linked List            | Structural modification                             | LC 237     |
| Odd Even Linked List                  | Pointer manipulation                                | LC 328     |
| Remove Nth Node from End              | Two pointers with gap                               | LC 19      |

## 10. The ONE Trick to Remember

**"Fast runs twice as fast — when it stops, slow is at the middle."**

Fast pointer covers twice the distance. When fast hits the end (or can't take 2 more steps), slow has covered exactly half. For even-length lists, "can't take 2 more steps" means fast is at the last or second-to-last node, placing slow at the second middle. One while loop, no counting needed.
