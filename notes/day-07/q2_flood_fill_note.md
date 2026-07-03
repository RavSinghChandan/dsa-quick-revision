# Q2 — Flood Fill (2-min card) — LC 733

**Paint all pixels connected to (sr,sc) with the same original color to a new color.**

**Pattern:** DFS/BFS on Grid — spread to 4-directional neighbors matching original color

```
Image:          After floodFill(1,1, color=2):
1 1 1           2 2 2
1 1 0    →      2 2 0
1 0 0           2 0 0

DFS from (1,1) original=1: paint all connected 1s to 2
(1,1)→2, then (0,1)→2,(2,1)=0 stop,(1,0)→2,(1,2)=0 stop...
```

**The trick:** "Guard: if original==new_color return immediately (prevents infinite loop)."

```python
def floodFill(image, sr, sc, color):
    orig = image[sr][sc]
    if orig == color: return image
    def dfs(r,c):
        if not(0<=r<len(image) and 0<=c<len(image[0])): return
        if image[r][c] != orig: return
        image[r][c] = color
        dfs(r+1,c); dfs(r-1,c); dfs(r,c+1); dfs(r,c-1)
    dfs(sr,sc); return image
```

**Complexity:** Time O(m*n) | Space O(m*n)

**Same pattern solves:** Number of Islands (LC 200), Max Area Island (LC 695), Surrounded Regions (LC 130), Island Perimeter (LC 463), Count Sub Islands (LC 1905), Coloring a Border (LC 1034), Word Search (LC 79).
