# DSA Quick Revision — Striver 10-Day + AI Engineer Track

> 79 core DSA questions (Striver's 10-day revision) **+ 18 AI/Python interview questions (Days 11–12)** · Every question explained so well you can recall it while walking.
>
> **Days 1–10:** Striver's TakeUForward 80. **Day 11:** 6 AI-Engineer must-knows (parsing, TF-IDF, JSON). **Day 12:** 12 Python practice questions (dicts · lists · heaps · generators · files · JSON). Same format throughout — one deep file + one 2-min note per question.

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

## Day-wise Question Map (Striver 10-Day Revision)

### Day 1 — Arrays
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | 3 Sum | Sort + fix one + two pointer |
| 2 | Sort an Array of 0's, 1's and 2's | Dutch National Flag (3 pointers) |
| 3 | Kadane's Algorithm | Running sum, reset on negative |
| 4 | Majority Element-II (> n/3) | Boyer-Moore voting (2 candidates) |
| 5 | Find the Repeating and Missing Number | Sum & sum-of-squares (or XOR) |
| 6 | Maximum Product Subarray | Kadane tracking max & min |
| 7 | Reverse Pairs | Merge sort + counting |

### Day 2 — Binary Search
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Search in Rotated Sorted Array-II | Modified binary search + dup guard |
| 2 | Koko Eating Bananas | Binary search on the answer |
| 3 | Aggressive Cows | Binary search on answer (max min) |
| 4 | Median of Two Sorted Arrays | Binary search on partition |

### Day 3 — Sliding Window, Prefix Sum & Backtracking
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Count Subarrays with Given XOR K | Prefix XOR + hash map |
| 2 | Longest Substring Without Repeating Characters | Variable sliding window |
| 3 | Longest Substring With At Most K Distinct | Window + count map |
| 4 | Count Number of Nice Subarrays | atMost(k) − atMost(k−1) |
| 5 | Combination Sum II | Backtracking + dup skip |
| 6 | N Queen | Backtracking + conflict sets |

### Day 4 — Linked List
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Sort a Linked List of 0's, 1's and 2's | Three dummy chains |
| 2 | Check if Linked List is Palindrome | Mid + reverse half |
| 3 | Find the Starting Point in Linked List | Floyd's cycle + head reset |
| 4 | Reverse Linked List in Group of K | Segment reversal + relink |
| 5 | Flatten Linked List | Recursive merge on bottom |
| 6 | Find the Intersection Point of Y Linked List | Two-pointer switch |
| 7 | Clone a Linked List with Random Pointer | Interleave clones (or hash map) |

### Day 5 — Stack, Queue & Greedy
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Next Greater Element | Monotonic decreasing stack |
| 2 | Asteroid Collision | Stack simulation |
| 3 | Sum of Subarray Ranges | Σmax − Σmin monotonic stack |
| 4 | Trapping Rain Water | Two pointers leftMax/rightMax |
| 5 | Largest Rectangle in Histogram | Monotonic increasing stack |
| 6 | LRU Cache | Hash map + doubly linked list |
| 7 | Remove K Digits | Monotonic stack (greedy) |
| 8 | Minimum Number of Platforms | Sort + two-pointer sweep |
| 9 | N Meetings in One Room | Greedy by end time |
| 10 | Job Sequencing Problem | Greedy + latest-free-slot |

### Day 6 — Binary Tree
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Lowest Common Ancestor in Binary Tree | Post-order DFS |
| 2 | Bottom View of Binary Tree | BFS + horizontal distance map |
| 3 | Construct Binary Tree from Preorder & Inorder | Recursion + index map |
| 4 | Minimum Time to Burn Binary Tree | Parent map + BFS |
| 5 | Morris Inorder Traversal | Threaded traversal, O(1) space |

### Day 7 — BST & Heap
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Lowest Common Ancestor in BST | Value-guided walk |
| 2 | Inorder Successor and Predecessor | BST guided walk |
| 3 | Correct BST with Two Nodes Swapped | Inorder dip detection |
| 4 | Largest BST in Binary Tree | Post-order (min,max,size,isBST) |
| 5 | Two Sum in BST | Hash set / two-pointer inorder |
| 6 | Heapify Algorithm | Bottom-up sift-down |
| 7 | Heap Sort | Build heap + extract-max |
| 8 | Kth Largest Element in a Stream | Min-heap of size K |

### Day 8 — Strings & Graphs
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Rabin Karp | Rolling hash |
| 2 | Z Algorithm | Z-array with [L,R] window |
| 3 | KMP (LPS Array) | Failure function |
| 4 | Longest Happy Prefix | KMP lps[n-1] |
| 5 | Bipartite Graph | BFS/DFS 2-coloring |
| 6 | Rotten Oranges | Multi-source BFS |
| 7 | Topological Sort (Kahn's) | BFS on in-degrees |
| 8 | Course Schedule I | Directed cycle detection |
| 9 | Alien Dictionary | Precedence graph + topo sort |
| 10 | Dijkstra's Algorithm | Min-heap shortest path |
| 11 | Cheapest Flight Within K Stops | Bellman-Ford K+1 rounds |
| 12 | Floyd Warshall Algorithm | All-pairs DP over intermediates |
| 13 | Disjoint Set Union (DSU) | Path compression + union by rank |
| 14 | Kosaraju's Algorithm | Two-pass DFS (SCC) |

### Day 9 — Dynamic Programming
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Frog Jump with K Distances | 1-D DP over last K states |
| 2 | House Robber | Take/skip 1-D DP |
| 3 | Minimum Falling Path Sum | Grid DP (3 cells above) |
| 4 | Best Time to Buy and Sell Stock IV | DP over (txn, holding) |
| 5 | 0/1 Knapsack | Include/exclude DP |
| 6 | Partition Set → Min Absolute Diff | Subset-sum reachability |
| 7 | Longest Increasing Subsequence | Patience + binary search |
| 8 | Longest Common Subsequence | 2-D string DP |
| 9 | Edit Distance | 2-D DP (3 ops) |
| 10 | Matrix Chain Multiplication | Interval DP |

### Day 10 — Math, Bit Manipulation & Trie
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Print All Primes Till N | Sieve of Eratosthenes |
| 2 | Prime Factorization of a Number | Trial division to √n / SPF sieve |
| 3 | Single Number III | XOR all + bit split |
| 4 | Trie Implementation and Operations | Prefix tree |
| 5 | Trie Advanced Operations | Trie + wildcard DFS |
| 6 | Longest Word with All Prefixes | Trie DFS through is_end |
| 7 | Number of Distinct Substrings | Suffix Trie node count |
| 8 | Maximum XOR of Two Numbers | Binary (bit) Trie |

---

## 🤖 AI Engineer Track (Days 11–12)

> Not typical SDE questions — these show up in **AI / ML / LLM engineering** interviews because AI engineers work with datasets, embeddings, preprocessing pipelines and document parsing far more than traditional SDEs.

### Day 11 — 6 AI-Engineer Must-Knows
| Q | Problem | Pattern |
|---|---------|---------|
| 1 | Flatten Nested Dictionary | Recursion (DFS) + prefix |
| 2 | Flatten Nested List | Recursion / iterative stack |
| 3 | Log Parser | Streaming scan + Counter (+ regex) |
| 4 | Token Frequency Counter (Top-K) | Hash map + size-K min-heap |
| 5 | TF-IDF from scratch | TF × IDF over dicts (log formula) |
| 6 | Deep-Merge JSON / Transform | Parallel recursion (override wins) |

### Day 12 — 12 Python Practice (dicts · lists · heaps · generators · files · JSON)
| Q | Problem | Category | Pattern |
|---|---------|----------|---------|
| 1 | Two Sum | Dicts | Hash map "seen" / complement |
| 2 | Group Anagrams | Dicts | Group by canonical signature |
| 3 | Move Zeroes (in place) | Lists | Two pointer write-index |
| 4 | Merge Intervals | Lists | Sort + sweep merge |
| 5 | Kth Largest Element | Heaps | Min-heap of size K |
| 6 | Merge K Sorted Lists | Heaps | Min-heap k-way merge |
| 7 | Batch an Iterable | Generators | `yield` lazy chunks |
| 8 | Sliding Window | Generators | `deque(maxlen=K)` + yield |
| 9 | Huge-File Word Count | Files | Stream lines + Counter + Top-K |
| 10 | Deduplicate File Lines | Files | Stream + "seen" set (keep order) |
| 11 | Safe Nested JSON Access | JSON | Split path + safe descent |
| 12 | Load & Validate JSONL | JSON | Per-line `json.loads` in try/except |

> **All 24 code snippets in Days 11–12 are runnable and were executed to confirm output.**

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
| Recursion + prefix/parallel walk | "Nested dict/list/JSON — flatten or merge" | Flatten dict/list, Deep-merge, Safe path |
| Streaming + Counter | "File/log too big for RAM, count/group" | Log parser, Huge-file word count |
| Generator (`yield`) | "Chunks / windows / infinite stream, lazy" | Batch iterable, Sliding window |
| "Seen" set | "Dedup / has this appeared, keep order" | Dedup lines, Two Sum, Contains Duplicate |
| Per-line parse in try/except | "Load JSONL/CSV, skip bad rows" | Load & validate JSONL |

---

*Started: 2026-07-03 | Days 1–10 based on Striver's TakeUForward DSA 10-day revision (79 questions). Days 11–12 added as an AI-Engineer interview track (parsing, embeddings prep, Python data handling).*
