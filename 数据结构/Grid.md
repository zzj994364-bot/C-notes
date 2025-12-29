### 要点：

- **0-based**，如果输入数据是1-based需要-1
- 基于**0-based**进行边界检查

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

/*=============================================================================
                            Grid (网格/矩阵) 数据结构模板
=============================================================================*/

//grid - vector索引代表row,string索引代表column
vector<string> grid;

//visited
vector<vector<int>> visited;

// 方向数组 - 四个方向（上下左右）
int dx[] = {-1, 1, 0, 0};
int dy[] = {0, 0, -1, 1};

// 方向数组 - 八个方向（包括对角线）
int dx8[] = {-1, -1, -1, 0, 0, 1, 1, 1};
int dy8[] = {-1, 0, 1, -1, 1, -1, 0, 1};

// 判断坐标是否合法
bool isValid(int x, int y, int rows, int cols) {
    return x >= 0 && x < rows && y >= 0 && y < cols;
}

/*-----------------------------------------------------------------------------
                            1. Grid的DFS遍历
-----------------------------------------------------------------------------*/
void dfs(vector<vector<int>>& grid, vector<vector<bool>>& visited, 
         int x, int y) {
    int rows = grid.size();
    int cols = grid[0].size();
    
    // 标记当前位置已访问
    visited[x][y] = true;
    
    // 处理当前格子
    cout << "访问: (" << x << ", " << y << ") = " << grid[x][y] << endl;
    
    // 遍历四个方向
    for (int i = 0; i < 4; i++) {
        int nx = x + dx[i];
        int ny = y + dy[i];
        
        // 检查新位置是否合法且未访问
        if (isValid(nx, ny, rows, cols) && !visited[nx][ny]) {
            // 可以添加其他条件，比如grid[nx][ny] == 1
            dfs(grid, visited, nx, ny);
        }
    }
}

/*-----------------------------------------------------------------------------
                            2. Grid的BFS遍历
-----------------------------------------------------------------------------*/
void bfs(vector<vector<int>>& grid, int startX, int startY) {
    int rows = grid.size();
    int cols = grid[0].size();
    
    vector<vector<bool>> visited(rows, vector<bool>(cols, false));
    queue<pair<int, int>> q;
    
    // 起点入队
    q.push({startX, startY});
    visited[startX][startY] = true;
    
    while (!q.empty()) {
        auto [x, y] = q.front();
        q.pop();
        
        // 处理当前格子
        cout << "访问: (" << x << ", " << y << ") = " << grid[x][y] << endl;
        
        // 遍历四个方向
        for (int i = 0; i < 4; i++) {
            int nx = x + dx[i];
            int ny = y + dy[i];
            
            if (isValid(nx, ny, rows, cols) && !visited[nx][ny]) {
                visited[nx][ny] = true;
                q.push({nx, ny});
            }
        }
    }
}

/*-----------------------------------------------------------------------------
                        3. Flood Fill (洪水填充算法)
-----------------------------------------------------------------------------*/
void floodFill(vector<vector<int>>& grid, int x, int y, 
               int oldColor, int newColor) {
    int rows = grid.size();
    int cols = grid[0].size();
    
    // 边界检查
    if (!isValid(x, y, rows, cols) || grid[x][y] != oldColor) {
        return;
    }
    
    // 填充当前格子
    grid[x][y] = newColor;
    
    // 递归填充四个方向
    for (int i = 0; i < 4; i++) {
        int nx = x + dx[i];
        int ny = y + dy[i];
        floodFill(grid, nx, ny, oldColor, newColor);
    }
}

/*-----------------------------------------------------------------------------
                        4. 统计岛屿数量 (连通块)
-----------------------------------------------------------------------------*/
void dfsIsland(vector<vector<int>>& grid, int x, int y) {
    int rows = grid.size();
    int cols = grid[0].size();
    
    // 越界或者是水域，返回
    if (!isValid(x, y, rows, cols) || grid[x][y] == 0) {
        return;
    }
    
    // 标记为已访问（变成水域）
    grid[x][y] = 0;
    
    // 继续搜索四个方向
    for (int i = 0; i < 4; i++) {
        dfsIsland(grid, x + dx[i], y + dy[i]);
    }
}

int countIslands(vector<vector<int>>& grid) {
    int rows = grid.size();
    if (rows == 0) return 0;
    int cols = grid[0].size();
    
    int count = 0;
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == 1) {
                count++;
                dfsIsland(grid, i, j);
            }
        }
    }
    return count;
}

/*-----------------------------------------------------------------------------
                        5. 最短路径 (BFS求距离)
-----------------------------------------------------------------------------*/
int shortestPath(vector<vector<int>>& grid, 
                 pair<int,int> start, pair<int,int> end) {
    int rows = grid.size();
    int cols = grid[0].size();
    
    // 距离数组
    vector<vector<int>> dist(rows, vector<int>(cols, -1));
    queue<pair<int, int>> q;
    
    q.push(start);
    dist[start.first][start.second] = 0;
    
    while (!q.empty()) {
        auto [x, y] = q.front();
        q.pop();
        
        // 到达终点
        if (x == end.first && y == end.second) {
            return dist[x][y];
        }
        
        // 四个方向扩展
        for (int i = 0; i < 4; i++) {
            int nx = x + dx[i];
            int ny = y + dy[i];
            
            // 检查是否可以走（0表示可走，1表示障碍物）
            if (isValid(nx, ny, rows, cols) && 
                grid[nx][ny] == 0 && dist[nx][ny] == -1) {
                dist[nx][ny] = dist[x][y] + 1;
                q.push({nx, ny});
            }
        }
    }
    
    return -1; // 无法到达
}

