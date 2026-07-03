# Q4 — Implement Stack Using Queue (LeetCode 225)

## 1. What is this question actually asking?

You need to build a stack (Last In, First Out) using only queue operations (enqueue and dequeue). A queue is First In, First Out. You need to support push, pop, top, and empty operations. The challenge is simulating LIFO behavior using FIFO data structures.

## 2. Pattern

Queue — make push expensive (rotate all elements to keep newest at front) OR make pop expensive

## 3. Understand with a Diagram

```
Stack operations: push(1), push(2), push(3), pop()

Expected stack behavior:
push(1): [1]
push(2): [1,2]  ← top is 2
push(3): [1,2,3] ← top is 3
pop()  → 3, stack=[1,2]

Approach 1: Make PUSH expensive (1 queue)
Rotate queue after each push so newest element is at front.

push(1): queue=[1]
push(2): enqueue 2 → queue=[1,2]
         rotate: dequeue 1, enqueue 1 → queue=[2,1]
         (newest is at front)
push(3): enqueue 3 → queue=[2,1,3]
         rotate all but last? No — rotate ALL except the one just added.
         Actually: enqueue new element, then rotate (queue.size - 1) times.
         enqueue 3 → [2,1,3]
         dequeue 2, enqueue 2 → [1,3,2]
         dequeue 1, enqueue 1 → [3,2,1]  ← 3 is at front = top
pop()  → dequeue → 3, queue=[2,1]

Queue after push(3): [3, 2, 1]
                      ↑
                    front = top of stack

Visual:
Queue dequeue end ←→ enqueue end
               [3, 2, 1]
                ↑
              dequeue here = pop from stack

Approach 2: Make POP expensive (2 queues)
Simpler to understand, uses 2 queues.
Push: enqueue into q1. O(1)
Pop:  move all but last from q1 to q2, remove last element, swap q1 and q2.
```

## 4. Brute Force → Better → Optimal

### Approach 1 — Expensive Push (1 queue)
**Idea:** On push, enqueue the new element, then rotate the queue (n-1 times) so the newest element is at the front.

```
push(x):
    q.enqueue(x)
    for _ in range(len(q) - 1):
        q.enqueue(q.dequeue())

pop():
    return q.dequeue()

top():
    return q.front()

empty():
    return len(q) == 0
```

Push: O(n). Pop/Top: O(1).

### Approach 2 — Expensive Pop (2 queues)
**Idea:** Push is simple. On pop, transfer all but the last element to q2, then extract the last element from q1, then swap q1 and q2.

```
push(x):
    q1.enqueue(x)

pop():
    while len(q1) > 1:
        q2.enqueue(q1.dequeue())
    result = q1.dequeue()
    q1, q2 = q2, q1
    return result

top():
    while len(q1) > 1:
        q2.enqueue(q1.dequeue())
    result = q1.front()
    q2.enqueue(q1.dequeue())
    q1, q2 = q2, q1
    return result
```

Pop/Top: O(n). Push: O(1).

### Optimal (for LeetCode 225) — Use single deque or approach 1
Most interview solutions use Approach 1 (expensive push) with a single queue for clean implementation.

## 5. Pseudocode (Optimal — Approach 1, single queue)

```
class MyStack:
    queue = []
    
    push(x):
        queue.enqueue(x)
        for _ in range(len(queue) - 1):
            queue.enqueue(queue.dequeue())
    
    pop():
        return queue.dequeue()
    
    top():
        return queue.front()
    
    empty():
        return len(queue) == 0
```

## 6. Python Code

```python
from collections import deque

class MyStack:
    def __init__(self):
        self.q = deque()
    
    def push(self, x):
        self.q.append(x)
        # Rotate so newest element is at front
        for _ in range(len(self.q) - 1):
            self.q.append(self.q.popleft())
    
    def pop(self):
        return self.q.popleft()
    
    def top(self):
        return self.q[0]
    
    def empty(self):
        return len(self.q) == 0


# Test
s = MyStack()
s.push(1)
s.push(2)
s.push(3)

print(s.top())   # 3
print(s.pop())   # 3
print(s.top())   # 2
print(s.pop())   # 2
print(s.empty()) # False
print(s.pop())   # 1
print(s.empty()) # True

# Test single element
s2 = MyStack()
s2.push(5)
print(s2.top())  # 5
print(s2.pop())  # 5
print(s2.empty())# True
```

## 7. Complexity Table

| Approach       | Push | Pop  | Top  | Space | Notes                    |
|----------------|------|------|------|-------|--------------------------|
| Expensive Push | O(n) | O(1) | O(1) | O(n)  | Single queue, rotate     |
| Expensive Pop  | O(1) | O(n) | O(n) | O(n)  | Two queues, transfer     |

## 8. Edge Cases to Remember

- Empty stack pop — undefined behavior, problem guarantees valid operations
- Single element — push rotates 0 times (len-1=0), pop/top work fine
- Queue ordering after push — after each push, newest is at deque[0] (front)
- Top without pop — front of queue without removing
- Multiple pushes then pops — ordering maintained by rotation

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Implement Queue using Stacks          | Reverse: use 2 stacks to simulate queue FIFO        | LC 232     |
| Min Stack                             | Augment stack to support min in O(1)                | LC 155     |
| Valid Parentheses                     | Classic stack usage for matching                    | LC 20      |
| Evaluate Reverse Polish Notation      | Stack-based expression evaluation                   | LC 150     |
| Decode String                         | Stack for nested structure decoding                 | LC 394     |
| Daily Temperatures                    | Monotonic stack for temperatures                    | LC 739     |
| Design Circular Queue                 | Array-based circular buffer                         | LC 622     |
| Design Hit Counter                    | Queue for sliding window of hits                    | LC 362     |

## 10. The ONE Trick to Remember

**"After each push, rotate the queue (size-1) times — newest goes to front, making pop O(1)."**

A queue is FIFO but we need LIFO. The trick: after pushing the new element, dequeue-enqueue everything else, cycling it to the back. The new element "bubbles up" to the front. Front = top of stack. This gives you O(1) pop and top, at the cost of O(n) push. The single-queue solution is cleaner in implementation than the two-queue approach.
