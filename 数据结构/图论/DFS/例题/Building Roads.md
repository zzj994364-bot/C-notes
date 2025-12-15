https://cses.fi/problemset/task/1666/

## **Building Roads**

在 Byteland 有 n 个城市，城市之间有 m 条道路。
 目标是建造一些新的道路，使得任意两个城市之间都能互相到达。
 你的任务是计算需要新建的道路的最少数量，并给出应当新建哪些道路。

### **输入**

第一行包含两个整数 n 和 m：城市数量和道路数量。城市编号为 1 到 n。
 接下来有 m 行，每行包含两个整数 a 和 b，表示城市 a 与城市 b 之间有一条道路。

保证每条道路连接两个不同的城市，且任意一对城市之间至多只有一条道路。

### **输出**

首先输出一个整数 k：所需新建的道路数量。
 接着输出 k 行，每行描述一条要新建的道路。
 可以输出任意一组合法的答案。

### **约束**

- 1 ≤ n ≤ 100000
- 1 ≤ m ≤ 200000
- 1 ≤ a, b ≤ n

### **样例**

输入：

```
4 2
1 2
3 4
```

输出：

```
1
2 3
```























## 解题思路

void类型的**DFS**  /  **Adjacency list**

#### 核心思想 

1. **遍历所有节点**：从1到n依次检查每个节点 
1. **发现新连通分量**：遇到未访问的节点，说明发现了新的连通分量 
1. **DFS标记整个分量**：从该节点开始DFS，将整个连通分量的所有节点标记为已访问 
1. **连接所有分量**：用k-1条边将k个连通分量连成一个整体



## 代码实现

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<vector<int>> graph;
vector<bool> visited;

//dfs
void dfs(int u) {
    visited[u] = true;

    for (auto v : graph[u]) {
        if (!visited[v]) {
            dfs(v);
        }
    }
}

int main() {
    int n, m;
    cin >> n >> m;

    graph.resize(n + 1);
    visited.resize(n + 1, false);

    //做adjacency list
    for (int i = 0; i < m; i++) {
        int a, b;
        cin >> a >> b;
        graph[a].push_back(b);
        graph[b].push_back(a);
    }

    //创建连通分量并以第一个出现的vertex作为这个连通分量的代表
    vector<int> representives;
    for (int i = 1; i <=n; i++) {
        if (!visited[i]) {
            dfs(i);
            representives.push_back(i);
        }
    }

    //1个连通分量
    if (representives.size() == 1) {
        cout << 0 << endl;
        return 0;
    }
    // > 1个连通分量
    if (representives.size() > 1) {
        //输出num_of_edges
        cout << representives.size() - 1 << '\n';
        //输出每条edges的vertices
        for (int i = 0; i < representives.size() - 1; i++) {
            cout << representives[i] << ' ' << representives[i + 1] << '\n';
        }
    }

    return 0;
}
```

