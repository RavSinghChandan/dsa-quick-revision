# Q4 — Implement Stack Using Queue (2-min card) — LC 225

**Build a LIFO stack using only queue operations.**

**Pattern:** Single Queue — make push expensive; rotate queue after each push so newest is at front

```
push(1): q=[1]
push(2): q=[1,2] → rotate 1 time → q=[2,1]  (2 at front = top)
push(3): q=[2,1,3] → rotate 2 times → q=[3,2,1]  (3 at front = top)

pop()  → dequeue from front → 3
top()  → peek front → 2
```

**The trick:** "Rotate queue (size-1) times after push — newest goes to front, making it the stack top."

```python
from collections import deque
class MyStack:
    def __init__(self): self.q = deque()
    def push(self, x):
        self.q.append(x)
        for _ in range(len(self.q)-1):
            self.q.append(self.q.popleft())
    def pop(self): return self.q.popleft()
    def top(self): return self.q[0]
    def empty(self): return not self.q
```

**Complexity:** Push O(n) | Pop O(1) | Top O(1) | Space O(n)

**Same pattern solves:** Implement Queue Using Stacks (LC 232), Min Stack (LC 155), Valid Parentheses (LC 20), Evaluate RPN (LC 150), Decode String (LC 394), Daily Temperatures (LC 739), Design Circular Queue (LC 622).