/*-----------------------------------------------------------------------------
                        6. 最大岛屿面积
-----------------------------------------------------------------------------*/
int dfsArea(vector<vector<int>>& grid, int x, int y) {
    int rows = grid.size();
    int cols = grid[0].size();
    
    if (!isValid(x, y, rows, cols) || grid[x][y] == 0) {
        return 0;
    }
    
    grid[x][y] = 0; // 标记已访问
    int area = 1;
    
    // 累加四个方向的面积
    for (int i = 0; i < 4; i++) {
        area += dfsArea(grid, x + dx[i], y + dy[i]);
    }
    
    return area;
}

int maxAreaOfIsland(vector<vector<int>>& grid) {
    int rows = grid.size();
    if (rows == 0) return 0;
    int cols = grid[0].size();
    
    int maxArea = 0;
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == 1) {
                maxArea = max(maxArea, dfsArea(grid, i, j));
            }
        }
    }
    return maxArea;
}

/*-----------------------------------------------------------------------------
                        7. 边界填充（从边界开始的DFS）
-----------------------------------------------------------------------------*/
void dfsBoundary(vector<vector<int>>& grid, int x, int y) {
    int rows = grid.size();
    int cols = grid[0].size();
    
    if (!isValid(x, y, rows, cols) || grid[x][y] != 0) {
        return;
    }
    
    grid[x][y] = 2; // 标记为边界连通区域
    
    for (int i = 0; i < 4; i++) {
        dfsBoundary(grid, x + dx[i], y + dy[i]);
    }
}

// 找出被包围的区域（类似围棋的死子）
void captureRegions(vector<vector<int>>& grid) {
    int rows = grid.size();
    if (rows == 0) return;
    int cols = grid[0].size();
    
    // 从边界开始DFS，标记所有边界连通的0
    for (int i = 0; i < rows; i++) {
        dfsBoundary(grid, i, 0);
        dfsBoundary(grid, i, cols - 1);
    }
    for (int j = 0; j < cols; j++) {
        dfsBoundary(grid, 0, j);
        dfsBoundary(grid, rows - 1, j);
    }
    
    // 遍历整个grid，0变成1（被包围），2变回0（边界连通）
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == 0) grid[i][j] = 1;
            else if (grid[i][j] == 2) grid[i][j] = 0;
        }
    }
}

/*-----------------------------------------------------------------------------
                                主函数示例
-----------------------------------------------------------------------------*/
int main() {
    // 示例1: 基本DFS遍历
    cout << "=== 示例1: DFS遍历 ===" << endl;
    vector<vector<int>> grid1 = {
        {1, 2, 3},
        {4, 5, 6},
        {7, 8, 9}
    };
    vector<vector<bool>> visited1(3, vector<bool>(3, false));
    dfs(grid1, visited1, 0, 0);
    
    // 示例2: 统计岛屿数量
    cout << "\n=== 示例2: 岛屿数量 ===" << endl;
    vector<vector<int>> grid2 = {
        {1, 1, 0, 0, 0},
        {1, 1, 0, 0, 0},
        {0, 0, 1, 0, 0},
        {0, 0, 0, 1, 1}
    };
    cout << "岛屿数量: " << countIslands(grid2) << endl;
    
    // 示例3: 最大岛屿面积
    cout << "\n=== 示例3: 最大岛屿面积 ===" << endl;
    vector<vector<int>> grid3 = {
        {1, 1, 0, 0, 0},
        {1, 1, 0, 0, 0},
        {0, 0, 1, 0, 0},
        {0, 0, 0, 1, 1}
    };
    cout << "最大面积: " << maxAreaOfIsland(grid3) << endl;
    
    // 示例4: 最短路径
    cout << "\n=== 示例4: 最短路径 ===" << endl;
    vector<vector<int>> grid4 = {
        {0, 0, 0, 0},
        {0, 1, 1, 0},
        {0, 0, 0, 0},
        {0, 0, 1, 0}
    };
    int dist = shortestPath(grid4, {0, 0}, {3, 3});
    cout << "最短距离: " << dist << endl;
    
    return 0;
}

/*
=============================================================================
                            Grid常用技巧总结
=============================================================================

1. 坐标系统:
   - grid[i][j]: i是行(row)，j是列(column)
   - (x, y): x通常表示行，y表示列

2. 方向数组:
   - dx[], dy[]: 简化方向遍历的代码
   - 四方向: 上下左右
   - 八方向: 包括对角线

3. 边界检查:
   - 0 <= x < rows && 0 <= y < cols
   - 封装成isValid函数提高代码可读性

4. 访问标记:
   - 使用visited数组
   - 或直接修改grid值（原地标记）

5. 常见问题类型:
   - 连通性问题: DFS/BFS
   - 最短路径: BFS
   - 区域填充: Flood Fill
   - 岛屿问题: DFS计数
   - 边界问题: 从边界开始搜索

6. 时间复杂度:
   - DFS/BFS遍历: O(rows * cols)
   - 每个格子最多访问一次
*/
```

