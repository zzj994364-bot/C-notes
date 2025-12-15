# DFS 类型总结：void / int / bool

DFS 像一条会拐弯的河流，在图或树里一路流淌。
 不同返回类型，就像换了不同的“水杯”去接回流的结果，各有用途。

------

##  **void 型 DFS**

### 典型用途

用于只**遍历**、**标记**、**收集结果**，但不需要上层返回值。
 例如：

- 找连通分量
- 标记访问
- 构建序列（欧拉序、DFS order）
- 统计结果但写在外部数组中（subtree size 等）

### 模板

```
void dfs(int u) {
    vis[u] = true;
    for (int v : adj[u]) {
        if (!vis[v]) dfs(v);
    }
}
```

### 例题：求连通分量个数

```
int n;
vector<vector<int>> adj;
vector<bool> vis;

void dfs(int u) {
    vis[u] = true;
    for (int v : adj[u])
        if (!vis[v]) dfs(v);
}

int main() {
    cin >> n;
    adj.resize(n+1);
    vis.assign(n+1, false);

    int a, b, m; 
    cin >> m;
    while (m--) cin >> a >> b, adj[a].push_back(b), adj[b].push_back(a);

    int cnt = 0;
    for (int i = 1; i <= n; i++) {
        if (!vis[i]) {
            cnt++;
            dfs(i);
        }
    }
    cout << cnt;
}
```

------

##  **int 型 DFS**

### 典型用途

用于从子树返回**某个数值**，由父节点合并。
 例如：

- 子树大小
- 子树 dp
- 路径长度
- 子树和（sum）

### 模板

```
int dfs(int u, int parent) {
    int result = 1;          // 例如子树大小至少为1（自己）
    for (int v : adj[u]) {
        if (v == parent) continue;
        result += dfs(v, u);
    }
    return result;
}
```

### 例题：树中每个节点的子树大小

```
vector<vector<int>> adj;
vector<int> sub;

int dfs(int u, int p) {
    sub[u] = 1;
    for (int v : adj[u]) {
        if (v == p) continue;
        sub[u] += dfs(v, u);
    }
    return sub[u];
}

int main() {
    int n; cin >> n;
    adj.resize(n+1);
    sub.resize(n+1);

    for (int i = 2; i <= n; i++) {
        int boss; cin >> boss;
        adj[boss].push_back(i);
    }

    dfs(1, 0);

    for (int i = 1; i <= n; i++) cout << sub[i]-1 << " ";
}
```

------

##  **bool 型 DFS**

### 典型用途

适用于沿 DFS 搜索时需要**剪枝**或**找到立刻返回 true 的目标**。
 例如：

- 找是否存在一条路径
- DFS 搜索某个值
- 判断图是否有环
- 回溯问题（数独、迷宫、棋盘搜索）

### 模板

```
bool dfs(int u, int target) {
    if (u == target) return true;
    vis[u] = true;

    for (int v : adj[u]) {
        if (!vis[v] && dfs(v, target))
            return true;
    }
    return false;
}
```

### 例题：图中是否存在从 s 到 t 的路径

```
vector<vector<int>> adj;
vector<bool> vis;

bool dfs(int u, int t) {
    if (u == t) return true;
    vis[u] = true;

    for (int v : adj[u]) {
        if (!vis[v] && dfs(v, t))
            return true;
    }
    return false;
}

int main() {
    int n, m, s, t;
    cin >> n >> m >> s >> t;
    adj.resize(n+1);
    vis.assign(n+1, false);

    while (m--) {
        int a, b; cin >> a >> b;
        adj[a].push_back(b); adj[b].push_back(a);
    }

    cout << (dfs(s, t) ? "YES" : "NO");
}
```