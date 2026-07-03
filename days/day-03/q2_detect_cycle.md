# Q2 — Detect Cycle in Linked List (LeetCode 141)

## 1. What is this question actually asking?

You're given a linked list. Determine if it has a cycle — that is, whether following the next pointers from any node eventually leads you back to a node you've already visited, creating an infinite loop. Return True if a cycle exists, False otherwise.

## 2. Pattern

Floyd's Cycle Detection (Tortoise and Hare) — slow pointer moves 1 step, fast pointer moves 2 steps; if they meet, there's a cycle

## 3. Understand with a Diagram

```
Linked list with cycle:
1 → 2 → 3 → 4 → 5
            ↑       ↓
            8 ← 7 ← 6

slow (tortoise): moves 1 step at a time
fast (hare):     moves 2 steps at a time

Start: slow=1, fast=1

Step 1: slow=2,  fast=3
Step 2: slow=3,  fast=5
Step 3: slow=4,  fast=7
Step 4: slow=5,  fast=4  (fast went 7→6→4, slow went 4→5)
Step 5: slow=6,  fast=6  ← MEET! Cycle detected ✓

No cycle example:
1 → 2 → 3 → 4 → None

fast reaches None before meeting slow → no cycle
Step 1: slow=2, fast=3
Step 2: slow=3, fast=None → fast is None → return False

Why do they always meet?
When slow enters the cycle, fast is already inside.
Fast gains 1 step per round on slow (moves 2 vs 1).
Gap between them decreases by 1 each step → eventually gap=0 → they meet.
```

## 4. Brute Force → Better → Optimal

### Brute Force — HashSet to track visited nodes
**Idea:** As you traverse the list, add each node's identity (memory address / object id) to a set. If you ever encounter a node already in the set, there's a cycle.

```
seen = empty set
node = head
while node is not None:
    if node in seen: return True
    seen.add(node)
    node = node.next
return False
```

Why it works: Correct, simple, O(n) time. But uses O(n) extra space.

### Better — Modify list (mark visited)
**Idea:** Mark each node as visited by changing its value to a sentinel. Detect cycle when you reach a marked node.

```
node = head
while node:
    if node.val == SENTINEL: return True
    node.val = SENTINEL
    node = node.next
return False
```

Why bad: Destructively modifies the list. Not acceptable in practice.

### Optimal — Floyd's Cycle Detection
**Idea:** Use two pointers (slow and fast). Move slow by 1 step and fast by 2 steps each iteration. If there's a cycle, fast will "lap" slow and they'll meet inside the cycle. If no cycle, fast will reach None.

```
slow = head
fast = head

while fast is not None and fast.next is not None:
    slow = slow.next
    fast = fast.next.next
    if slow == fast:
        return True

return False
```

O(n) time, O(1) space. Elegant and the standard solution.

**Why does this work mathematically?**
```
If cycle length = c, and fast enters cycle k steps after slow:
Each round, fast gains 1 step on slow.
After c rounds, fast laps slow — they meet.
Total steps ≤ 2n.
```

## 5. Pseudocode (Optimal)

```
function hasCycle(head):
    slow = head
    fast = head
    
    while fast is not None and fast.next is not None:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    
    return False
```

## 6. Python Code

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next


def hasCycle(head):
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            return True
    
    return False


# Build list with cycle for testing
def build_with_cycle(arr, pos):
    """pos is the index where tail connects back (-1 for no cycle)"""
    if not arr:
        return None
    nodes = [ListNode(val) for val in arr]
    for i in range(len(nodes)-1):
        nodes[i].next = nodes[i+1]
    if pos >= 0:
        nodes[-1].next = nodes[pos]
    return nodes[0]


# Test 1 — cycle exists (tail connects to index 1)
head = build_with_cycle([3, 2, 0, -4], 1)
print(hasCycle(head))   # True

# Test 2 — cycle exists (tail connects to index 0)
head = build_with_cycle([1, 2], 0)
print(hasCycle(head))   # True

# Test 3 — no cycle
head = build_with_cycle([1], -1)
print(hasCycle(head))   # False

# Test 4 — empty list
print(hasCycle(None))   # False

# Test 5 — long list, no cycle
head = build_with_cycle([1, 2, 3, 4, 5], -1)
print(hasCycle(head))   # False
```

## 7. Complexity Table

| Approach          | Time  | Space | Notes                            |
|-------------------|-------|-------|----------------------------------|
| HashSet           | O(n)  | O(n)  | Extra memory per node            |
| Destructive mark  | O(n)  | O(1)  | Modifies list — avoid            |
| Floyd's (optimal) | O(n)  | O(1)  | Classic two-pointer trick        |

## 8. Edge Cases to Remember

- Empty list — fast is None immediately, return False
- Single node, no self-loop — fast.next is None, return False
- Single node with self-loop (node.next = node) — slow=node, fast=node, one step: both at node.next = node, they meet → True
- Two-node cycle — works correctly
- Very long list with cycle at end — Floyd's still O(n)
- Cycle at first node — fast and slow both re-enter from index 0

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Find Start of Cycle                   | Extend Floyd's to locate entry point                | LC 142     |
| Happy Number                          | Detect cycle in sequence of digit-square sums       | LC 202     |
| Linked List Cycle II                  | Return the cycle-start node                         | LC 142     |
| Find Duplicate Number                 | Array as linked list, find cycle start              | LC 287     |
| Middle of Linked List                 | Slow/fast — where slow stops is middle              | LC 876     |
| Palindrome Linked List                | Find middle with slow/fast, then reverse            | LC 234     |
| Intersection of Two Linked Lists      | Two pointers switching lists                        | LC 160     |
| Remove Nth Node from End              | Two pointers with gap of n                          | LC 19      |

## 10. The ONE Trick to Remember

**"Tortoise and hare: if fast ever equals slow inside the loop, there's a cycle."**

Move slow 1 step, fast 2 steps. Fast laps slow if and only if there's a cycle — because in a cycle, fast catches slow at a rate of 1 step per round. No cycle means fast reaches None. The meeting condition `slow == fast` (same node object, not just same value) is the cycle signal.
