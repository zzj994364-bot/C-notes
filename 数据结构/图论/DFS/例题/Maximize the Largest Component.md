https://codeforces.com/contest/1985/problem/H1

## **H1. 最大化最大连通分量（简单版）**

每次测试时间限制：2 秒

每次测试内存限制：512 兆字节

**注意**：简单版和困难版实际上是不同的问题，所以请完整且仔细地阅读两个问题的题面。两个版本之间唯一的区别在于操作方式。

Alex 有一个由 . 和 # 字符组成的 $n$ 行 $m$ 列的网格。

如果一组 # 单元格中的任意一个单元格，都可以通过仅移动到该集合中与其共享公共边（即上下左右相邻）的另一个单元格，来到达该集合中的任何其他单元格，那么这组 # 单元格就构成了一个连通分量。连通分量的大小就是该集合中单元格的数量。

在**一次操作**中，Alex 可以选择任意一行 $r$ ($1 \le r \le n$) **或者** 任意一列 $c$ ($1 \le c \le m$)，然后将第 $r$ 行或第 $c$ 列中的**每一个**单元格都变为 `#`。

请帮助 Alex 找出在执行**至多一次**操作后，他能得到的最大的 `#` 连通分量的最大可能大小。

### **输入 (Input)**

输入的第一行包含一个整数 $t$ ($1 \le t \le 10^4$) — 测试用例的数量。

每个测试用例的第一行包含两个整数 $n$ 和 $m$ ($1 \le n \cdot m \le 10^6$) — 网格的行数和列数。

接下来的 $n$ 行每行包含 $m$ 个字符。每个字符要么是 `.`，要么是 `#`。

保证所有测试用例中 $n \cdot m$ 的总和不超过 $10^6$。

### **输出 (Output)**

对于每个测试用例，输出一个整数 — Alex 能达到的最大的 `#` 连通分量的最大可能大小。

### **样例 (Example)**

**Input**

Plaintext

```
6
1 1
.
4 2
..
#.
#.
.#
3 5
.#.#.
..#..
.#.#.
5 5
#...#
....#
#...#
.....
...##
6 6
.#..#.
#..#..
.#...#
#.#.#.
.#.##.
###..#
6 8
..#....#
.####.#.
###.#..#
.##.#.##
.#.##.##
#..##.#.
```

**Output**

Plaintext

```
1
6
9
11
15
30
```

### **提示 (Note)**

在第二个测试用例中，Alex 最优的做法是将第 2 列的所有单元格设为 `#`。这样做会导致最大的 `#` 连通分量大小为 6。

在第三个测试用例中，Alex 最优的做法是将第 2 行的所有单元格设为 `#`。这样做会导致最大的 `#` 连通分量大小为 9。

在第四个测试用例中，Alex 最优的做法是将第 4 行的所有单元格设为 `#`。这样做会导致最大的 `#` 连通分量大小为 11。





















































## 解题思路

**核心思想**

- **模拟每行每列**处理后的最大连通块大小并取最大值

**实现难点**

- 连通块的统计：需要给每个'#'分配comp_id和comp_sz

  ```cpp
  vector<int> comp_id;
  vector<int> comp_sz;
  /*
  comp_id为全局变量，comp_sz储存对应id的sz
  此思路只需要dfs一个连通块的第一个'#'
  dfs过程中就会把连通块内的所有'#'赋予id
  并递归回来此连通块大小
  同时将comp_id初始化为-1，承担visited的作用
  */
  ```

## 代码实现

```cpp
//point：非递归dfs(手动维护栈)     用comp_id作为桥梁沟通点和所在连通块的sz，并用-1承担visited的作用
#include <bits/stdc++.h>
using namespace std;

int n, m;
//grid   comp_id    comp_sz
vector<string> grid;
vector<vector<int>> comp_id; //初始为0， -1代表未访问，  comp_id承担了visited的作用
vector<int> comp_sz;

//方向数组
int dr[] = {1, -1, 0, 0};
int dc[] = {0, 0, 1, -1};

//统计连通块sz顺便打上id
int dfs(int x, int y, int cur_id) {
    int count = 0;
    
    vector<pair<int, int>> st; //st用来储存合法的点,st.back()是目前要结算的点
    st.push_back({x, y});
    comp_id[x][y] = cur_id;
    while (!st.empty()) {
        pair<int, int> cur_pos = st.back();  //结算目前的点(st.back())并踢出st
        st.pop_back();
        count++;

        //访问邻居
        for (int i = 0; i < 4; i++) {
            int nx = cur_pos.first + dr[i];
            int ny = cur_pos.second + dc[i];
            if (nx >= 0 && nx < n && ny >= 0 && ny <m
            && grid[nx][ny] == '#' && comp_id[nx][ny] == -1) { //挑选合法的点进入st
                comp_id[nx][ny] = cur_id;
                st.push_back({nx, ny});
            }
        }
    }
    return count;
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    while (t--) {
        cin >> n >> m;
        //initialize
        grid.resize(n);
        comp_id.assign(n, vector<int>(m, -1));
        comp_sz.clear();
        //build grid
        for (int i = 0; i < n; i++) cin >> grid[i];

        //统计comp_id,comp_sz
        int cur_id = 0;
        for (int x = 0; x < n; x++) {
            for (int y = 0; y < m; y++) {
                if (grid[x][y] == '#' && comp_id[x][y] == -1) {
                    int temp = dfs(x, y, cur_id);
                    cur_id++;
                    comp_sz.push_back(temp);
                }
            }
        }

        /* ===========
           模拟每行每列
           ===========*/
        int res = 0;

        //模拟行
        for (int x = 0; x < n; x++) {
            set<int> used_id;
            int sum = 0;
            for (int y = 0; y < m; y++) {
                if (grid[x][y] == '.') {
                    sum += 1;
                    //访问邻居
                    for (int i = 0; i < 4; i++) {
                        int nx = x + dr[i];
                        int ny = y + dc[i];
                        if (nx < 0 || nx >= n || ny < 0 || ny >= m || grid[nx][ny] == '.') continue;
                        if (!used_id.count(comp_id[nx][ny])) {
                            used_id.insert(comp_id[nx][ny]);
                            sum += comp_sz[comp_id[nx][ny]];
                        }
                    }
                } else if (grid[x][y] == '#') {
                    if (!used_id.count(comp_id[x][y])) {
                        used_id.insert(comp_id[x][y]);
                        sum += comp_sz[comp_id[x][y]];
                    }
                }
            }
            res = max(res, sum);
        }
        //模拟列
        for (int y = 0; y < m; y++) {
            set<int> used_id;
            int sum = 0;
            for (int x = 0; x < n; x++) {
                if (grid[x][y] == '.') {
                    sum += 1;
                    //访问邻居
                    for (int i = 0; i < 4; i++) {
                        int nx = x + dr[i];
                        int ny = y + dc[i];
                        if (nx < 0 || nx >= n || ny < 0 || ny >= m || grid[nx][ny] == '.') continue;
                        if (!used_id.count(comp_id[nx][ny])) {
                            used_id.insert(comp_id[nx][ny]);
                            sum += comp_sz[comp_id[nx][ny]];
                        }
                    }
                } else if (grid[x][y] == '#') {
                    if (!used_id.count(comp_id[x][y])) {
                        used_id.insert(comp_id[x][y]);
                        sum += comp_sz[comp_id[x][y]];
                    }
                }
            }
            res = max(res, sum);
        }

        cout << res << '\n';
    }
    return 0;
}
```

