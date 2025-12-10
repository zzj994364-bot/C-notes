# 前缀和 (Prefix Sum)

## 基础

前缀和（Prefix Sum）是一种通过预计算来快速求解**静态数组区间和**的技巧。对于一个给定的数组 `a`，其前缀和数组 `prefix` 的定义是 `prefix[i]` 存储了原数组从开头到第 `i-1` 项的总和。

核心思想是，一旦预计算出前缀和数组，任意区间 `[l, r]` 的和就可以通过两次数组访问和一个减法操作在 $O(1)$ 时间内得出，即 `sum(l, r) = prefix[r+1] - prefix[l]`。这个思想可以扩展到二维，用于快速计算子矩阵的和。

常见应用：

  * **一维/二维静态区间和查询**: 在数组或矩阵内容**不变**的情况下，快速响应大量的区间/子矩阵和查询。这是其最核心的应用。
  * **差分数组的逆运算**: 对一个差分数组求前缀和，可以得到其原始数组。
  * **结合哈希表**: 用于解决“和为k的子数组”等问题。通过遍历数组，将每个位置的前缀和存入哈希表，可以快速检查是否存在一个 `prefix[j]` 使得 `prefix[i] - prefix[j] = k`。

时间/空间复杂度：

  * **预计算（构建）**:
      * 一维: 时间 $O(N)$，空间 $O(N)$
      * 二维: 时间 $O(M \times N)$，空间 $O(M \times N)$
  * **查询**:
      * 一维/二维: 时间 $O(1)$

## 算法的流程

### 一维前缀和流程

1.  **构建**: 创建一个比原数组 `a`（长度为 N）大 1 的前缀和数组 `prefix`（长度为 N+1）。令 `prefix[0] = 0`。
2.  遍历原数组 `a`，从 `i = 0` 到 `N-1`，计算 `prefix[i] = prefix[i - 1] + a[i]`。
3.  **查询**: 计算原数组中 `[l, r]`（闭区间，0-indexed）的和时，结果为 `prefix[r+1] - prefix[l]`。

### 二维前缀和流程

1.  **构建**: 创建一个比原矩阵 `mat`（大小为 M x N）的行列都大 1 的前缀和矩阵 `prefix`（大小为 (M+1) x (N+1)）。
2.  遍历原矩阵 `mat` 的每个元素 `mat[i][j]`，使用递推公式计算前缀和：
    `prefix[i][j]=prefix[i−1][j]+prefix[i][j−1]−prefix[i−1][j−1]+arr[i][j]`。
3.  **查询**: **r: row行, c: column列**计算以 `(r1, c1)` 为左上角，`(r2, c2)` 为右下角的子矩阵（0-indexed）的和时，使用容斥原理计算结果：
4.  `result = prefixsum2D[r2][c2] - prefixsum2D[r1-1][c2] - prefixsum2D[r2][c1-1] + prefixsum2D[r1-1][c1-1]`。

## 算法模板

### 模板一：一维前缀和

https://usaco.guide/silver/prefix-sums?lang=cpp

使用类来封装预计算和查询逻辑，是一种清晰的实现方式。

```cpp
#include <iostream>
#include <vector>
using ll = long long;
using namespace std;

int main() {
    int n, q;
    cin >> n >> q;

    // 初始化 (n+1) 的前缀和数组，下标从 1 开始
    vector<ll> prefixsum(n + 1);

    // 构建一维前缀和
    for (int i = 1; i <= n; i++) {
        ll num;
        cin >> num;
        prefixsum[i] = prefixsum[i - 1] + num;
    }

    // 处理查询
    while (q--) {
        int l, r;
        cin >> l >> r;
        
        ll res = prefixsum[r] - prefixsum[l - 1];

        cout << res << '\n';
    }

    return 0;
}
```

### 模板二：二维前缀和

https://usaco.guide/silver/more-prefix-sums?lang=cpp

```cpp
#include <iostream>
#include <vector>
using ll = long long;
using namespace std;

int main() {
    int n, m, q;
    cin >> n >> m >> q;

    // 初始化 (n+1) × (m+1) 的前缀和数组，下标从 1 开始
    vector<vector<ll>> prefixsum2D(n + 1, vector<ll>(m + 1));

    // 构建二维前缀和
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            ll num;
            cin >> num;
            prefixsum2D[i][j] = num
                              + prefixsum2D[i][j - 1]
                              + prefixsum2D[i - 1][j]
                              - prefixsum2D[i - 1][j - 1];
        }
    }

    // 处理查询
    while (q--) {
        int r1, c1, r2, c2;
        cin >> r1 >> c1 >> r2 >> c2;
        
        ll res = prefixsum2D[r2][c2]
               - prefixsum2D[r1 - 1][c2]
               - prefixsum2D[r2][c1 - 1]
               + prefixsum2D[r1 - 1][c1 - 1];

        cout << res << '\n';
    }

    return 0;
}
```

### 二维数组旋转45度
参考题目：https://usaco.org/index.php?page=viewproblem2&cpid=416

二维数组 `n x n` 旋转45度（此时并不是一个二维数组），并用其他元素（如0）填充成一个大的二维数组 `(n + n - 1) x (n + n - 1)`。

![](C:\Users\24940\Desktop\C++笔记\Prefix sum前缀和\images\数组旋转45度.png)

```cpp
// 下标从 0 开始
// 原数组的元素(i, j)，在新数组中的位置位(i + j, n - i + j - 1)
int new_n = 2 * n - 1; // 行、列填充到 2 * n - 1 个元素
vector<vector<int>> vec(new_n, vector<int>(new_n));
for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
        arr_new[i + j][n - i + j - 1] = arr_origin[i][j];
    }
}
```
```cpp
// 下标从 1 开始
int new_n = 2 * n - 1; // 行、列填充到 2 * n - 1 个元素
vector<vector<int>> vec(new_n + 1, vector<int>(new_n + 1));
for (int i = 1; i <= n; i++) {
    for (int j = 1; j <= n; j++) {
        arr_new[i + j - 1][n - i + j] = arr_origin[i][j];
    }
}
```