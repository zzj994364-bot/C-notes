## 二维 Vector 详解

### 基本概念

二维 vector 本质上是"vector 的 vector"，即 `vector<vector<T>>`。每一行是一个独立的 vector，可以有不同的长度。

### 创建与初始化

#### 1. 规则矩阵（所有行长度相同）
```cpp
// 方法一：构造函数（推荐）
int m = 3, n = 4;
vector<vector<int>> matrix(m, vector<int>(n, 0));
// 创建 3×4 矩阵，所有元素初始化为 0

// 方法二：初始化列表
vector<vector<int>> matrix = {
    {1, 2, 3, 4},
    {5, 6, 7, 8},
    {9, 10, 11, 12}
};

// 方法三：先创建空 vector，再逐行添加
vector<vector<int>> matrix;
for (int i = 0; i < m; i++) {
    matrix.push_back(vector<int>(n, 0));
}

// 方法四：resize 方式
vector<vector<int>> matrix;
matrix.resize(m);
for (int i = 0; i < m; i++) {
    matrix[i].resize(n, 0);
}

// 方法五：预分配空间（性能优化）
vector<vector<int>> matrix;
matrix.reserve(m);
for (int i = 0; i < m; i++) {
    matrix.emplace_back(n, 0);  // 直接在末尾构造
}
```

#### 2. 不规则矩阵（锯齿数组）
```cpp
// 方法一：初始化列表
vector<vector<int>> jagged = {
    {1, 2, 3},
    {4, 5},
    {6, 7, 8, 9},
    {10}
};

// 方法二：动态创建
vector<vector<int>> jagged;
jagged.push_back({1, 2, 3});
jagged.push_back({4, 5});
jagged.push_back({6, 7, 8, 9});

// 方法三：根据条件创建不同长度的行
vector<vector<int>> triangle(n);
for (int i = 0; i < n; i++) {
    triangle[i].resize(i + 1, 0);  // 第 i 行有 i+1 个元素
}
// triangle = {
//     {0},
//     {0, 0},
//     {0, 0, 0},
//     ...
// }
```

#### 3. 特殊初始化
```cpp
// 对角矩阵
vector<vector<int>> identity(n, vector<int>(n, 0));
for (int i = 0; i < n; i++) {
    identity[i][i] = 1;
}

// 上三角矩阵
vector<vector<int>> upperTri(n);
for (int i = 0; i < n; i++) {
    upperTri[i].resize(n - i);  // 第 i 行有 n-i 个元素
}

// 帕斯卡三角形
vector<vector<int>> pascal(n);
for (int i = 0; i < n; i++) {
    pascal[i].resize(i + 1);
    pascal[i][0] = pascal[i][i] = 1;
    for (int j = 1; j < i; j++) {
        pascal[i][j] = pascal[i-1][j-1] + pascal[i-1][j];
    }
}
```

### 访问元素

```cpp
vector<vector<int>> matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

// 1. 下标访问（不检查越界）
int val = matrix[1][2];  // 6

// 2. at() 访问（检查越界）
try {
    int val = matrix.at(1).at(2);  // 6
    int bad = matrix.at(10).at(10);  // 抛出 out_of_range
} catch (const out_of_range& e) {
    cout << "越界：" << e.what() << endl;
}

// 3. 获取行和列数
int rows = matrix.size();        // 3
int cols = matrix[0].size();     // 3（假设非空且规则）

// 4. 检查是否为空
if (!matrix.empty() && !matrix[0].empty()) {
    // 矩阵非空
}

// 5. 不规则矩阵访问
vector<vector<int>> jagged = {{1,2,3}, {4,5}, {6}};
for (int i = 0; i < jagged.size(); i++) {
    for (int j = 0; j < jagged[i].size(); j++) {  // 每行长度可能不同
        cout << jagged[i][j] << " ";
    }
    cout << endl;
}
```

### 遍历方法

