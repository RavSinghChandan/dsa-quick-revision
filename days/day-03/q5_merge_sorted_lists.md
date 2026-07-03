# Q5 — Merge Two Sorted Linked Lists (LeetCode 21)

## 1. What is this question actually asking?

You have two sorted linked lists. Merge them into one sorted linked list and return the head of the merged list. You must do this in-place by relinking the existing nodes — don't create new nodes. The combined list must remain sorted throughout.

## 2. Pattern

Linked List — dummy head trick + merge two sorted sequences with two pointers

## 3. Understand with a Diagram

```
List 1: 1 → 2 → 4
List 2: 1 → 3 → 4

Use a dummy node to simplify edge cases:
dummy → (result builds here)

curr = dummy

Step 1: l1=1, l2=1 → l1 <= l2, pick l1
  curr → 1(from l1) → ...
  l1 = 2, curr = 1(from l1)

Step 2: l1=2, l2=1 → l2 < l1, pick l2
  curr → 1(l2)
  l2 = 3, curr = 1(l2)

Step 3: l1=2, l2=3 → l1 <= l2, pick l1
  curr → 2(l1)
  l1 = 4, curr = 2(l1)

Step 4: l1=4, l2=3 → l2 < l1, pick l2
  curr → 3(l2)
  l2 = 4, curr = 3(l2)

Step 5: l1=4, l2=4 → l1 <= l2, pick l1
  curr → 4(l1)
  l1 = None, curr = 4(l1)

l1 is None → append remaining l2=4
  curr → 4(l2)

Result: dummy → 1 → 1 → 2 → 3 → 4 → 4
Return dummy.next = 1 ✓

Pointer state diagram:
dummy  → ...
        curr points to last added node
        l1 points to next node to consider from list 1
        l2 points to next node to consider from list 2
```

## 4. Brute Force → Better → Optimal

### Brute Force — Collect all values, sort, rebuild
**Idea:** Traverse both lists, collect all values in an array, sort the array, build a new linked list.

```
vals = []
node = l1
while node: vals.append(node.val); node = node.next
node = l2
while node: vals.append(node.val); node = node.next
vals.sort()
head = dummy = ListNode(0)
for v in vals:
    dummy.next = ListNode(v)
    dummy = dummy.next
return head.next
```

Why not ideal: O(n+m) time but O(n+m) space for the array and new nodes.

### Optimal — In-place pointer manipulation
**Idea:** Use a dummy head node to avoid special-casing the result's head. Use two pointers, one for each list. At each step, pick the smaller head node, attach it to the result, and advance that list's pointer. When one list is exhausted, attach the remaining other list.

```
dummy = ListNode(0)
curr = dummy

while l1 is not None and l2 is not None:
    if l1.val <= l2.val:
        curr.next = l1
        l1 = l1.next
    else:
        curr.next = l2
        l2 = l2.next
    curr = curr.next

// Attach remaining
curr.next = l1 if l1 else l2

return dummy.next
```

O(n+m) time, O(1) extra space (no new nodes created).

**Recursive version:**
```
function merge(l1, l2):
    if not l1: return l2
    if not l2: return l1
    if l1.val <= l2.val:
        l1.next = merge(l1.next, l2)
        return l1
    else:
        l2.next = merge(l1, l2.next)
        return l2
```

Elegant but O(n+m) stack space.

## 5. Pseudocode (Optimal)

```
function mergeTwoLists(l1, l2):
    dummy = new Node(0)
    curr = dummy
    
    while l1 != None and l2 != None:
        if l1.val <= l2.val:
            curr.next = l1
            l1 = l1.next
        else:
            curr.next = l2
            l2 = l2.next
        curr = curr.next
    
    curr.next = l1 if l1 != None else l2
    
    return dummy.next
```

## 6. Python Code

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next


def mergeTwoLists(l1, l2):
    dummy = ListNode(0)
    curr = dummy
    
    while l1 and l2:
        if l1.val <= l2.val:
            curr.next = l1
            l1 = l1.next
        else:
            curr.next = l2
            l2 = l2.next
        curr = curr.next
    
    curr.next = l1 or l2  # attach whichever is left
    
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


# Test 1 — standard
l1 = build([1, 2, 4])
l2 = build([1, 3, 4])
print(to_list(mergeTwoLists(l1, l2)))   # [1, 1, 2, 3, 4, 4]

# Test 2 — one empty
l1 = build([])
l2 = build([0])
print(to_list(mergeTwoLists(l1, l2)))   # [0]

# Test 3 — both empty
print(to_list(mergeTwoLists(None, None)))  # []

# Test 4 — different lengths
l1 = build([1, 3, 5, 7])
l2 = build([2, 4])
print(to_list(mergeTwoLists(l1, l2)))   # [1, 2, 3, 4, 5, 7]

# Test 5 — one list is subset
l1 = build([1, 2, 3])
l2 = build([10, 20])
print(to_list(mergeTwoLists(l1, l2)))   # [1, 2, 3, 10, 20]
```

## 7. Complexity Table

| Approach          | Time     | Space  | Notes                              |
|-------------------|----------|--------|------------------------------------|
| Collect + Sort    | O((n+m) log(n+m)) | O(n+m) | New nodes, sort array        |
| Iterative merge   | O(n+m)   | O(1)   | In-place relinking — optimal       |
| Recursive merge   | O(n+m)   | O(n+m) | Stack depth = n+m                  |

## 8. Edge Cases to Remember

- Both lists empty — return None
- One list empty — return the other list unchanged
- Lists of very different lengths — the remaining portion attaches via `curr.next = l1 or l2`
- All elements of l1 < all elements of l2 — l1 fully consumed first, then l2 appended
- Duplicate values — the `<=` ensures stable order (l1 comes first when equal)
- Single elements in each — merge of size 2 result

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Merge K Sorted Lists                  | Extend to k lists using heap                        | LC 23      |
| Sort List                             | Merge sort on linked list (find mid, split, merge)  | LC 148     |
| Intersection of Two Linked Lists      | Two-pointer on two lists                            | LC 160     |
| Reverse Linked List                   | Fundamental pointer manipulation                    | LC 206     |
| Add Two Numbers                       | Traverse two lists simultaneously                   | LC 2       |
| Remove Duplicates from Sorted List    | Single pass on merged sorted structure              | LC 83      |
| Palindrome Linked List                | Find mid + reverse                                  | LC 234     |
| Merge Sorted Array                    | Same logic, on arrays instead of lists              | LC 88      |

## 10. The ONE Trick to Remember

**"Dummy head + pick the smaller of two heads each step + attach the leftover."**

The dummy node saves you from special-casing what the result's head is. curr tracks where to attach the next node. Always pick the smaller of l1 and l2, advance that pointer. When either list runs out, the rest of the other list is already sorted — just attach it whole. Three logical pieces: setup dummy, merge loop, append tail.
