https://cses.fi/problemset/task/1674

## **Subordinates（下属数量）**

给定一家公司的组织结构，你的任务是计算每个员工有多少下属。

------

### **输入格式**

第一行给一个整数 `n`：员工数量。
 员工编号为 `1, 2, ..., n`，其中 **1 号员工是总负责人（总监）**。

接下来有 `n - 1` 个整数：
 第 `i` 个数表示 **员工 i+1 的直属上司**。

也就是说：

- 输入的第一个数字对应员工 2 的老板
- 第二个数字对应员工 3 的老板
- ...
- 最后一个数字对应员工 n 的老板

------

### **输出格式**

输出 `n` 个整数。
 第 `i` 个整数表示 **员工 i 的所有下属数量（不只是直属，而是整棵树下面所有层级）**。

------

### **约束条件**

- `1 ≤ n ≤ 2 * 10^5`
- 输入形成一棵树（因为所有人都汇报给 1）

------

### **样例**

#### 输入

```
5
1 1 2 3
```

含义：

- 员工 2 的老板是 1
- 员工 3 的老板是 1
- 员工 4 的老板是 2
- 员工 5 的老板是 3

结构是：

```
        1
      /   \
     2     3
    /       \
   4         5
```

#### 输出

```
4 1 1 0 0
```

解释：

- 1 有 4 个下属（2,3,4,5）
- 2 有 1 个下属（4）
- 3 有 1 个下属（5）
- 4 有 0
- 5 有 0//

























## 解题思路

int类型DFS

本质是统计子树和



## 代码实现

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<vector<int>> graph;
vector<bool> visited;
vector<int> all_subordinates;

//直接统计all_subordinates
int dfs(int u) {
    int total = 0;
    visited[u] = true;

    //遍历所有邻接点
    for (auto v : graph[u]) {
        //确保访问子节点
        if (!visited[v]) {
            int subtree_size = dfs(v);
            total += subtree_size;
        }
    }

    all_subordinates[u] = total;
    return total + 1;
}


int main() {
    int n;
    cin >> n;

    graph.resize(n + 1);
    visited.resize(n + 1, false);
    all_subordinates.resize(n + 1);

    //adjacency list
    for (int a = 2; a <= n; a++) {
        int b;
        cin >> b;
        graph[a].push_back(b);
        graph[b].push_back(a);
    }

    dfs(1);

    for (int i = 1; i <= n; i++) {
        cout << all_subordinates[i] << ' ';
    }


    return 0;
}
```



