# Q1 — Reverse a Linked List (LeetCode 206)

## 1. What is this question actually asking?

You have a singly linked list where each node points to the next node. Reverse it so the last node becomes the first, the first becomes the last, and all the arrows point backwards. For example, 1→2→3→4→5 should become 5→4→3→2→1. Return the new head of the reversed list.

## 2. Pattern

Linked List — three-pointer iterative reversal (or recursive)

## 3. Understand with a Diagram

```
Original:
1 → 2 → 3 → 4 → 5 → None
^
head

Goal:
None ← 1 ← 2 ← 3 ← 4 ← 5
                            ^
                          new head

Iterative process (prev, curr, next):

Step 0: prev=None  curr=1
   None   1 → 2 → 3 → 4 → 5

Step 1: next=2, 1.next=None, prev=1, curr=2
   None ← 1   2 → 3 → 4 → 5
          prev curr

Step 2: next=3, 2.next=1, prev=2, curr=3
   None ← 1 ← 2   3 → 4 → 5
               prev curr

Step 3: next=4, 3.next=2, prev=3, curr=4
   None ← 1 ← 2 ← 3   4 → 5
                   prev curr

Step 4: next=5, 4.next=3, prev=4, curr=5
   None ← 1 ← 2 ← 3 ← 4   5
                       prev curr

Step 5: next=None, 5.next=4, prev=5, curr=None
   None ← 1 ← 2 ← 3 ← 4 ← 5
                           prev curr=None

curr is None → stop. Return prev=5 as new head.
5 → 4 → 3 → 2 → 1 → None ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — Collect then rebuild
**Idea:** Traverse the list, collect all values in an array. Then rebuild the list in reverse order.

```
values = []
node = head
while node:
    values.append(node.val)
    node = node.next

prev = None
for val in reversed(values):
    node = ListNode(val)
    node.next = prev
    prev = node
return prev
```

Why not ideal: O(n) time and O(n) space. Creates new nodes unnecessarily.

### Better — Stack-based
**Idea:** Push all nodes onto a stack, pop them to build the reversed list.

```
stack = []
node = head
while node:
    stack.append(node)
    node = node.next

new_head = stack.pop()
curr = new_head
while stack:
    curr.next = stack.pop()
    curr = curr.next
curr.next = None
return new_head
```

Why same: Still O(n) space. Not an improvement.

### Optimal — Three Pointer Iterative
**Idea:** Reverse the list in-place using three pointers: prev (starts at None), curr (starts at head), next (temporary). At each step, save next, redirect curr.next to prev, then advance both.

```
prev = None
curr = head

while curr:
    next_node = curr.next   // save next
    curr.next = prev        // reverse the arrow
    prev = curr             // advance prev
    curr = next_node        // advance curr

return prev  // new head
```

O(n) time, O(1) space.

**Trace:**
Already done in the diagram above.

**Recursive version:**
```
function reverse(head):
    if not head or not head.next: return head
    new_head = reverse(head.next)
    head.next.next = head
    head.next = None
    return new_head
```

Recursive builds the reversed list from the tail, O(n) time, O(n) stack space.

## 5. Pseudocode (Optimal)

```
function reverseList(head):
    prev = None
    curr = head
    
    while curr is not None:
        next_node = curr.next
        curr.next = prev
        prev = curr
        curr = next_node
    
    return prev
```

## 6. Python Code

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next


def reverseList(head):
    prev = None
    curr = head
    
    while curr:
        next_node = curr.next
        curr.next = prev
        prev = curr
        curr = next_node
    
    return prev


# Helper to build list from array
def build_list(arr):
    if not arr:
        return None
    head = ListNode(arr[0])
    curr = head
    for val in arr[1:]:
        curr.next = ListNode(val)
        curr = curr.next
    return head

# Helper to convert list to array
def to_array(head):
    result = []
    while head:
        result.append(head.val)
        head = head.next
    return result


# Test 1 — standard case
head = build_list([1, 2, 3, 4, 5])
print(to_array(reverseList(head)))   # [5, 4, 3, 2, 1]

# Test 2 — two nodes
head = build_list([1, 2])
print(to_array(reverseList(head)))   # [2, 1]

# Test 3 — single node
head = build_list([1])
print(to_array(reverseList(head)))   # [1]

# Test 4 — empty list
print(to_array(reverseList(None)))   # []
```

## 7. Complexity Table

| Approach          | Time  | Space | Notes                           |
|-------------------|-------|-------|---------------------------------|
| Collect + Rebuild | O(n)  | O(n)  | Array storage                   |
| Stack-based       | O(n)  | O(n)  | Stack storage                   |
| Three Pointer     | O(n)  | O(1)  | In-place, optimal               |
| Recursive         | O(n)  | O(n)  | Call stack space                |

## 8. Edge Cases to Remember

- Empty list (head = None) — return None
- Single node — prev becomes that node, curr becomes None, return that node
- Two nodes — first common tricky case, works fine with the three-pointer approach
- Already reversed — works, just reverses back to original
- After reversal, old head's next must be None — the algorithm handles this (curr.next = prev when prev=None)

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Reverse Linked List II               | Reverse only from position left to right            | LC 92      |
| Palindrome Linked List               | Reverse second half, compare with first half        | LC 234     |
| Reverse Nodes in k-Group             | Reverse every k consecutive nodes                   | LC 25      |
| Swap Nodes in Pairs                  | Reverse pairs of nodes                              | LC 24      |
| Reorder List                         | Find middle, reverse second half, merge             | LC 143     |
| Rotate List                          | Find new tail, reverse connections                  | LC 61      |
| Add Two Numbers                      | Traverse reversed linked lists                      | LC 2       |
| Odd Even Linked List                 | Relink nodes by position                            | LC 328     |

## 10. The ONE Trick to Remember

**"Save next, flip arrow, slide both pointers — three lines inside the loop."**

In every iteration: (1) save curr.next so you don't lose the rest of the list, (2) flip curr.next = prev to reverse the arrow, (3) advance both prev and curr. Three operations, three pointers. When curr becomes None, prev is sitting at the new head.
