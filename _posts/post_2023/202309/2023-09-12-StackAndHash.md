---
layout: post
title: Stack, Hash에 대해서
tags: [python]
categories: [python]
sitemap:
  changefreq: daily
  priority : 1.0
---

리스트를 사용하면 Q(n)이고 딕셔너리의 키(key) 값을 사용하면 O(1)이다.  

```python
nums_list = [1, 5, 10, 6, 55]
nums_dict = {1: True, 5: True, 10: True, 6: True, 55: True}

6 in num_list # => O(n)
6 in num_dict # => O(1)
```

인접 리스트(adjacency list)
```python
graph = {
    1: [3,5],
    2: [4,5]
    3: [1,5]
}
```

BFS 코드

```python
def bfs(graph, start_va):
    q = deque()
    q.append(start_v)
    visited = {start_v: True}

    while q:
        cur_v = q.popleft()
        for next_v in graph[cur_v]:
            if next_v not in visited:
                q.append(next_v)
                visited[next_v] = True
graph = {
    0: [1,3,6,],
    1: [0, 3],
    2: [3],
    3: [0,1,2,7]
    4: [5],

}
bfs(graph, start_v=0)
```

DFS
```python
from typing import List
class Solution:
    def __init__(self):
        self.moving = {
            'up':[-1,0],
            'down':[1,0],
            'left':[0,-1],
            'right':[0,1]
        }
        
    
    def dfs(self, row, col):
        self.check_isLand[row][col] = True
        for dirction in self.moving.keys():
            moving_row, moving_col = self.moving[dirction]
            next_row = row + moving_row
            next_col = col + moving_col
            if self.isGridInside(next_row, next_col):
                if self.grid[next_row][next_col] == '1' and self.check_isLand[next_row][next_col] == False:
                    self.dfs(next_row, next_col)

    def numIslands(self, grid: List[List[str]]) -> int:
        self.answer = 0
        self.grid = grid.copy()
        self.row_len, self.col_len = len(self.grid), len(self.grid[0])
        self.check_isLand = [[False] * self.col_len for _ in range(self.row_len)]
        for row in range(self.row_len):
            for col in range(self.col_len):
                if self.grid[row][col] == '1' and self.check_isLand[row][col] == False:
                    self.dfs(row, col)
                    self.answer += 1
        return self.answer
        
    def isGridInside(self, next_row, next_col):
        return 0 <= next_row < self.row_len and 0 <= next_col < self.col_len and self.grid[next_row][next_col] == '1'
        
s=Solution()
s.numIslands(grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
])

```



----

목차
- toc
{: toc }

----  

