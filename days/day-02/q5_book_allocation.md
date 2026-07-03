# Q5 — Book Allocation Problem

## 1. What is this question actually asking?

You have n books and m students. Each book has a certain number of pages. You must allocate all books to the students such that each student gets at least one book, books are allocated in contiguous order (student 1 gets books 1..k, student 2 gets books k+1..j, etc.), and you want to minimize the maximum number of pages any single student has to read. Think of it as dividing a playlist of songs between people — no skipping around, and you want the heaviest load to be as light as possible.

## 2. Pattern

Binary Search on Answer — binary search on the maximum pages any student reads, greedy check how many students are needed

## 3. Understand with a Diagram

```
Books:    [12, 34, 67, 90]    Students: 2

We binary search the "max pages" value:
Range: [max_single_book, sum_all_books] = [90, 203]

Can 2 students handle max=113?
  Student 1: 12, then 34 (total=46), then 67 (total=113) → OK
  Student 2: 90 (total=90) → OK
  Only 2 students needed → 113 is feasible ✓

Can 2 students handle max=100?
  Student 1: 12, 34 (total=46), 67 (total=113 > 100) → stop at 34
             12, 34 = 46
  Student 2: 67, 90 = 157 > 100 → need another student for 90
  → 3 students needed → 100 is infeasible ✗

Binary search:
lo=90, hi=203
mid=146: feasible → hi=145, answer=146
mid=117: feasible → hi=116, answer=117
mid=103: feasible → hi=102, answer=103
mid=96: infeasible → lo=97
...converges to 113

Visualization:
Books:  [12] [34] [67] [90]
              ↑
           split point for max=113:

Student 1: [12][34][67] → 12+34+67=113
Student 2: [90]         → 90
Max = 113 ← minimized!
```

## 4. Brute Force → Better → Optimal

### Brute Force — Try all partition points
**Idea:** Try all ways to split n books into m contiguous groups. For each split, compute max group sum, track minimum.

```
recursively try all (m-1) split points in n books
for each partition:
    max_pages = max(sum of each group)
    answer = min(answer, max_pages)
```

Why slow: C(n-1, m-1) partitions. Exponential for large n.

### Better — DP
**Idea:** dp[i][j] = minimum of the maximum pages when first j books are allocated to i students.

```
dp[i][j] = min over k from i-1 to j-1 of:
            max(dp[i-1][k], sum(books[k..j-1]))
```

Why better: O(m * n²) time. Better than brute but still slow for large inputs.

### Optimal — Binary Search on Answer
**Idea:** Binary search on the maximum pages any student reads. For a given limit, greedily assign books: keep adding books to the current student until adding the next book would exceed the limit, then start a new student. Count how many students are needed; if it's <= m, the limit is feasible.

```
lo = max(books)      // minimum possible max (must take largest book)
hi = sum(books)      // maximum possible (one student reads all)

while lo <= hi:
    mid = (lo + hi) // 2
    if studentsNeeded(books, mid) <= m:
        answer = mid
        hi = mid - 1
    else:
        lo = mid + 1

function studentsNeeded(books, max_pages):
    count = 1
    current = 0
    for pages in books:
        if current + pages > max_pages:
            count += 1
            current = pages
        else:
            current += pages
    return count
```

**Trace:**
```
books=[12,34,67,90], m=2
lo=90, hi=203

mid=146: students(146): 12+34+67=113<146, then 90=90<146 → 2 students ✓
  answer=146, hi=145

mid=117: students(117): 12+34+67=113<117, then 90<117 → 2 students ✓
  answer=117, hi=116

mid=103: students(103): 12+34=46, 46+67=113>103 → new student (count=2), 67+90=157>103
  → new student (count=3). 3>2 → infeasible ✗ → lo=104

mid=110: students(110): 12+34+67=113>110 → split after 34 (count=2, current=67)
         67+90=157>110 → count=3 → infeasible → lo=111

mid=113: students(113): 12+34+67=113<=113, then 90<=113 → 2 students ✓
  answer=113, hi=112

lo=113>hi=112 → return 113
```

## 5. Pseudocode (Optimal)

```
function bookAllocation(books[], m):
    if m > len(books): return -1  // more students than books
    
    lo = max(books)
    hi = sum(books)
    answer = hi
    
    while lo <= hi:
        mid = (lo + hi) // 2
        if studentsNeeded(books, mid) <= m:
            answer = mid
            hi = mid - 1
        else:
            lo = mid + 1
    
    return answer

function studentsNeeded(books[], max_pages):
    count = 1
    current_sum = 0
    for pages in books:
        if current_sum + pages > max_pages:
            count += 1
            current_sum = pages
        else:
            current_sum += pages
    return count
```

## 6. Python Code

```python
def students_needed(books, max_pages):
    count = 1
    current = 0
    for pages in books:
        if current + pages > max_pages:
            count += 1
            current = pages
        else:
            current += pages
    return count


def book_allocation(books, m):
    if m > len(books):
        return -1  # can't give each student a book
    
    lo = max(books)
    hi = sum(books)
    answer = hi
    
    while lo <= hi:
        mid = (lo + hi) // 2
        if students_needed(books, mid) <= m:
            answer = mid
            hi = mid - 1
        else:
            lo = mid + 1
    
    return answer


# Test 1 — standard example
print(book_allocation([12, 34, 67, 90], 2))   # 113

# Test 2 — 4 books, 4 students (each gets 1 book)
print(book_allocation([12, 34, 67, 90], 4))   # 90

# Test 3 — 1 student (reads all books)
print(book_allocation([12, 34, 67, 90], 1))   # 203

# Test 4 — equal pages
print(book_allocation([10, 10, 10, 10], 2))   # 20

# Test 5 — more students than books
print(book_allocation([5, 10], 5))             # -1
```

## 7. Complexity Table

| Approach          | Time          | Space | Notes                                 |
|-------------------|---------------|-------|---------------------------------------|
| Brute Force       | O(C(n-1,m-1)) | O(m)  | Exponential                           |
| DP                | O(m * n²)     | O(m*n)| Dynamic programming on partitions     |
| Binary Search     | O(n log S)    | O(1)  | S = sum of all books                  |

## 8. Edge Cases to Remember

- m > n — impossible, return -1 (can't give each student at least one book)
- m = 1 — one student reads all books, answer = sum(books)
- m = n — each student gets exactly one book, answer = max(books)
- One extremely large book — lo starts at that value
- All books have equal pages — answer = sum/m rounded up
- m = 0 — edge case, return -1 or handle per problem constraints

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Painter's Partition Problem           | Identical structure — painters instead of students  | GFG        |
| Split Array Largest Sum               | Same binary search + greedy check                   | LC 410     |
| Capacity to Ship Packages in D Days   | Same — minimize max ship capacity                   | LC 1011    |
| Aggressive Cows                       | Binary search — maximize minimum instead            | SPOJ       |
| Magnetic Force Between Balls          | Maximize minimum distance                           | LC 1552    |
| Minimum Time to Complete Trips        | Binary search on time, check trips                  | LC 2187    |
| Divide Chocolate                      | Minimize maximum, same structure                    | LC 1231    |
| EKO (SPOJ)                            | Binary search on saw height                         | SPOJ       |

## 10. The ONE Trick to Remember

**"Binary search from max-book to sum-all; use greedy count to check if limit works."**

The answer (minimum of maximum pages) must lie between the largest book (lower bound — one student must read that) and the total of all books (upper bound — one student reads everything). Binary search in this range. For each candidate answer, greedily count needed students — if we exceed the limit, start a new student. If students needed <= m, the answer is feasible.
