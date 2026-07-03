# DSA Quick Revision — Striver's 80 Questions (TakeUForward)

> 80 questions · 10 days · Every question explained so well you can recall it while walking.

---

## How to use this repo

### Two files per question

| File | Purpose | Time to read |
|------|---------|-------------|
| `days/day-XX/qN_name.md` | Full deep-dive: problem → diagram → brute/better/optimal → pseudocode → Python code → similar questions | 10–15 min (learn once) |
| `notes/day-XX/qN_name_note.md` | Ultra-short recall card: pattern, trick, skeleton code, related problems | 2 min (review daily) |

---

## Format of every solution file

Every `days/` file follows this exact structure:

```
1. What is this question actually asking?   ← plain English, no jargon
2. Pattern                                  ← the algorithm family it belongs to
3. Diagram                                  ← ASCII visual of the problem
4. Brute Force → Better → Optimal          ← all three with why each is better
5. Pseudocode                               ← language-free logic
6. Python Code                              ← clean, runnable, commented
7. Complexity table                         ← Time + Space for each approach
8. Edge Cases                               ← what to check in interview
9. Similar Questions table                  ← 8-10 more problems this trick solves
10. The ONE trick to remember               ← memory hook you can say out loud
```

---

## Format of every note file (2-min walk card)

```
1. Problem in one line
2. Pattern name
3. ASCII visual (tiny)
4. The trick — say it out loud
5. 3-line code skeleton
6. Complexity
7. 5 other problems this solves
```

---

## Day-wise Question Map

### Day 1 — Arrays & Greedy
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Minimum Platforms Required | Sort + Two Pointer |
| 2 | Job Sequencing Problem | Greedy + Sorting |
| 3 | N meetings in one room | Greedy (Activity Selection) |
| 4 | Fractional Knapsack | Greedy (ratio sort) |
| 5 | Chocolate Distribution Problem | Sliding Window on sorted array |

### Day 2 — Binary Search
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Find element in rotated sorted array | Modified Binary Search |
| 2 | Search in 2D matrix | Binary Search on flattened matrix |
| 3 | Median of two sorted arrays | Binary Search on partition |
| 4 | Aggressive Cows | Binary Search on answer |
| 5 | Book Allocation | Binary Search on answer |
| 6 | Painter's Partition | Binary Search on answer |

### Day 3 — Linked List
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Reverse a Linked List | Two pointer reverse |
| 2 | Detect cycle in Linked List | Floyd's cycle detection |
| 3 | Find starting point of cycle | Floyd's + math |
| 4 | Middle of Linked List | Slow-fast pointer |
| 5 | Merge two sorted lists | Two pointer merge |
| 6 | Remove Nth node from end | Two pointer with gap |

### Day 4 — Stack & Queue
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Next Greater Element | Monotonic Stack |
| 2 | Trapping Rainwater | Stack / Two Pointer |
| 3 | Largest Rectangle in Histogram | Monotonic Stack |
| 4 | Implement Stack using Queue | Queue simulation |
| 5 | LRU Cache | HashMap + Doubly Linked List |

### Day 5 — Binary Trees
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Height of Binary Tree | DFS recursion |
| 2 | Diameter of Binary Tree | DFS + track max |
| 3 | Maximum path sum | DFS post-order |
| 4 | Lowest Common Ancestor | DFS |
| 5 | Level order traversal | BFS (queue) |
| 6 | Zigzag level order | BFS with direction flag |
| 7 | Boundary traversal | Left + leaves + Right |

### Day 6 — Binary Search Tree + Heaps
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Validate BST | Inorder traversal |
| 2 | Kth smallest in BST | Inorder |
| 3 | Top K frequent elements | Min-Heap of size K |
| 4 | K closest points to origin | Max-Heap of size K |
| 5 | Merge K sorted arrays | Min-Heap |

### Day 7 — Graphs
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Number of islands | DFS/BFS flood fill |
| 2 | Flood fill | DFS |
| 3 | Detect cycle in directed graph | DFS + recursion stack |
| 4 | Topological sort | DFS / Kahn's BFS |
| 5 | Shortest path (BFS) | BFS on unweighted graph |
| 6 | Dijkstra's algorithm | Min-Heap + BFS |
| 7 | Bellman-Ford | Relax all edges V-1 times |
| 8 | Minimum spanning tree (Prim's) | Greedy + Min-Heap |

### Day 8 — Dynamic Programming Part 1
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Climbing Stairs | 1D DP (Fibonacci) |
| 2 | 0/1 Knapsack | 2D DP (include/exclude) |
| 3 | Subset Sum | 2D DP |
| 4 | Coin Change | Unbounded DP |
| 5 | Longest Common Subsequence | 2D DP |
| 6 | Longest Increasing Subsequence | DP + Binary Search |

### Day 9 — Dynamic Programming Part 2
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Edit Distance | 2D DP |
| 2 | Matrix Chain Multiplication | Interval DP |
| 3 | Egg Drop Problem | DP |
| 4 | Burst Balloons | Interval DP |
| 5 | Maximum sum rectangle in 2D matrix | Kadane's on columns |

### Day 10 — Tries + Strings + Mixed
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Implement Trie | Trie insert/search/prefix |
| 2 | Word Break | DP + Trie |
| 3 | KMP Pattern Matching | Failure function |
| 4 | Z algorithm | String matching |
| 5 | Count distinct substrings | Suffix Array / Trie |

---

## Folder Structure

```
dsa-quick-revision/
│
├── README.md                          ← this file — full map
│
├── days/                              ← DEEP STUDY files (read once, learn fully)
│   ├── day-01/
│   │   ├── q1_minimum_platforms.md
│   │   ├── q2_job_sequencing.md
│   │   └── ...
│   ├── day-02/
│   └── ...
│
└── notes/                             ← 2-MINUTE RECALL cards (read daily while walking)
    ├── day-01/
    │   ├── q1_minimum_platforms_note.md
    │   └── ...
    └── ...
```

---

## Pattern Reference — Master Cheatsheet

| Pattern | When to use | Questions it covers |
|---------|------------|---------------------|
| Sort + Two Pointer | "How many overlap at same time" | Platforms, Meeting Rooms, Merge Intervals |
| Binary Search on Answer | "Minimum maximum / maximum minimum" | Aggressive Cows, Book Allocation, Painter's |
| Monotonic Stack | "Next greater/smaller element" | NGE, Histogram, Trapping Rain |
| Floyd's Cycle | "Detect loop in linked list" | Cycle detect, Find start, Happy Number |
| Sliding Window | "Subarray/substring of size K" | Max sum subarray, Longest substring |
| BFS on graph | "Shortest path unweighted" | Level order, Flood fill, Rotten Oranges |
| DFS post-order | "Height, diameter, max path" | Binary tree problems |
| 2D DP (include/exclude) | "Knapsack-type choice" | 0/1 Knapsack, Subset Sum, LCS |
| Greedy (activity) | "Max tasks in given time" | Job sequencing, N meetings |
| Min-Heap size K | "Top K frequent / K closest" | Heaps problems |

---

*Started: 2026-07-03 | Based on Striver's TakeUForward DSA Quick Revision — 80 Questions*
