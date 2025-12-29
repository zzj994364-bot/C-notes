https://codeforces.com/contest/540/problem/C

## **C. Ice Cave**

每次测试时间限制：2 秒

每次测试内存限制：256 兆字节

你正在玩一款电脑游戏。你的角色位于一个多层冰穴的某一层。为了继续前进，你需要下降到更低的一层，而唯一的办法就是从冰层中掉下去。

你所在的这一层冰穴是一个 $n$ 行 $m$ 列的矩形网格。每个单元格要么是**完整的冰（intact ice）**，要么是**破碎的冰（cracked ice）**。你可以从当前单元格移动到与其**相邻**的单元格（由于游戏引擎的某些限制，你不能原地跳跃，即不能从一个单元格跳回它自己）。

- 如果你移动到一个**破碎的冰**单元格，你的角色就会从那里掉下去。
- 如果你移动到一个**完整的冰**单元格，该单元格的冰就会变成**破碎的**。

我们将行从上到下编号为 $1$ 到 $n$，列从左到右编号为 $1$ 到 $m$。第 $r$ 行和第 $c$ 列交汇处的单元格记作 $(r, c)$。

你初始站在单元格 $(r_1, c_1)$，这个单元格是**破碎的**，因为你刚从更高的一层掉到这里。你需要从单元格 $(r_2, c_2)$ 掉下去，因为通往下一层的出口就在那里。你能做到吗？

### **输入 (Input)**

第一行包含两个整数 $n$ 和 $m$ ($1 \le n, m \le 500$) — 冰穴描述中的行数和列数。

接下来的 $n$ 行描述了冰穴的初始状态。每行包含 $m$ 个字符：

- `.` 表示完整的冰。
- `X` 表示破碎的冰。

下一行包含两个整数 $r_1$ 和 $c_1$ ($1 \le r_1 \le n, 1 \le c_1 \le m$) — 你的初始坐标。题目保证坐标 $(r_1, c_1)$ 对应的字符是 'X'，即起始单元格的冰最初就是破碎的。

最后一行包含两个整数 $r_2$ 和 $c_2$ ($1 \le r_2 \le n, 1 \le c_2 \le m$) — 你需要掉下去的目标单元格坐标。终点单元格可能与起点单元格重合。

### **输出 (Output)**

如果你能到达目的地并掉下去，打印 'YES'，否则打印 'NO'。

### **样例 (Example)**

**Input**

Plaintext

```
4 6
X...XX
...XX.
.X..X.
......
1 6
2 2
```

**Output**

Plaintext

```
YES
```

**Input**

Plaintext

```
5 4
.X..
...X
X.X.
....
.XX.
5 3
1 1
```

**Output**

Plaintext

```
NO
```

**Input**

Plaintext

```
4 7
..X.XX.
.XX..X.
X...X..
X......
2 2
1 6
```

**Output**

Plaintext

```
YES
```

### **提示 (Note)**

在第一个样例中，一种可能的路径是：

$(1, 6) \to (1, 5) \to (1, 4) \to (1, 3) \to (2, 3) \to (2, 2)$。

在第一次访问单元格 $(2, 2)$ 后，那里的冰会破碎；当你第二次踏入该单元格时，你的角色就会按计划掉下去。



































## 解题思路

**核心思想**

- 判断起点终点是否连通
  - 若连通，终点为**'.'**
    - 起点终点相邻 -> 终点需要包括起点以内的两个合法邻居
    - 起点终点不相邻 -> 终点需要两个合法邻居
  - 终点为**'X'**
    - 起点终点相同 -> 终点需要一个合法邻居
    - 其他 -> "YES"

**技巧**

- 剪枝：当已经找到连通路径后canreach = true; 立即停止所有搜索来优化性能

## 代码实现

```cpp
//n, m
//grid
//start, destination
#include <bits/stdc++.h>
using namespace std;

int n, m;
int r1, c1, r2, c2;


vector<string> grid; //0-based grid
vector<vector<bool>> visited;

int dr[4] = {1, -1, 0, 0};
int dc[4] = {0, 0, 1, -1};


bool canreach = false;
//dfs
void dfs(int x, int y) {
    visited[x][y] = true;
    if (x == r2 && y == c2) {
        canreach = true;
        return;
    }
    if (canreach) return; //剪枝
    //四向邻居
    for (int i = 0; i < 4; i++) {
        int nx = x + dr[i];
        int ny = y + dc[i];
        if (nx < 0 || nx >= n || ny < 0 || ny >= m) continue; //跳过溢出邻居
        
        //向'.'邻居 或 'X'目的地邻居递归
        
        if ((grid[nx][ny] == '.' && !visited[nx][ny]) || (nx == r2 && ny == c2)) dfs(nx, ny);
        
    }
}



//destination's neighbors
int neighbors(int x, int y) {
    int num = 0;
    for (int i = 0; i < 4; i++) {
        int nx = x + dr[i];
        int ny = y + dc[i];
        if (nx < 0 || nx >= n || ny < 0 || ny >= m) continue;

        if (grid[nx][ny] == '.' || visited[nx][ny]) num++; //visited[nx][ny]用来记录被访问的起点，用于起点终点相邻时
    }
    return num;
}


int main() {
    cin >> n >> m;
    grid.resize(n);
    visited.assign(505, vector<bool>(505, false));
    for (int i = 0; i < n; i++) {
        cin >> grid[i];
    }
    cin >> r1 >> c1 >> r2 >> c2;
    r1--;c1--;r2--;c2--;
    if (r1 == r2 && c1 == c2) {
        if (neighbors(r2, c2) >= 1) {
            cout << "YES" << endl;
            return 0;
        } else {
            cout << "NO" << endl;
            return 0;
        }
    }

    //if can reach
    dfs(r1, c1);
    if (!canreach) {
        cout << "NO" << endl;
    } else {
        if (grid[r2][c2] == 'X') {
            cout << "YES" << endl;
        } else {
            int neighborsnum = neighbors(r2, c2);
            if (neighborsnum >= 2) {
                cout << "YES" << endl;
            } else {
                cout << "NO" << endl;
            }
        }
    }
    return 0;
}
```

