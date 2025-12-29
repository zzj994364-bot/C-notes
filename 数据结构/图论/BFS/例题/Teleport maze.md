https://atcoder.jp/contests/abc436/tasks/abc436_d

## **D - 传送迷宫（Teleport Maze）**
 **时间限制：** 2 秒
 **内存限制：** 1024 MiB
 **分值：** 400 分

------

### 题目描述

有一个由 **H 行、W 列**组成的迷宫网格。用
 $(i, j)$ 表示从上往下第 $i$ 行、从左往右第 $j$ 列的格子。
 每个格子的类型由字符 $S_{i,j}$ 给出，含义如下：

- `.` ：空格子
- `#` ：障碍物
- 小写英文字母（`a`–`z`）：传送格子

在迷宫中，你可以任意次、任意顺序执行以下两种操作：

1. **行走（Walk）**
    从当前格子移动到上下左右相邻的一个格子。
    但不能移动到障碍物格子，也不能走出迷宫边界。
2. **传送（Warp）**
    当你位于某个传送格子时，可以移动到任意一个**写有相同字母**的传送格子。

请判断是否可以从格子 $(1,1)$ 移动到格子 $(H,W)$。
 如果可以，输出所需的**最少操作次数**；否则输出 `-1`。

------

### 约束条件

- $1 \le H, W \le 1000$
- $H \times W \ge 2$
- $H, W$ 为整数
- $S_{i,j}$ 为 `.`、`#` 或小写英文字母
- $S_{1,1} \neq \#$
- $S_{H,W} \neq \#$

------

### 输入格式

```
H W
S_{1,1} S_{1,2} … S_{1,W}
⋮
S_{H,1} S_{H,2} … S_{H,W}
```

------

### 输出格式

如果可以从 $(1,1)$ 到达 $(H,W)$，输出最少操作次数；否则输出 `-1`。

------

### 样例 1

**输入**

```
3 4
..a.
####
ba#b
```

**输出**

```
5
```

**说明**
 可以按如下方式移动：

1. 从 $(1,1)$ 行走到 $(1,2)$
2. 从 $(1,2)$ 行走到 $(1,3)$
3. 从 $(1,3)$ 通过传送到 $(3,2)$
4. 从 $(3,2)$ 行走到 $(3,1)$
5. 从 $(3,1)$ 通过传送到 $(3,4)$

总操作数为 5，是最小值。

------

### 样例 2

**输入**

```
3 4
..a.
####
b.#b
```

**输出**

```
-1
```

------

### 样例 3

**输入**

```
4 4
xxxx
xxxx
xxxx
xxxx
```

**输出**

```
1
```

------

### 样例 4

**输入**

```
7 11
u..#y..#...
k..#.z.#.k.
iju#...#x..
###########
..x#.t.#..n
abc#y..#...
..z#..t#.y.
```

**输出**

```
12
```















































## 解题思路

**思考**

- bfs模板题的变种

**雷点**

- 不注意以下两点会超时

- 使用**map**记录dist会炸，map只适合**开不了数组**及**疏密**情况
- 同字母传送的**重复开销**

## 代码实现

```cpp
//最短路径问题
//相同字母连通
#include <bits/stdc++.h>
using namespace std;
//方向数组
int dr[] = {1, -1, 0, 0};
int dc[] = {0, 0, 1, -1};

vector<string> grid;
vector<vector<int>> dist;
vector<vector<pair<int, int>>> letters;
vector<bool> l_visited;

//bfs
int n, m;
int bfs(int sx, int sy) {
    dist[sx][sy] = 0;
    queue<pair<int, int>> q;
    q.push({sx, sy});
    
    
    while (!q.empty()) {
        pair<int, int> cur = q.front();
        q.pop();
        int x = cur.first;
        int y = cur.second;

        //找到
        if (x == n - 1 && y == m - 1) {
            return dist[x][y];
        }



        //若cur为字母，访问字母邻居
        if (grid[x][y] != '.' && grid[x][y] != '#' && !l_visited[grid[x][y] - 'a']) {
            l_visited[grid[x][y] - 'a'] = true; //阅后即焚
            for (auto co : letters[grid[x][y] - 'a']) {
                if (co != cur && dist[co.first][co.second] == -1) {
                    dist[co.first][co.second] = dist[x][y] + 1;
                    q.push({co.first, co.second});
                }
            }
        }
        //访问  邻居
        for (int i = 0; i < 4; i++) {
            int nx = x + dr[i];
            int ny = y + dc[i];
            if (nx < 0 || nx >= n || ny < 0 || ny >= m || grid[nx][ny] == '#' || dist[nx][ny] != -1) {
                continue;
            }

            dist[nx][ny] = dist[x][y] + 1;
            q.push({nx, ny});
        }
    }

    //没找到
    return -1;
}



int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cin >> n >> m;
    grid.resize(n);
    dist.resize(n, vector<int>(m));
    for (int i = 0; i < n; i++) cin >> grid[i];
    for (int x = 0; x < n; x++) {
        for (int y = 0; y < m; y++) {
            dist[x][y] = -1;
        }
    }
    letters.resize(26);
    l_visited.assign(26, false);
    for (int x = 0; x < n; x++) {
        for (int y = 0; y < m; y++) {
            if (grid[x][y] != '#' && grid[x][y] != '.') {
                letters[grid[x][y] - 'a'].push_back({x, y});
            }
        }
    }
    cout << bfs(0, 0) << endl;
    return 0;
}
```



