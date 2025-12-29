https://codeforces.com/contest/769/problem/C

## C. 迷宫中的环

每个测试点的时间限制：15 秒

内存限制：256 兆字节

机器人处在一个大小为 $n \times m$ 的矩形迷宫中。迷宫中的每个格子要么是空的，要么被障碍物占据。机器人可以在相邻格子之间向左（符号 "L"）、向右（符号 "R"）、向上（符号 "U"）或向下（符号 "D"）移动。机器人只能移动到空闲的格子中。最初，机器人位于一个空闲格子内。

你的任务是找到一条**字典序最小**的机器人路径，其长度**恰好**为 $k$，该路径从机器人初始位置出发并回到该位置。允许机器人多次访问任何格子（包括起点）。

机器人的路径以由符号 "L"、"R"、"U" 和 "D" 组成的字符串形式给出。例如，如果机器人先向下移动，然后向左，接着向右，最后向上，那么他的路径被记录为 "DLRU"。

在本题中，你**不需要**最小化路径长度。你需要找到满足上述要求且字典序最小（按照字典字母顺序）的字符串。

### 输入

第一行包含三个整数 $n, m$ 和 $k$ ($1 \le n, m \le 1000, 1 \le k \le 10^6$) —— 迷宫的大小和循环路径的长度。

接下来的 $n$ 行每行包含 $m$ 个符号 —— 迷宫的描述。如果符号等于 "."，则当前格子为空。如果符号等于 "*"，则当前格子被障碍物占据。如果符号等于 "X"，则表示初始时机器人在该格子中，且该格子为空。保证符号 "X" 在迷宫中恰好出现一次。

### 输出

打印字典序最小且长度恰好为 $k$ 的机器人路径，该路径从机器人初始位置出发并结束。如果不存在这样的路径，打印 "IMPOSSIBLE"（不带引号）。

### 示例

**输入 1**

```
2 3 2
.**
X..
```

**输出 1**

```
RL
```

**输入 2**

```
5 6 14
..***.
*...X.
..*...
..*.**
....*.
```

**输出 2**

```
DLDDLLLRRRUURU
```

**输入 3**

```
3 3 4
***
*X*
***
```

**输出 3**

```
IMPOSSIBLE
```

### 说明

在第一个示例中，机器人存在两条长度为 2 的循环路径 —— "UD" 和 "RL"。第二条路径的字典序更小。

在第二个示例中，机器人的移动路线为：下，左，下，下，左，左，左，右，右，右，上，上，右，上。

在第三个示例中，机器人无法移动到相邻格子，因为它们都被障碍物占据了。











































## 解题思路

**核心思想**

- **字典序最小**天然适合**贪心**（局部最优）
- 贪心模拟路径会产生一个问题：如何**确保局部最优落脚点可返回**？
- 利用**bfs**预处理各点到起点的距离来判断是否可返回

## 代码实现

```cpp
/*
使用bfs确立最优路径的边界
嵌套循环模拟每一步 和 该步的最优解
要求字典序最小天然适合greedy，局部最优
*/
#include <bits/stdc++.h>
using namespace std;

int sx, sy;
int n, m, k;
//grid
vector<string> grid;


//四向数组  D L R U
int dr[] = {1, 0, 0, -1};
int dc[] = {0, -1, 1, 0};
string direction = "DLRU";
                  //0123                  

//bfs预处理每一个点到起点的位置
vector<vector<int>> dist;
void bfs(int sx, int sy) {
    dist[sx][sy] = 0;
    queue<pair<int, int>> q;
    q.push({sx, sy});

    while (!q.empty()) {
        pair<int, int> cur_node = q.front();
        q.pop();
        int ox = cur_node.first;
        int oy = cur_node.second;
        
        for (int i = 0; i < 4; i++) {
            int nx = ox + dr[i];
            int ny = oy + dc[i];
            if (nx < 0 || nx >= n || ny < 0 || ny >= m || grid[nx][ny] == '*' || dist[nx][ny] != -1) continue;
            dist[nx][ny] = dist[ox][oy] + 1;
            q.push({nx, ny});
        }
    }
}


int main() {
    cin >> n >> m >> k;
    //0based
    grid.resize(n);
    dist.assign(n, vector<int>(m, -1));
    for (int i = 0; i < n; i++) cin >> grid[i];

    //k为奇则天然错误
    if (k % 2 != 0) {
        cout << "IMPOSSIBLE" << endl;
        return 0;
    }
    //找sx sy
    for (int x = 0; x < n; x++) {
        for (int  y = 0; y < m; y++) {
            if (grid[x][y] == 'X') {
                sx = x;
                sy = y;
            }
        }
    }

    //bfs预处理
    bfs(sx, sy);

    int curx = sx, cury = sy;
    string ans;
    bool moved;
    //模拟每一步
    for (int i = 1; i <= k; i++) {
        moved = false;
        for (int j = 0; j < 4; j++) {
            int nx = curx + dr[j];
            int ny = cury + dc[j];
            if (nx < 0 || nx >= n || ny < 0 || ny >= m || grid[nx][ny] == '*') continue;
            if (dist[nx][ny] > (k - i)) continue;
            // cout << nx << ' ' << ny << '\n';
            ans.push_back(direction[j]);
            moved = true;
            curx = nx;
            cury = ny;
            break;
        }
    }
    if (!moved) {
        cout << "IMPOSSIBLE" << endl;
        return 0;
    }
    cout << ans << endl;
    return 0;

}

```

