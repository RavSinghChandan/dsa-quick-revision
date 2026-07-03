# Q6 — Remove Nth Node from End (LeetCode 19)

## 1. What is this question actually asking?

Given a linked list, remove the nth node from the end of the list and return the head. For example, in 1→2→3→4→5, removing the 2nd from the end removes node 4, leaving 1→2→3→5. You must do this in one pass.

## 2. Pattern

Two Pointers with a gap of n — advance fast by n steps first, then advance both until fast reaches end

## 3. Understand with a Diagram

```
List: 1 → 2 → 3 → 4 → 5    Remove 2nd from end

Step 1: Use a dummy node before head to handle edge case of removing the head.
dummy → 1 → 2 → 3 → 4 → 5

Step 2: Place fast and slow both at dummy. Advance fast by n+1=3 steps.
        (n+1 because we want slow to be ONE BEFORE the node to delete)

fast advances 3 steps:
dummy  1  2  3  4  5
slow
fast → 1 (step 1)
fast → 2 (step 2)
fast → 3 (step 3)

State:
dummy  1  2  3  4  5
  S         F

Step 3: Advance both until fast is None:
slow → 1, fast → 4
slow → 2, fast → 5
slow → 3, fast → None → STOP

State:
dummy  1  2  3  4  5
               S     F=None

slow is at node 3, which is ONE BEFORE node 4 (the 2nd from end).

Step 4: Remove: slow.next = slow.next.next
  slow.next = 4.next = 5

Result: dummy → 1 → 2 → 3 → 5
Return dummy.next = 1 ✓

Why n+1 steps for fast?
fast: ←n→ gap ←1→ slow
When fast reaches None, slow is n nodes behind fast.
fast is at "position after last node" (None), so slow is n from end.
But we want slow to be at the node BEFORE the one to delete.
That's why we advance fast by n+1, not n.
Or equivalently: advance fast by n, then advance both, stop when fast.next is None.
```

## 4. Brute Force → Better → Optimal

### Brute Force — Count length then traverse
**Idea:** First pass counts total length L. Second pass traverses to position L-n to find the node before the target, then removes.

```
L = 0
node = head
while node: L += 1; node = node.next

dummy = ListNode(0)
dummy.next = head
node = dummy
for _ in range(L - n):
    node = node.next
node.next = node.next.next
return dummy.next
```

Why not optimal: Two passes. Correct but unnecessary.

### Optimal — Two pointers with gap
**Idea:** Use a dummy node. Move fast n+1 steps ahead of slow. Then advance both together until fast is None. At that point, slow is right before the node to delete.

```
dummy.next = head
slow = fast = dummy

// Move fast n+1 steps ahead
for _ in range(n + 1):
    fast = fast.next

// Advance both together
while fast is not None:
    slow = slow.next
    fast = fast.next

// Delete the node after slow
slow.next = slow.next.next

return dummy.next
```

O(n) time, O(1) space, one pass.

**Alternate implementation:** move fast n steps ahead, then both until fast.next is None (fast stops at last node, not beyond).

## 5. Pseudocode (Optimal)

```
function removeNthFromEnd(head, n):
    dummy = new Node(0)
    dummy.next = head
    slow = fast = dummy
    
    // Advance fast by n+1 steps
    for i from 0 to n:   // n+1 total steps (0..n inclusive)
        fast = fast.next
    
    // Move both until fast is None
    while fast is not None:
        slow = slow.next
        fast = fast.next
    
    // Remove node after slow
    slow.next = slow.next.next
    
    return dummy.next
```

## 6. Python Code

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next


def removeNthFromEnd(head, n):
    dummy = ListNode(0)
    dummy.next = head
    slow = fast = dummy
    
    # Move fast n+1 steps ahead
    for _ in range(n + 1):
        fast = fast.next
    
    # Move both until fast is None
    while fast:
        slow = slow.next
        fast = fast.next
    
    # Delete the node after slow
    slow.next = slow.next.next
    
    return dummy.next


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


# Test 1 — standard, remove 2nd from end
head = build([1, 2, 3, 4, 5])
print(to_list(removeNthFromEnd(head, 2)))   # [1, 2, 3, 5]

# Test 2 — remove last node
head = build([1, 2, 3])
print(to_list(removeNthFromEnd(head, 1)))   # [1, 2]

# Test 3 — remove first node (nth from end = length)
head = build([1, 2, 3])
print(to_list(removeNthFromEnd(head, 3)))   # [2, 3]

# Test 4 — single node
head = build([1])
print(to_list(removeNthFromEnd(head, 1)))   # []

# Test 5 — two nodes, remove head
head = build([1, 2])
print(to_list(removeNthFromEnd(head, 2)))   # [2]
```

## 7. Complexity Table

| Approach          | Time  | Space | Notes                          |
|-------------------|-------|-------|--------------------------------|
| Count + Traverse  | O(n)  | O(1)  | Two passes                     |
| Two Pointer Gap   | O(n)  | O(1)  | One pass — optimal             |

## 8. Edge Cases to Remember

- n = length of list — removes the head. Dummy node handles this cleanly
- Single node list, n=1 — fast goes to None after 2 steps, slow stays at dummy, dummy.next = None
- n = 1 — removes the last node, slow stops at second-to-last
- Two-node list — carefully verify both n=1 and n=2 work
- fast advances n+1 (not n) steps if you want slow to be BEFORE the target. Or n steps if you stop when fast.next is None
- Always use dummy node — it unifies removing the head with all other cases

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Remove Linked List Elements           | Remove all nodes with given value                   | LC 203     |
| Delete Node in Linked List            | Delete without access to head                       | LC 237     |
| Middle of Linked List                 | Slow/fast to find middle                            | LC 876     |
| Linked List Cycle                     | Slow/fast to detect cycle                           | LC 141     |
| Swap Nodes in Pairs                   | Relinking with pointer manipulation                 | LC 24      |
| Rotate List                           | Find (length - k % length) th from end             | LC 61      |
| Reorder List                          | Find middle, reverse, interleave                    | LC 143     |
| Intersection of Two Linked Lists      | Length-based two pointer sync                       | LC 160     |

## 10. The ONE Trick to Remember

**"Gap of n+1: advance fast by n+1, then move both — slow lands just before the target."**

The gap between fast and slow = n+1. When fast falls off the end (reaches None), slow is n+1 nodes behind — which is exactly 1 node before the nth node from the end. So slow.next is the node to delete. The dummy head node removes the special case of deleting the actual head.