```cpp
vector<vector<int>> matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

// 1. 传统双层循环
for (int i = 0; i < matrix.size(); i++) {
    for (int j = 0; j < matrix[i].size(); j++) {
        cout << matrix[i][j] << " ";
    }
    cout << endl;
}

// 2. 范围 for 循环（推荐）
for (const auto& row : matrix) {
    for (const auto& elem : row) {
        cout << elem << " ";
    }
    cout << endl;
}

// 3. 使用迭代器
for (auto it = matrix.begin(); it != matrix.end(); ++it) {
    for (auto jt = it->begin(); jt != it->end(); ++jt) {
        cout << *jt << " ";
    }
    cout << endl;
}

// 4. 修改元素（需要引用）
for (auto& row : matrix) {
    for (auto& elem : row) {
        elem *= 2;
    }
}

// 5. 带索引的范围 for（C++20 或自定义）
for (int i = 0; i < matrix.size(); i++) {
    for (int j = 0; j < matrix[i].size(); j++) {
        cout << "matrix[" << i << "][" << j << "] = " << matrix[i][j] << endl;
    }
}
```

### 常用操作

#### 1. 添加行/列
```cpp
vector<vector<int>> matrix = {
    {1, 2, 3},
    {4, 5, 6}
};

// 添加新行
matrix.push_back({7, 8, 9});

// 在指定位置插入行
matrix.insert(matrix.begin() + 1, {10, 11, 12});

// 为每行添加新列
for (auto& row : matrix) {
    row.push_back(0);  // 在每行末尾添加元素
}

// 为每行在开头插入列
for (auto& row : matrix) {
    row.insert(row.begin(), 0);
}
```

#### 2. 删除行/列
```cpp
vector<vector<int>> matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

// 删除最后一行
matrix.pop_back();

// 删除指定行
matrix.erase(matrix.begin() + 1);  // 删除第 2 行

// 删除指定范围的行
matrix.erase(matrix.begin() + 1, matrix.begin() + 3);

// 删除每行的最后一列
for (auto& row : matrix) {
    if (!row.empty()) {
        row.pop_back();
    }
}

// 删除每行的指定列
int colToDelete = 1;
for (auto& row : matrix) {
    if (colToDelete < row.size()) {
        row.erase(row.begin() + colToDelete);
    }
}

// 清空整个矩阵
matrix.clear();
```

#### 3. 调整大小
```cpp
vector<vector<int>> matrix;

// 调整为 m×n，新元素填充 0
int m = 4, n = 5;
matrix.resize(m);
for (auto& row : matrix) {
    row.resize(n, 0);
}

// 扩大矩阵
matrix.resize(m + 2);  // 增加 2 行
for (auto& row : matrix) {
    row.resize(n + 3, 0);  // 每行增加 3 列
}

// 缩小矩阵
matrix.resize(m - 1);  // 删除最后 1 行
for (auto& row : matrix) {
    row.resize(n - 2);  // 每行删除最后 2 列
}
```

### 矩阵算法

#### 1. 转置
```cpp
vector<vector<int>> transpose(const vector<vector<int>>& matrix) {
    if (matrix.empty()) return {};
    
    int m = matrix.size();
    int n = matrix[0].size();
    vector<vector<int>> result(n, vector<int>(m));
    
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            result[j][i] = matrix[i][j];
        }
    }
    return result;
}

// 方阵原地转置
void transposeInPlace(vector<vector<int>>& matrix) {
    int n = matrix.size();
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            swap(matrix[i][j], matrix[j][i]);
        }
    }
}
```

#### 2. 旋转
```cpp
// 顺时针旋转 90 度
void rotate90(vector<vector<int>>& matrix) {
    int n = matrix.size();
    
    // 先转置
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            swap(matrix[i][j], matrix[j][i]);
        }
    }
    
    // 再水平翻转
    for (auto& row : matrix) {
        reverse(row.begin(), row.end());
    }
}

// 逆时针旋转 90 度
void rotate90CCW(vector<vector<int>>& matrix) {
    int n = matrix.size();
    
    // 先转置
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            swap(matrix[i][j], matrix[j][i]);
        }
    }
    
    // 再垂直翻转
    reverse(matrix.begin(), matrix.end());
}
```

