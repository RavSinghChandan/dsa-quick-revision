# Q1 — Next Greater Element (LeetCode 496)

## 1. What is this question actually asking?

You have two arrays: nums1 is a subset of nums2. For each number in nums1, find the next greater element in nums2 — that is, the first element to the right of that number in nums2 which is larger than it. If no such element exists, return -1. For example, if nums2 is [1,3,4,2] and nums1 is [4,1,2], then for 4 (no greater to the right): -1, for 1 (next greater is 3): 3, for 2 (no greater to right): -1.

## 2. Pattern

Monotonic Stack — maintain a decreasing stack, pop when a greater element is found; that element is the "next greater" for all popped elements

## 3. Understand with a Diagram

```
nums2 = [2, 1, 2, 4, 3]

We want next greater element for each position in nums2:
  2 → 4 (first element to its right that's > 2)
  1 → 2
  2 → 4
  4 → -1
  3 → -1

Monotonic Stack Trace (maintain decreasing stack):
Process left to right:

i=0, val=2: stack=[], push 2. stack=[2]
i=1, val=1: 1 < stack.top()=2, push 1. stack=[2,1]
i=2, val=2: 2 > stack.top()=1 → pop 1, NGE[1]=2. 2==stack.top()=2 → pop 2, NGE[2]=2. push 2. stack=[2]
i=3, val=4: 4 > stack.top()=2 → pop 2, NGE[2 at pos0]=4. stack empty, push 4. stack=[4]
i=4, val=3: 3 < stack.top()=4, push 3. stack=[4,3]
End: remaining stack=[4,3] → NGE = -1 for both

Result map: {2→4, 1→2, 4→-1, 3→-1}

Stack state visualization:
      ┌───┐
      │ 2 │ ← top
      │ 1 │
      └───┘
When 2 arrives: pop 1 (NGE=2), pop 2 (NGE=2), push new 2
When 4 arrives: pop 2 (NGE=4), stack empty, push 4
```

## 4. Brute Force → Better → Optimal

### Brute Force — For each element scan right
**Idea:** For each element in nums2, scan every element to its right to find the next greater.

```
nge = {}
for i from 0 to len(nums2)-1:
    found = False
    for j from i+1 to len(nums2)-1:
        if nums2[j] > nums2[i]:
            nge[nums2[i]] = nums2[j]
            found = True
            break
    if not found:
        nge[nums2[i]] = -1
return [nge.get(x, -1) for x in nums1]
```

Why slow: O(n²) for finding NGE in nums2.

### Optimal — Monotonic Stack
**Idea:** Process nums2 left to right. Maintain a stack of elements waiting for their next greater element. When a new element arrives, it is the NGE for all stack elements smaller than it. Pop them, record the NGE, then push the new element.

```
stack = []
nge = {}

for val in nums2:
    while stack and stack[-1] < val:
        nge[stack.pop()] = val
    stack.append(val)

while stack:
    nge[stack.pop()] = -1

return [nge.get(x, -1) for x in nums1]
```

O(n) time — each element is pushed and popped at most once.

**Trace:**
```
nums2 = [1, 3, 4, 2], nums1 = [4, 1, 2]

val=1: stack empty, push 1. stack=[1]
val=3: 3>1 → pop 1, nge[1]=3. push 3. stack=[3]
val=4: 4>3 → pop 3, nge[3]=4. push 4. stack=[4]
val=2: 2<4, push 2. stack=[4,2]

Remaining: nge[4]=-1, nge[2]=-1

nge = {1:3, 3:4, 4:-1, 2:-1}
nums1=[4,1,2] → [-1, 3, -1]
```

## 5. Pseudocode (Optimal)

```
function nextGreaterElement(nums1, nums2):
    stack = []
    nge = {}
    
    for val in nums2:
        while stack is not empty and stack.top() < val:
            nge[stack.pop()] = val
        stack.push(val)
    
    while stack is not empty:
        nge[stack.pop()] = -1
    
    return [nge[x] for x in nums1]
```

## 6. Python Code

```python
def nextGreaterElement(nums1, nums2):
    stack = []
    nge = {}
    
    for val in nums2:
        while stack and stack[-1] < val:
            nge[stack.pop()] = val
        stack.append(val)
    
    # Remaining elements have no next greater
    while stack:
        nge[stack.pop()] = -1
    
    return [nge[x] for x in nums1]


# Test 1 — standard LeetCode example
print(nextGreaterElement([4,1,2], [1,3,4,2]))   # [-1, 3, -1]

# Test 2
print(nextGreaterElement([2,4], [1,2,3,4]))      # [3, -1]

# Test 3 — all have next greater
print(nextGreaterElement([1,2,3], [1,2,3,4]))    # [2, 3, 4]

# Test 4 — none have next greater
print(nextGreaterElement([4,3,2], [4,3,2,1]))    # [-1, -1, -1]

# Test 5 — single element
print(nextGreaterElement([1], [1]))              # [-1]
```

## 7. Complexity Table

| Approach          | Time   | Space | Notes                            |
|-------------------|--------|-------|----------------------------------|
| Brute Force       | O(n²)  | O(n)  | Nested scan for each element     |
| Monotonic Stack   | O(n+m) | O(n)  | n=nums2 size, m=nums1 size       |

## 8. Edge Cases to Remember

- Element has no next greater (last position or nothing larger to the right) — gets -1
- Duplicate values in nums2 — the problem says nums2 has distinct values (check constraints)
- nums1 has a single element — works normally
- All elements in descending order — stack fills up, all get -1
- All elements ascending — every element gets its immediate right neighbor
- Element with value equal to right neighbor — not "greater than", use strict `>`

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Next Greater Element II (circular)    | Same but array wraps around — iterate 2x           | LC 503     |
| Next Greater Node in Linked List      | Same pattern on linked list                         | LC 1019    |
| Daily Temperatures                    | Find days until warmer temperature                  | LC 739     |
| Largest Rectangle in Histogram        | Monotonic stack — find spans                        | LC 84      |
| Trapping Rain Water                   | Precompute left/right max or monotonic stack        | LC 42      |
| Sum of Subarray Minimums              | Monotonic stack to find previous/next smaller       | LC 907     |
| Stock Span Problem                    | Monotonic stack — count days with lower price       | GFG        |
| Buildings with Ocean View             | Monotonic stack — find increasing suffix            | LC 1762    |

## 10. The ONE Trick to Remember

**"Stack stores elements still waiting for their next greater — incoming larger element resolves them all."**

When a new value arrives, it's the NGE for every stack element smaller than it. Pop them all and record the answer. Then push the new value (it's now waiting for its own NGE). After the loop, anything still in the stack never found a greater element — give them -1.
