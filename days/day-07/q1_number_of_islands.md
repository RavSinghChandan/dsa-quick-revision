# Q1 — Number of Islands (LeetCode 200)

## 1. What is this question actually asking?

You're given a 2D grid of '1's (land) and '0's (water). Count the number of islands — a group of adjacent '1' cells (connected horizontally or vertically, not diagonally) that is surrounded by water. Each separate connected group of land cells is one island.

## 2. Pattern

DFS/BFS on Grid — for each unvisited '1', run DFS/BFS to mark the entire island as visited, increment count

## 3. Understand with a Diagram

```
Grid:
1 1 1 1 0
1 1 0 1 0
1 1 0 0 0
0 0 0 0 0

Starting at (0,0): DFS explores all connected '1's
Visited after DFS from (0,0):
V V V V 0
V V 0 V 0
V V 0 0 0
0 0 0 0 0

Island 1 (all connected) → count=1

No more unvisited '1's → answer=1

Another example:
1 1 0 0 0
1 1 0 0 0
0 0 1 0 0
0 0 0 1 1

Island 1: top-left cluster {(0,0),(0,1),(1,0),(1,1)} → count=1
Island 2: center {(2,2)} → count=2
Island 3: bottom-right {(3,3),(3,4)} → count=3

Answer: 3

DFS propagation from (0,0):
(0,0)→mark, explore (0,1),(1,0)
(0,1)→mark, explore (0,2)=0,(1,1),(0,0)=visited
(1,0)→mark, explore (2,0)=0,(0,0)=vis,(1,1)
(1,1)→mark...
All 4 cells of island 1 marked, count=1.
```

## 4. Brute Force → Better → Optimal

### Brute Force — Union-Find or repeated scanning
**Idea:** For each cell, try to connect it with adjacent cells using union-find. Count connected components.

Works but union-find has overhead for this simple case.

### Optimal — DFS or BFS
**Idea:** Iterate through every cell. When you find an unvisited '1', increment count and run DFS/BFS to mark all connected '1's as visited (either by changing to '0' or using a visited array). Continue scanning.

**DFS approach:**
```
count = 0
for each cell (r, c):
    if grid[r][c] == '1':
        count += 1
        dfs(r, c)  // marks all connected '1's as visited

function dfs(r, c):
    if out of bounds or grid[r][c] != '1': return
    grid[r][c] = '0'  // mark visited by sinking
    dfs(r-1, c); dfs(r+1, c)
    dfs(r, c-1); dfs(r, c+1)
```

O(m*n) time, O(m*n) space (call stack in worst case for skewed grids).

**BFS approach:**
```
count = 0
for each cell (r, c):
    if grid[r][c] == '1':
        count += 1
        queue = [(r, c)]
        grid[r][c] = '0'
        while queue:
            r0, c0 = queue.popleft()
            for each neighbor (nr, nc):
                if valid and grid[nr][nc] == '1':
                    grid[nr][nc] = '0'
                    queue.append((nr, nc))
```

## 5. Pseudocode (Optimal)

```
function numIslands(grid[][]):
    m = rows, n = cols
    count = 0
    
    for r from 0 to m-1:
        for c from 0 to n-1:
            if grid[r][c] == '1':
                count += 1
                dfs(grid, r, c)
    
    return count

function dfs(grid, r, c):
    if r < 0 or r >= m or c < 0 or c >= n: return
    if grid[r][c] != '1': return
    grid[r][c] = '0'  // sink the island
    dfs(grid, r+1, c)
    dfs(grid, r-1, c)
    dfs(grid, r, c+1)
    dfs(grid, r, c-1)
```

## 6. Python Code

```python
def numIslands(grid):
    if not grid:
        return 0
    
    m, n = len(grid), len(grid[0])
    count = 0
    
    def dfs(r, c):
        if r < 0 or r >= m or c < 0 or c >= n:
            return
        if grid[r][c] != '1':
            return
        grid[r][c] = '0'  # sink (mark visited)
        dfs(r+1, c)
        dfs(r-1, c)
        dfs(r, c+1)
        dfs(r, c-1)
    
    for r in range(m):
        for c in range(n):
            if grid[r][c] == '1':
                count += 1
                dfs(r, c)
    
    return count


# Test 1 — LeetCode example 1
grid1 = [
    ['1','1','1','1','0'],
    ['1','1','0','1','0'],
    ['1','1','0','0','0'],
    ['0','0','0','0','0']
]
print(numIslands(grid1))   # 1

# Test 2 — LeetCode example 2
grid2 = [
    ['1','1','0','0','0'],
    ['1','1','0','0','0'],
    ['0','0','1','0','0'],
    ['0','0','0','1','1']
]
print(numIslands(grid2))   # 3

# Test 3 — all water
print(numIslands([['0','0'],['0','0']]))   # 0

# Test 4 — all land (one island)
print(numIslands([['1','1'],['1','1']]))   # 1

# Test 5 — single cell
print(numIslands([['1']]))   # 1
print(numIslands([['0']]))   # 0
```

## 7. Complexity Table

| Approach      | Time    | Space   | Notes                               |
|---------------|---------|---------|-------------------------------------|
| DFS           | O(m*n)  | O(m*n)  | Stack depth up to m*n (skewed)      |
| BFS           | O(m*n)  | O(min(m,n)) | Queue width bounded by grid dim |
| Union-Find    | O(m*n α(m*n)) | O(m*n) | α is inverse Ackermann ≈ O(1) |

## 8. Edge Cases to Remember

- All water — return 0
- All land — return 1
- Single cell '1' — return 1
- Grid with single row/column — works fine with direction checks
- Very large grid — BFS preferred over DFS (avoids stack overflow from recursion)
- Original grid gets modified (sinking) — if you can't modify input, use a separate visited set
- Diagonal not connected — only 4 directions, not 8

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Max Area of Island                    | Same DFS/BFS, track size, return max                | LC 695     |
| Flood Fill                            | Same DFS/BFS, replace color                         | LC 733     |
| Surrounded Regions                    | BFS from border, mark safe regions                  | LC 130     |
| Pacific Atlantic Water Flow           | Multi-source BFS from each ocean                    | LC 417     |
| Number of Provinces                   | Connected components on adjacency matrix            | LC 547     |
| Count Connected Components            | Union-Find or DFS on graph edges                    | LC 323     |
| Word Search                           | DFS on grid with backtracking                       | LC 79      |
| Rotten Oranges                        | Multi-source BFS for minimum time                   | LC 994     |

## 10. The ONE Trick to Remember

**"Every unvisited '1' is a new island — DFS from it and sink all connected land."**

When you hit a '1' that hasn't been visited, you've found a new island. Increment count, then DFS in all 4 directions, turning each '1' to '0' as you go (sinking the island so you don't recount it). After DFS returns, continue scanning. Each cell is visited at most twice (once during scan, once during DFS), so the total time is O(m*n).
