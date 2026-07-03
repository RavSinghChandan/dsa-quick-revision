# Q1 — Number of Islands (2-min card) — LC 200

**Count connected groups of '1's in a 2D grid (4-directional connectivity).**

**Pattern:** DFS/BFS on Grid — find unvisited '1', increment count, DFS to sink all connected land

```
Grid:             After DFS from (0,0):
1 1 0 0 0         0 0 0 0 0
1 1 0 0 0    →    0 0 0 0 0   (island 1 sunk, count=1)
0 0 1 0 0         0 0 1 0 0   ← island 2 found (count=2)
0 0 0 1 1         0 0 0 1 1   ← island 3 found (count=3)

Answer = 3
```

**The trick:** "Every unvisited '1' is a new island — DFS and sink all connected land before continuing."

```python
def dfs(r, c):
    if not (0<=r<m and 0<=c<n) or grid[r][c] != '1': return
    grid[r][c] = '0'
    dfs(r+1,c); dfs(r-1,c); dfs(r,c+1); dfs(r,c-1)

count = 0
for r in range(m):
    for c in range(n):
        if grid[r][c]=='1': count+=1; dfs(r,c)
```

**Complexity:** Time O(m*n) | Space O(m*n)

**Same pattern solves:** Max Area Island (LC 695), Flood Fill (LC 733), Surrounded Regions (LC 130), Pacific Atlantic (LC 417), Number Provinces (LC 547), Word Search (LC 79), Rotten Oranges (LC 994), Count Connected Components (LC 323).
