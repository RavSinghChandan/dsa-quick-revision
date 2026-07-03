# Q3 — Find Starting Point of Cycle (LeetCode 142)

## 1. What is this question actually asking?

Given a linked list that may contain a cycle, find the node where the cycle begins. If there is no cycle, return None. For example, if the list is 1→2→3→4→5→3 (5 points back to 3), the cycle starts at node with value 3, so return that node.

## 2. Pattern

Floyd's Cycle Detection extended — after slow and fast meet, reset one pointer to head, advance both one step at a time; they meet at cycle start

## 3. Understand with a Diagram

```
List: head → A → B → C → D → E
                      ↑           ↓
                      H ← G ← F ←

Let:
  F = distance from head to cycle start (A→B→C = 3 steps if C is start)
  C = length of the cycle
  K = distance from cycle start to meeting point

Phase 1 — Find meeting point:
  slow travels: F + K
  fast travels: F + K + n*C  (lapped slow n times)
  Since fast = 2 * slow:
    F + K + n*C = 2(F + K)
    n*C = F + K
    F = n*C - K

Phase 2 — Find cycle start:
  Reset one pointer to head.
  Both advance 1 step at a time.
  After F steps, head-pointer is at cycle start.
  The in-cycle pointer has traveled F = n*C - K more steps from meeting point.
  Meeting point is K steps into cycle from start.
  After F = n*C - K more steps: K + (n*C - K) = n*C = 0 steps mod C from start.
  So the in-cycle pointer is also at cycle start!
  They meet at the cycle start. ✓

Concrete example:
  0 → 1 → 2 → 3 → 4
                ↑       ↓
                7 ← 6 ← 5

  F=3 (head to node 3), C=5 (3→4→5→6→7→3)

  Phase 1 trace:
  start: slow=0, fast=0
  step1: slow=1, fast=2
  step2: slow=2, fast=4
  step3: slow=3, fast=6
  step4: slow=4, fast=3  (fast: 6→7→3, then... wait)
  
  Let me redo with positions in cycle:
  Nodes: 0,1,2,3,4,5,6,7 where 3,4,5,6,7 form the cycle (3 is cycle start)
  
  step1: slow=1, fast=2
  step2: slow=2, fast=4
  step3: slow=3, fast=6
  step4: slow=4, fast=3  ← fast went 6→7→3(cycle start)
  step5: slow=5, fast=5  ← MEET at node 5!

  Phase 2: reset slow to head (0), fast stays at 5
  step1: slow=1, fast=6
  step2: slow=2, fast=7
  step3: slow=3, fast=3  ← MEET at node 3 = cycle start ✓
```

## 4. Brute Force → Better → Optimal

### Brute Force — HashSet of visited nodes
**Idea:** Traverse the list. For each node, check if it's in a visited set. The first node you visit for the second time is the cycle start.

```
seen = set()
node = head
while node:
    if node in seen: return node
    seen.add(node)
    node = node.next
return None
```

Why not optimal: O(n) space. But simple and correct.

### Optimal — Floyd's Extended
**Idea:** Phase 1: use fast and slow to detect a meeting point inside the cycle. Phase 2: reset one pointer to head, advance both one step at a time — they meet at the cycle start.

```
Phase 1: find meeting point
  slow = fast = head
  while fast and fast.next:
      slow = slow.next
      fast = fast.next.next
      if slow == fast: break
  else: return None  // no cycle

Phase 2: find cycle start
  slow = head
  while slow != fast:
      slow = slow.next
      fast = fast.next
  return slow  // or fast
```

O(n) time, O(1) space. The math proves they must meet at the cycle start.

## 5. Pseudocode (Optimal)

```
function detectCycleStart(head):
    slow = fast = head
    
    // Phase 1: find meeting point
    while fast is not None and fast.next is not None:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break
    else:
        return None  // no cycle
    
    // Phase 2: find cycle start
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    
    return slow
```

## 6. Python Code

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next


def detectCycle(head):
    if not head or not head.next:
        return None
    
    slow = fast = head
    
    # Phase 1: detect cycle
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            break
    else:
        return None  # no cycle
    
    # Phase 2: find cycle start
    slow = head
    while slow is not fast:
        slow = slow.next
        fast = fast.next
    
    return slow


# Build list with cycle
def build_with_cycle(arr, pos):
    nodes = [ListNode(val) for val in arr]
    for i in range(len(nodes) - 1):
        nodes[i].next = nodes[i+1]
    if pos >= 0:
        nodes[-1].next = nodes[pos]
    return nodes, nodes[0] if nodes else None


# Test 1 — cycle at index 1
nodes, head = build_with_cycle([3, 2, 0, -4], 1)
result = detectCycle(head)
print(result.val if result else None)   # 2

# Test 2 — cycle at index 0
nodes, head = build_with_cycle([1, 2], 0)
result = detectCycle(head)
print(result.val if result else None)   # 1

# Test 3 — no cycle
nodes, head = build_with_cycle([1], -1)
print(detectCycle(head))               # None

# Test 4 — cycle at start of longer list
nodes, head = build_with_cycle([1, 2, 3, 4, 5], 0)
result = detectCycle(head)
print(result.val if result else None)   # 1
```

## 7. Complexity Table

| Approach          | Time  | Space | Notes                          |
|-------------------|-------|-------|--------------------------------|
| HashSet           | O(n)  | O(n)  | Store visited nodes            |
| Floyd's Phase 1+2 | O(n)  | O(1)  | Mathematical guarantee         |

## 8. Edge Cases to Remember

- No cycle — Phase 1 exits via while condition (fast reaches None), return None
- Empty list — return None immediately
- Single node with self-loop (node.next = node) — Phase 1: fast==slow after 1 step; Phase 2: slow reset to head, slow=head→next=head, fast stays, meet at head ✓
- Cycle at first node — works correctly
- Cycle at last node only — works correctly
- Very long pre-cycle section — Floyd's handles correctly via the F = n*C - K math

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Linked List Cycle I                   | Just detect cycle, don't find start                 | LC 141     |
| Find the Duplicate Number             | Array as implicit linked list, find cycle start     | LC 287     |
| Happy Number                          | Find cycle in sequence, not in list                 | LC 202     |
| Intersection of Two Linked Lists      | Two pointers meet at intersection                   | LC 160     |
| Middle of Linked List                 | Slow/fast — slow at mid when fast reaches end       | LC 876     |
| Palindrome Linked List                | Find middle, reverse, compare                       | LC 234     |
| Reorder List                          | Find middle, reverse, interleave                    | LC 143     |
| Remove Nth from End                   | Two pointers with fixed gap                         | LC 19      |

## 10. The ONE Trick to Remember

**"After slow and fast meet, reset slow to head — advance both by 1 — they meet at the cycle start."**

The math: slow travels F+K, fast travels F+K+nC = 2(F+K), so F = nC-K. After the meeting point, the remaining distance in the cycle back to start is C-K. From head to start is F = nC-K ≡ -K ≡ C-K (mod C). So both pointers travel the same distance to reach the cycle start. Memorize the two-phase pattern: detect, then reset-one-to-head.