#### 3. 螺旋遍历
```cpp
vector<int> spiralOrder(const vector<vector<int>>& matrix) {
    if (matrix.empty()) return {};
    
    vector<int> result;
    int m = matrix.size(), n = matrix[0].size();
    int top = 0, bottom = m - 1, left = 0, right = n - 1;
    
    while (top <= bottom && left <= right) {
        // 向右
        for (int j = left; j <= right; j++) {
            result.push_back(matrix[top][j]);
        }
        top++;
        
        // 向下
        for (int i = top; i <= bottom; i++) {
            result.push_back(matrix[i][right]);
        }
        right--;
        
        // 向左
        if (top <= bottom) {
            for (int j = right; j >= left; j--) {
                result.push_back(matrix[bottom][j]);
            }
            bottom--;
        }
        
        // 向上
        if (left <= right) {
            for (int i = bottom; i >= top; i--) {
                result.push_back(matrix[i][left]);
            }
            left++;
        }
    }
    
    return result;
}
```

#### 4. 搜索
```cpp
// 二维搜索（无序矩阵）
bool search(const vector<vector<int>>& matrix, int target) {
    for (const auto& row : matrix) {
        for (int elem : row) {
            if (elem == target) return true;
        }
    }
    return false;
}

// 搜索排序矩阵（每行每列递增）
bool searchMatrix(const vector<vector<int>>& matrix, int target) {
    if (matrix.empty() || matrix[0].empty()) return false;
    
    int m = matrix.size(), n = matrix[0].size();
    int i = 0, j = n - 1;  // 从右上角开始
    
    while (i < m && j >= 0) {
        if (matrix[i][j] == target) {
            return true;
        } else if (matrix[i][j] > target) {
            j--;  // 向左移动
        } else {
            i++;  // 向下移动
        }
    }
    return false;
}
```

#### 5. 填充
```cpp
// 全部填充为指定值
void fillMatrix(vector<vector<int>>& matrix, int val) {
    for (auto& row : matrix) {
        fill(row.begin(), row.end(), val);
    }
}

// 按模式填充
void fillPattern(vector<vector<int>>& matrix) {
    int count = 1;
    for (auto& row : matrix) {
        for (auto& elem : row) {
            elem = count++;
        }
    }
}

// 对角线填充
void fillDiagonal(vector<vector<int>>& matrix, int val) {
    int n = min(matrix.size(), matrix[0].size());
    for (int i = 0; i < n; i++) {
        matrix[i][i] = val;
    }
}
```

### 性能考虑

#### 1. 内存布局
```cpp
// 二维 vector 不保证内存连续！
vector<vector<int>> v2d;  // 每行是独立的 vector，可能分散在内存中

// 如果需要内存连续，使用一维 vector 模拟：
int m = 3, n = 4;
vector<int> matrix(m * n);

// 访问 (i, j)：
int val = matrix[i * n + j];

// 或使用辅助函数：
auto at = [&](int i, int j) -> int& {
    return matrix[i * n + j];
};
at(1, 2) = 10;
```

#### 2. 预分配空间
```cpp
// 差：频繁扩容
vector<vector<int>> matrix;
for (int i = 0; i < m; i++) {
    vector<int> row;
    for (int j = 0; j < n; j++) {
        row.push_back(j);  // 可能多次扩容
    }
    matrix.push_back(row);  // 可能多次扩容
}

// 好：预分配
vector<vector<int>> matrix;
matrix.reserve(m);
for (int i = 0; i < m; i++) {
    vector<int> row;
    row.reserve(n);
    for (int j = 0; j < n; j++) {
        row.push_back(j);
    }
    matrix.push_back(std::move(row));
}

// 更好：直接构造
vector<vector<int>> matrix(m, vector<int>(n));
for (int i = 0; i < m; i++) {
    for (int j = 0; j < n; j++) {
        matrix[i][j] = j;
    }
}
```

#### 3. 拷贝与移动
```cpp
// 深拷贝（昂贵）
vector<vector<int>> m1(100, vector<int>(100));
vector<vector<int>> m2 = m1;  // 拷贝所有 10000 个元素

// 移动（高效）
vector<vector<int>> m3 = std::move(m1);  // m1 变空，m3 获得数据

// 引用传递（避免拷贝）
void process(const vector<vector<int>>& matrix) {
    // 只读访问，无拷贝
}
```

