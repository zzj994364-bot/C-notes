https://codeforces.com/contest/24/problem/A

## 环形公路（Ring road)

**时间限制：** 2 秒
 **内存限制：** 256 MB

为了提高行车安全并减少交通拥堵，如今世界各地都在推行单向交通。Berland 政府也决定跟上这一趋势。

最初，Berland 的 **n 个城市** 由 **n 条双向道路** 连接成一个环，也就是说：

- 每个城市都恰好与另外两个城市直接相连；
- 从任意一个城市都可以到达其他所有城市。

后来，Berland 政府将这 **n 条道路全部改成了单向道路**。但很快发现，这样一来，从某些城市已经无法到达另外一些城市了。

现在，对于每一条道路，都已知：

- 该道路目前的行驶方向；
- 若要把道路的方向反转（改为相反方向），所需要的花费。

问题是：**政府最少需要花多少钱，才能通过反转部分道路的方向，使得从任意一个城市都可以到达其他所有城市？**

------

### 输入

第一行包含一个整数 **n**（3 ≤ n ≤ 100）——城市的数量（也是道路的数量）。

接下来 **n 行**，每行描述一条道路，包含三个整数 **aᵢ, bᵢ, cᵢ**（1 ≤ aᵢ, bᵢ ≤ n，aᵢ ≠ bᵢ，1 ≤ cᵢ ≤ 100），表示：

- 这条道路目前是 **从城市 aᵢ 指向城市 bᵢ**；
- 如果要将这条道路的方向反转，所需的花费为 **cᵢ**。

------

### 输出

输出一个整数 —— 政府为了保证 **任意城市之间都可互相到达**，所需要支付的 **最小总花费**。

------

### 示例

**输入**

```
3
1 3 1
1 2 1
3 2 1
```

**输出**

```
1
```

**输入**

```
3
1 3 1
1 2 5
3 2 1
```

**输出**

```
2
```

**输入**

```
6
1 5 4
5 3 8
2 4 15
1 6 16
2 3 23
4 6 42
```

**输出**

```
39
```

**输入**

```
4
1 2 9
2 3 8
3 4 7
4 1 5
```

**输出**

```
0
```



















## 解题思路

构造一个 **环** 的图

- 按边DFS

- ```cpp
  vector<directed_edge>> adj; //用有向边构造邻接表
  ```

分为顺和逆两个方向行驶，取cost最小的值

## 代码实现

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct directed_edge{
    int to;
    int cost;
};

//directed edge adj
vector<vector<directed_edge>> adj;
long long ans;

//from ：上一个节点
//to ： 当前节点
//e.to ： 下一个节点
void dfs(int to, int from, int cost) {
    if (to == 1) { //当下一个节点是初始的“1”时，完成闭环，结算cost
        ans = cost;
        return;
    }
    for (auto e : adj[to]) {
        if (e.to != from) { //构造的是“环”，方向只有两个，因此只要“上一个节点”不是“下一个节点”就能确保传递下去
            dfs(e.to, to, cost + e.cost);
        }
    }
}


int main() {
    int n;
    cin >> n;
    
    //initialize adj and visited
    adj.resize(n + 1);
    for (int i = 1; i <= n; i++) {
        int a, b, c;
        cin >> a >> b >> c;
        adj[a].push_back({b, 0});
        adj[b].push_back({a, c});
    }

    dfs(adj[1][0].to, 1, adj[1][0].cost); //正向
    long long ans1 = ans;
    dfs(adj[1][1].to, 1, adj[1][1].cost); //反向
    long long ans2 = ans;

    cout << min(ans1, ans2) << endl;

    return 0;

}
```

本题**没有用到visited**
