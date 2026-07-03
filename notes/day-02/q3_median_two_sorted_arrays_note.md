# Q3 — Median of Two Sorted Arrays (2-min card) — LC 4

**Find median of two sorted arrays combined in O(log(min(m,n))) — no merging.**

**Pattern:** Binary Search — partition smaller array, derive partition in larger array

```
nums1=[1,3]  nums2=[2]  total=3 → half=2

p1=1: nums1=[1 | 3]  p2=1: nums2=[2 | ∞]
L1=1, R1=3, L2=2, R2=∞
L1<=R2 (1<=∞)✓  L2<=R1 (2<=3)✓  → valid!
odd total → return max(L1,L2) = max(1,2) = 2
```

**The trick:** "Partition smaller array, p2 = half - p1; valid when max(lefts) <= min(rights)."

```python
if len(nums1) > len(nums2): nums1, nums2 = nums2, nums1
m, n = len(nums1), len(nums2); half = (m+n+1)//2
lo, hi = 0, m
while lo <= hi:
    p1 = (lo+hi)//2; p2 = half-p1
    L1 = nums1[p1-1] if p1 else -inf; R1 = nums1[p1] if p1<m else inf
    L2 = nums2[p2-1] if p2 else -inf; R2 = nums2[p2] if p2<n else inf
    if L1<=R2 and L2<=R1: return max(L1,L2) if (m+n)%2 else (max(L1,L2)+min(R1,R2))/2
    elif L1>R2: hi=p1-1
    else: lo=p1+1
```

**Complexity:** Time O(log(min(m,n))) | Space O(1)

**Same pattern solves:** Kth Smallest in Matrix (LC 378), Median Data Stream (LC 295), K Pairs Smallest Sums (LC 373), Split Array Largest Sum (LC 410), Kth Largest (LC 215), Min Max Dist Gas Station (LC 774), Search 2D Matrix (LC 74).