### 常见应用场景

#### 1. 动态规划
```cpp
// 最长公共子序列
int longestCommonSubsequence(string s1, string s2) {
    int m = s1.size(), n = s2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
    
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (s1[i-1] == s2[j-1]) {
                dp[i][j] = dp[i-1][j-1] + 1;
            } else {
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
            }
        }
    }
    return dp[m][n];
}
```

#### 2. 图的邻接矩阵
```cpp
// 无向图
int n = 5;  // 5 个节点
vector<vector<int>> graph(n, vector<int>(n, 0));

// 添加边
graph[0][1] = graph[1][0] = 1;  // 节点 0 和 1 之间有边
graph[1][2] = graph[2][1] = 1;

// 带权重的图
vector<vector<int>> weightedGraph(n, vector<int>(n, INT_MAX));
for (int i = 0; i < n; i++) {
    weightedGraph[i][i] = 0;  // 自己到自己距离为 0
}
weightedGraph[0][1] = weightedGraph[1][0] = 5;  // 边权重为 5
```

#### 3. 图像处理
```cpp
// 表示灰度图像
vector<vector<int>> image(height, vector<int>(width));

// 读取像素
int pixel = image[y][x];

// 模糊处理（3×3 平均）
vector<vector<int>> blur(const vector<vector<int>>& img) {
    int h = img.size(), w = img[0].size();
    vector<vector<int>> result(h, vector<int>(w));
    
    for (int i = 1; i < h - 1; i++) {
        for (int j = 1; j < w - 1; j++) {
            int sum = 0;
            for (int di = -1; di <= 1; di++) {
                for (int dj = -1; dj <= 1; dj++) {
                    sum += img[i + di][j + dj];
                }
            }
            result[i][j] = sum / 9;
        }
    }
    return result;
}
```

#### 4. 游戏地图
```cpp
// 游戏地图（0=空地，1=墙，2=玩家）
vector<vector<int>> map = {
    {1, 1, 1, 1, 1},
    {1, 0, 0, 0, 1},
    {1, 0, 2, 0, 1},
    {1, 0, 0, 0, 1},
    {1, 1, 1, 1, 1}
};

// BFS 寻路
vector<vector<int>> directions = {{-1,0}, {1,0}, {0,-1}, {0,1}};
```

### 常见陷阱

#### 1. 未初始化列
```cpp
// 错误：只创建了行，没有创建列
vector<vector<int>> matrix(m);
matrix[0][0] = 1;  // 越界！列不存在

// 正确：
vector<vector<int>> matrix(m, vector<int>(n, 0));
```

#### 2. 不规则矩阵的行长度
```cpp
vector<vector<int>> jagged = {{1,2}, {3,4,5}, {6}};

// 错误：假设所有行长度相同
int cols = jagged[0].size();
for (int i = 0; i < jagged.size(); i++) {
    for (int j = 0; j < cols; j++) {  // 第 3 行只有 1 个元素！
        cout << jagged[i][j];
    }
}

// 正确：使用每行的实际长度
for (int i = 0; i < jagged.size(); i++) {
    for (int j = 0; j < jagged[i].size(); j++) {
        cout << jagged[i][j];
    }
}
```

#### 3. 引用失效
```cpp
vector<vector<int>> matrix = {{1, 2}, {3, 4}};
auto& row = matrix[0];  // 引用第一行

matrix.push_back({5, 6});  // 可能导致 matrix 扩容
// row 可能失效！

// 安全做法：重新获取引用或使用索引
```

#### 4. 深拷贝 vs 浅拷贝
```cpp
vector<vector<int>> m1 = {{1, 2}, {3, 4}};

// 这是深拷贝，不是引用
vector<vector<int>> m2 = m1;
m2[0][0] = 100;
cout << m1[0][0];  // 输出 1，不是 100

// 如果需要引用：
vector<vector<int>>& m3 = m1;
m3[0][0] = 100;
cout << m1[0][0];  // 输出 100
```