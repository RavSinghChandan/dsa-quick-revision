# Q2 — Flood Fill (LeetCode 733)

## 1. What is this question actually asking?

Given a 2D image (grid of integers representing colors), a starting pixel (sr, sc), and a new color, perform a flood fill. Replace the color of the starting pixel and all pixels connected to it (4-directional) with the same original color, changing them all to the new color. This is exactly like the paint bucket tool in image editors.

## 2. Pattern

DFS/BFS on Grid — from starting cell, spread to all 4-directionally connected cells with the same original color

## 3. Understand with a Diagram

```
Image:
1 1 1
1 1 0
1 0 0

Starting pixel: (1,1), new color: 2
Original color at (1,1) = 1

DFS from (1,1): paint all connected '1's to '2'

(1,1) = 1 → paint to 2
  (0,1) = 1 → paint to 2
    (0,0) = 1 → paint to 2
      (1,0) = 1 → paint to 2
        (2,0) = 1 → paint to 2
    (0,2) = 1 → paint to 2
  (1,2) = 0 → stop
  (2,1) = 0 → stop

Result:
2 2 2
2 2 0
2 0 0

The '0's were never '1' so they don't get painted.

Key edge case:
If new_color == original_color → return image as-is (avoid infinite recursion)
```

## 4. Brute Force → Better → Optimal

### Only Approach — DFS/BFS
**Idea:** Record the original color at (sr, sc). DFS from (sr, sc), painting every connected cell with the same original color to the new color.

```
original = image[sr][sc]

if original == new_color: return image  // no change needed

function dfs(r, c):
    if out of bounds or image[r][c] != original: return
    image[r][c] = new_color
    dfs(r+1,c); dfs(r-1,c); dfs(r,c+1); dfs(r,c-1)

dfs(sr, sc)
return image
```

O(m*n) time, O(m*n) space (recursion stack).

**BFS variant:**
```
original = image[sr][sc]
if original == new_color: return image

queue = deque([(sr, sc)])
image[sr][sc] = new_color

while queue:
    r, c = queue.popleft()
    for (nr, nc) in [(r+1,c),(r-1,c),(r,c+1),(r,c-1)]:
        if valid and image[nr][nc] == original:
            image[nr][nc] = new_color
            queue.append((nr, nc))
```

## 5. Pseudocode (Optimal)

```
function floodFill(image, sr, sc, new_color):
    original = image[sr][sc]
    if original == new_color: return image
    
    dfs(image, sr, sc, original, new_color)
    return image

function dfs(image, r, c, original, new_color):
    if out of bounds or image[r][c] != original: return
    image[r][c] = new_color
    dfs(image, r+1, c, original, new_color)
    dfs(image, r-1, c, original, new_color)
    dfs(image, r, c+1, original, new_color)
    dfs(image, r, c-1, original, new_color)
```

## 6. Python Code

```python
def floodFill(image, sr, sc, color):
    original = image[sr][sc]
    if original == color:
        return image
    
    m, n = len(image), len(image[0])
    
    def dfs(r, c):
        if r < 0 or r >= m or c < 0 or c >= n:
            return
        if image[r][c] != original:
            return
        image[r][c] = color
        dfs(r+1, c)
        dfs(r-1, c)
        dfs(r, c+1)
        dfs(r, c-1)
    
    dfs(sr, sc)
    return image


# Test 1 — LeetCode example 1
image1 = [[1,1,1],[1,1,0],[1,0,0]]
print(floodFill(image1, 1, 1, 2))
# [[2,2,2],[2,2,0],[2,0,0]]

# Test 2 — same color (no change)
image2 = [[0,0,0],[0,0,0]]
print(floodFill(image2, 0, 0, 0))
# [[0,0,0],[0,0,0]] (unchanged)

# Test 3 — single cell
print(floodFill([[5]], 0, 0, 3))   # [[3]]

# Test 4 — starting at corner
image4 = [[1,1],[1,0]]
print(floodFill(image4, 0, 0, 9))
# [[9,9],[9,0]]
```

## 7. Complexity Table

| Approach  | Time    | Space   | Notes                         |
|-----------|---------|---------|-------------------------------|
| DFS       | O(m*n)  | O(m*n)  | Worst case: entire grid same color |
| BFS       | O(m*n)  | O(m*n)  | Queue-based, avoids deep stack |

## 8. Edge Cases to Remember

- new_color == original color — return immediately; otherwise infinite recursion (we keep repainting)
- Starting cell is alone (no neighbors with same color) — only that cell changes
- Entire grid same color — all cells repainted
- Single cell grid — just repaint that one cell
- Image is 1D (single row) — works fine with direction checks
- Starting outside the grid — problem guarantees valid (sr, sc)

## 9. Similar Questions

| Question                              | Where Pattern Applies                               | LeetCode # |
|---------------------------------------|-----------------------------------------------------|------------|
| Number of Islands                     | Count connected components (same DFS)               | LC 200     |
| Max Area of Island                    | DFS, count cells in component                       | LC 695     |
| Surrounded Regions                    | Flood fill from border (capture 'O's)               | LC 130     |
| Island Perimeter                      | Count border edges during DFS                       | LC 463     |
| Count Sub Islands                     | Check if sub-island exists in main                  | LC 1905    |
| Paint House                           | DP color assignment                                 | LC 256     |
| Word Search                           | DFS with backtracking on grid                       | LC 79      |
| Coloring A Border                     | Flood fill — only paint border cells                | LC 1034    |

## 10. The ONE Trick to Remember

**"Flood fill = DFS/BFS that only crosses into cells with the same original color — always check original ≠ new to avoid infinite loop."**

The algorithm is simple: DFS from start, paint cells matching the original color. The one critical edge case is when original == new color — skip the fill entirely (not because it would be wrong, but because you'd enter an infinite loop: the cell's color equals original, so you'd paint it, check again, paint again...). Guard against this first.
