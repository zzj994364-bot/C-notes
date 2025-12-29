https://codeforces.com/contest/1805/problem/D

## 宽宽的图

每次测试的时间限制：1秒

内存限制：256兆字节

给你一棵包含 $n$ 个节点的树（一个无环的连通图）。

考虑一个固定的整数 $k$。图 $G_k$ 是一个包含 $n$ 个节点的无向图，其中节点 $u$ 和 $v$ 之间存在一条边的充要条件是：在给定的树中，$u$ 和 $v$ 之间的距离**至少**为 $k$。

对于从 $1$ 到 $n$ 的每个 $k$，输出图 $G_k$ 中连通分量的数量。

**输入**

第一行包含整数 $n$ ($2 \le n \le 10^5$) —— 图中的节点数。

接下来的 $n-1$ 行，每行包含两个整数 $u$ 和 $v$ ($1 \le u, v \le n$)，表示树中节点 $u$ 和 $v$ 之间有一条边。保证这些边构成一棵有效的树。

**输出**

输出 $n$ 个整数：对于每个从 $1$ 到 $n$ 的 $k$，输出图 $G_k$ 中连通分量的数量。

**示例**

**输入**

```
6
1 2
1 3
2 4
2 5
3 6
```

**输出**

```
1 1 2 4 6 6 
```

**输入**

```
5
1 2
2 3
3 4
3 5
```

**输出**

```
1 1 3 5 5 
```

**注**

在第一个示例中：如果 $k=1$，图中每对节点之间都有边，因此它有一个连通分量。如果 $k=4$，图中只有边 $4 \leftrightarrow 6$ 和 $5 \leftrightarrow 6$，因此图有 $4$ 个连通分量。

在第二个示例中：当 $k=1$ 或 $k=2$ 时，图有一个连通分量。当 $k=3$ 时，图 $G_k$ 分为 $3$ 个连通分量：一个分量包含节点 $1, 4, 5$，另外两个分量各包含一个节点。当 $k=4$ 或 $k=5$ 时，每个节点都是一个独立的连通分量。





























## 解题思路

**目的**

- 在k动态变化先求Gk的连通块数量
- 构造要求符合dist[u, v] >= k

**关键观察**

- 从k = 1开始构造会形成极度稠密图
- **正难则反**，从 dist[u, v] < k 出发
  - 此时的图是**n个孤立点**，连通块数量为**n**
  - 找到**max_dist[u, v]**（即树的直径）并缩小k，可以建立一个n个孤立点**逐渐连通**的动态模型
  - 得出**关键参数：点到直径端点的最远距离**，即耐受极限，当max_dist < k，将是一个孤立点

## 代码实现

```cpp
//求diameter和L,R
//k从n递减
//k  >  diameter， capms = n
//k  <= diameter, 合并连通块
#include <bits/stdc++.h>

//1based宏定义
#define all1(a) a.begin() + 1, a.end()
#define rall1(a) a.rbegin(), a.end() - 1

using namespace std;

//adj
vector<vector<int>> adj;
int n;

/*
dfs 功能：记录树各点到u的距离,即整理distu
第一次找最远点A
第二次整理distA并找到最远点B
第三次整理distB
*/
void dfs(int u, int fa, int depth, vector<int>& dist) {
    dist[u] = depth;
    for (auto v : adj[u]) {
        if (v == fa) continue;
        dfs(v, u, depth + 1, dist);
    }
}


int main() {
    cin >> n;
    adj.resize(n + 1);
    for (int i = 0; i < n - 1; i++) {
        int a, b;
        cin >> a >> b;
        adj[a].push_back(b);
        adj[b].push_back(a);
    }
    
    vector<int> distA(n + 1);
    vector<int> distB(n + 1);
    //找A点
    dfs(1, 0, 0, distA);
    int A = max_element(all1(distA)) - distA.begin();
    //整理distA
    dfs(A, 0, 0, distA);
    //取distA最远点为B
    int B = max_element(all1(distA)) - distA.begin();
    //整理distB
    dfs(B, 0, 0, distB);

    /*
    整合所有点维持在连通块中的最远距离，即耐受极限max_dist
    随k递增，当max_dist < k, 该点被孤立， v_num++
    */
    for (int i = 1; i <= n; ++i) {
        distB[i] = max(distB[i], distA[i]);
    }
    sort(all1(distB));

    int v_num = 0;
    int index = 1;
    for (int k = 1; k <= n; k++) {
        while (index <= n && distB[index] < k) {
            v_num++;
            index++;
        }
        cout << min(n, v_num + 1) << ' ';
    }
    return 0;
}
```

