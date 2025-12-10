# C++ Vector 完整学习笔记

## 目录
1. [基本概念](#基本概念)
2. [内部实现原理](#内部实现原理)
3. [构造与初始化](#构造与初始化)
4. [容量管理](#容量管理)
5. [元素访问](#元素访问)
6. [修改操作](#修改操作)
7. [迭代器](#迭代器)
8. [算法与操作](#算法与操作)
9. [性能分析](#性能分析)
10. [最佳实践](#最佳实践)
11. [常见陷阱](#常见陷阱)

---

## 基本概念

### 什么是 Vector?
`vector` 是 C++ 标准模板库（STL）中的序列容器，表示可以动态改变大小的数组。

**核心特性：**
- 动态数组，自动管理内存
- 元素在内存中连续存储
- 支持随机访问（O(1) 时间复杂度）
- 尾部插入/删除效率高（均摊 O(1)）
- 中间插入/删除效率较低（O(n)）

**头文件：**

```cpp
#include <vector>
using namespace std;
```

---

## 内部实现原理

### 内存布局
```
vector 内部维护三个指针：
┌─────────────────────────────────┐
│ start    finish   end_of_storage│
│   ↓        ↓            ↓       │
│ [元素1][元素2][元素3][空闲空间]  │
└─────────────────────────────────┘
```

- **start**: 指向第一个元素
- **finish**: 指向最后一个元素的下一个位置
- **end_of_storage**: 指向分配内存的末尾

### 动态扩容机制

**扩容策略：**
1. 当 `size() == capacity()` 时触发扩容
2. 通常按 1.5 倍或 2 倍增长（实现依赖）
3. 分配新内存 → 拷贝/移动元素 → 释放旧内存

**扩容示例：**
```cpp
vector<int> v;
// capacity: 0, size: 0

v.push_back(1);
// capacity: 1, size: 1 (首次分配)

v.push_back(2);
// capacity: 2, size: 2 (扩容)

v.push_back(3);
// capacity: 4, size: 3 (扩容)

v.push_back(4);
// capacity: 4, size: 4 (无需扩容)

v.push_back(5);
// capacity: 8, size: 5 (扩容)
```

---

## 构造与初始化

### 1. 默认构造
```cpp
vector<int> v1;                    // 空 vector
```

### 2. 指定大小
```cpp
vector<int> v2(10);                // 10 个元素，默认初始化为 0
vector<int> v3(10, 5);             // 10 个元素，每个初始化为 5
```

### 3. 拷贝构造
```cpp
vector<int> v4(v3);                // 从 v3 拷贝
vector<int> v5 = v3;               // 同上
```

### 4. 移动构造（C++11）
```cpp
vector<int> v6(std::move(v3));     // 移动 v3 的资源到 v6
```

### 5. 初始化列表（C++11）
```cpp
vector<int> v7 = {1, 2, 3, 4, 5};
vector<int> v8{1, 2, 3, 4, 5};
```

### 6. 范围构造
```cpp
int arr[] = {1, 2, 3, 4, 5};
vector<int> v9(arr, arr + 5);      // 从数组构造

vector<int> v10(v9.begin(), v9.begin() + 3);  // 从迭代器范围构造
```

### 7. 二维 Vector
```cpp
// 创建 m×n 的二维 vector
vector<vector<int>> matrix(m, vector<int>(n, 0));

// 不规则二维数组
vector<vector<int>> jagged = {
    {1, 2, 3},
    {4, 5},
    {6, 7, 8, 9}
};
```

---

## 容量管理

### 容量相关函数

| 函数 | 说明 | 时间复杂度 |
|------|------|-----------|
| `size()` | 返回元素个数 | O(1) |
| `capacity()` | 返回当前容量 | O(1) |
| `empty()` | 判断是否为空 | O(1) |
| `max_size()` | 返回理论最大容量 | O(1) |
| `reserve(n)` | 预留至少 n 个元素的空间 | O(n) |
| `shrink_to_fit()` | 释放多余容量（C++11） | O(n) |
| `resize(n)` | 改变 size 到 n | O(n) |
| `resize(n, val)` | 改变 size 到 n，新元素初始化为 val | O(n) |

### 容量管理示例
```cpp
vector<int> v;

// 预分配空间，避免多次扩容
v.reserve(1000);
cout << "capacity: " << v.capacity() << endl;  // >= 1000
cout << "size: " << v.size() << endl;          // 0

// 改变大小
v.resize(10);        // size = 10, 元素初始化为 0
v.resize(15, 5);     // size = 15, 新增元素初始化为 5
v.resize(5);         // size = 5, 删除后面的元素

// 释放多余空间
v.shrink_to_fit();   // capacity 接近 size
```

### reserve vs resize
```cpp
vector<int> v1, v2;

v1.reserve(100);     // capacity = 100, size = 0
// v1[0] = 1;        // 错误！元素不存在

v2.resize(100);      // capacity >= 100, size = 100
v2[0] = 1;           // 正确
```

---

## 元素访问

### 访问方法对比

| 方法 | 越界检查 | 性能 | 使用场景 |
|------|---------|------|---------|
| `v[i]` | 无 | 最快 | 确保不越界时 |
| `v.at(i)` | 有，抛异常 | 稍慢 | 需要安全检查时 |
| `v.front()` | 无 | 快 | 访问第一个元素 |
| `v.back()` | 无 | 快 | 访问最后一个元素 |
| `v.data()` | - | 快 | 获取底层数组指针 |

### 访问示例
```cpp
vector<int> v = {10, 20, 30, 40, 50};

// 1. 下标访问（不检查越界）
cout << v[0] << endl;        // 10
cout << v[4] << endl;        // 50
// cout << v[10] << endl;    // 未定义行为！

// 2. at() 访问（检查越界）
cout << v.at(0) << endl;     // 10
try {
    cout << v.at(10) << endl;
} catch (const out_of_range& e) {
    cout << "越界：" << e.what() << endl;
}

// 3. front() 和 back()
cout << v.front() << endl;   // 10
cout << v.back() << endl;    // 50

// 4. data() 获取底层指针
int* ptr = v.data();
cout << ptr[0] << endl;      // 10

// 5. 通过迭代器访问
cout << *v.begin() << endl;  // 10
cout << *(v.end() - 1) << endl;  // 50
```

---

## 修改操作

### 插入操作

```cpp
vector<int> v = {1, 2, 3};

// 1. push_back - 尾部插入 O(1) 均摊
v.push_back(4);              // {1, 2, 3, 4}

// 2. emplace_back - 原地构造 O(1) 均摊（C++11）
v.emplace_back(5);           // {1, 2, 3, 4, 5}

// 3. insert - 指定位置插入前一位 O(n)
v.insert(v.begin(), 0);      // {0, 1, 2, 3, 4, 5}
v.insert(v.begin() + 2, 99); // {0, 1, 99, 2, 3, 4, 5}

// 插入多个元素
v.insert(v.end(), 3, 100);   // 插入 3 个 100
v.insert(v.end(), {7, 8, 9}); // 插入初始化列表

// 4. emplace - 原地构造插入 O(n)（C++11）
v.emplace(v.begin() + 3, 88);
```

### 删除操作

```cpp
vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8, 9};

// 1. pop_back - 删除尾部元素 O(1)
v.pop_back();                // {1, 2, 3, 4, 5, 6, 7, 8}

// 2. erase - 删除指定位置 O(n)
v.erase(v.begin());          // {2, 3, 4, 5, 6, 7, 8}
v.erase(v.begin() + 2);      // {2, 3, 5, 6, 7, 8}

// 删除范围 [first, last)
v.erase(v.begin() + 1, v.begin() + 3);  // {2, 6, 7, 8}

// 3. clear - 清空所有元素 O(n)
v.clear();                   // {}
// 注意：clear() 不改变 capacity

// 4. 删除特定值
vector<int> v2 = {1, 2, 3, 2, 4, 2, 5};
v2.erase(remove(v2.begin(), v2.end(), 2), v2.end());
// {1, 3, 4, 5} - erase-remove idiom
```

### 其他修改操作

```cpp
vector<int> v1 = {1, 2, 3};
vector<int> v2 = {4, 5, 6};

// 1. swap - 交换两个 vector O(1)
v1.swap(v2);                 // v1={4,5,6}, v2={1,2,3}

// 2. assign - 重新赋值
v1.assign(5, 10);            // {10, 10, 10, 10, 10}
v1.assign({1, 2, 3, 4});     // {1, 2, 3, 4}
v1.assign(v2.begin(), v2.end());  // 从迭代器范围赋值

// 3. 修改元素
v1[0] = 100;
v1.at(1) = 200;
```

---

## 迭代器

### 迭代器类型
```cpp
vector<int> v = {1, 2, 3, 4, 5};

// 1. 正向迭代器
vector<int>::iterator it;
for (it = v.begin(); it != v.end(); ++it) {
    cout << *it << " ";
}

// 2. 常量迭代器
vector<int>::const_iterator cit;
for (cit = v.cbegin(); cit != v.cend(); ++cit) {
    cout << *cit << " ";
    // *cit = 10;  // 错误！不能修改
}

// 3. 反向迭代器
vector<int>::reverse_iterator rit;
for (rit = v.rbegin(); rit != v.rend(); ++rit) {
    cout << *it << " ";  // 5 4 3 2 1
}

// 4. 常量反向迭代器
vector<int>::const_reverse_iterator crit = v.crbegin();
```

### C++11 范围 for 循环
```cpp
vector<int> v = {1, 2, 3, 4, 5};

// 只读
for (const auto& elem : v) {
    cout << elem << " ";
}

// 可修改
for (auto& elem : v) {
    elem *= 2;
}

// 拷贝（效率低，不推荐）
for (auto elem : v) {
    cout << elem << " ";
}
```

### 迭代器失效问题

**重要：**某些操作会使迭代器失效！

```cpp
vector<int> v = {1, 2, 3, 4, 5};
auto it = v.begin();

// 1. 插入导致扩容 - 所有迭代器失效
v.push_back(6);
// *it;  // 未定义行为！

// 2. erase - 被删除元素及之后的迭代器失效
v = {1, 2, 3, 4, 5};
it = v.begin() + 2;  // 指向 3
v.erase(it);         // it 失效
// 正确做法：
it = v.erase(it);    // erase 返回下一个有效迭代器

// 3. 删除所有偶数（错误示例）
for (auto it = v.begin(); it != v.end(); ++it) {
    if (*it % 2 == 0) {
        v.erase(it);  // 错误！it 失效
    }
}

// 正确做法：
for (auto it = v.begin(); it != v.end(); ) {
    if (*it % 2 == 0) {
        it = v.erase(it);  // 获取下一个有效迭代器
    } else {
        ++it;
    }
}
```

---

## 算法与操作

### 常用算法

```cpp
#include <algorithm>
#include <numeric>

vector<int> v = {5, 2, 8, 1, 9, 3, 7};

// 1. 排序
sort(v.begin(), v.end());              // 升序：{1, 2, 3, 5, 7, 8, 9}
sort(v.begin(), v.end(), greater<>());  // 降序：{9, 8, 7, 5, 3, 2, 1}

// 自定义比较函数
sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;  // 降序
});

// 2. 查找
auto it = find(v.begin(), v.end(), 5);
if (it != v.end()) {
    cout << "找到 5，索引：" << (it - v.begin()) << endl;
}

// 二分查找（需要先排序）
sort(v.begin(), v.end());
bool found = binary_search(v.begin(), v.end(), 5);

// 3. 反转
reverse(v.begin(), v.end());

// 4. 累加
int sum = accumulate(v.begin(), v.end(), 0);

// 5. 最大/最小值
auto max_it = max_element(v.begin(), v.end());
auto min_it = min_element(v.begin(), v.end());
cout << "最大值：" << *max_it << endl;
cout << "最小值：" << *min_it << endl;

// 6. 去重（需要先排序）
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());

// 7. 计数
int count = count(v.begin(), v.end(), 5);

// 8. 填充
fill(v.begin(), v.end(), 0);

// 9. 复制
vector<int> v2(v.size());
copy(v.begin(), v.end(), v2.begin());

// 10. 变换
transform(v.begin(), v.end(), v.begin(), [](int x) {
    return x * 2;
});
```

### 排序相关

```cpp
vector<int> v = {5, 2, 8, 1, 9, 3, 7};

// 部分排序
partial_sort(v.begin(), v.begin() + 3, v.end());
// 前 3 个元素已排序

// 第 n 个元素
nth_element(v.begin(), v.begin() + 3, v.end());
// 第 4 个元素在正确位置，左边都 <=，右边都 >=

// 检查是否已排序
bool is_sorted = is_sorted(v.begin(), v.end());

// 稳定排序
stable_sort(v.begin(), v.end());
```

---

## 性能分析

### 时间复杂度总结

| 操作 | 平均 | 最坏 | 说明 |
|------|------|------|------|
| 随机访问 `[]` | O(1) | O(1) | 连续内存 |
| `push_back` | O(1) | O(n) | 均摊 O(1)，扩容时 O(n) |
| `pop_back` | O(1) | O(1) | 仅改变 size |
| `insert` | O(n) | O(n) | 需要移动元素 |
| `erase` | O(n) | O(n) | 需要移动元素 |
| `find` | O(n) | O(n) | 线性查找 |
| `sort` | O(n log n) | O(n log n) | - |

### 空间复杂度
- 存储：O(n)
- 扩容：额外 O(n) 空间（短暂）

### 性能优化技巧

```cpp
// 1. 预分配空间，避免多次扩容
vector<int> v;
v.reserve(1000);  // 如果知道大概需要多少元素
for (int i = 0; i < 1000; i++) {
    v.push_back(i);
}

// 2. 使用 emplace_back 代替 push_back
struct Point {
    int x, y;
    Point(int x, int y) : x(x), y(y) {}
};

vector<Point> points;
// 好：直接在 vector 中构造
points.emplace_back(1, 2);
// 不够好：先构造临时对象，再拷贝/移动
points.push_back(Point(1, 2));

// 3. 使用 move 语义避免拷贝
vector<string> v1 = {"a", "b", "c"};
vector<string> v2 = std::move(v1);  // 移动而非拷贝

// 4. 避免在循环中频繁 insert
// 差：每次都 O(n)
vector<int> v;
for (int i = 0; i < n; i++) {
    v.insert(v.begin(), i);  // O(n)
}

// 好：先 push_back，最后反转
vector<int> v;
for (int i = 0; i < n; i++) {
    v.push_back(i);  // O(1) 均摊
}
reverse(v.begin(), v.end());  // O(n)

// 5. 用 swap trick 释放内存
vector<int> v(1000000);
// ... 使用后只剩几个元素
v.clear();  // size=0，但 capacity 不变
vector<int>().swap(v);  // 释放内存
// 或 C++11：
v.shrink_to_fit();
```

---

## 最佳实践

### 1. 选择合适的容器
```cpp
// vector：大多数情况的首选
// - 需要随机访问
// - 主要在尾部操作
// - 内存连续（缓存友好）

// deque：需要头部插入/删除
// list：需要频繁中间插入/删除
// array：固定大小，栈上分配
```

### 2. 传参方式
```cpp
// 只读：const 引用
void process(const vector<int>& v) {
    for (int x : v) {
        cout << x << " ";
    }
}

// 修改：非 const 引用
void modify(vector<int>& v) {
    for (auto& x : v) {
        x *= 2;
    }
}

// 转移所有权：移动
vector<int> create() {
    vector<int> v = {1, 2, 3};
    return v;  // 自动移动，无拷贝开销
}
```

### 3. 初始化
```cpp
// 好：直接初始化
vector<int> v1 = {1, 2, 3, 4, 5};

// 好：预分配已知大小
vector<int> v2(100, 0);

// 不好：先创建空 vector 再逐个 push_back（已知大小时）
vector<int> v3;
for (int i = 0; i < 100; i++) {
    v3.push_back(0);  // 可能多次扩容
}
```

### 4. 安全访问
```cpp
// 性能关键路径：使用 []
for (int i = 0; i < v.size(); i++) {
    sum += v[i];  // 快
}

// 需要安全检查：使用 at()
int index = getUserInput();
try {
    cout << v.at(index) << endl;  // 越界会抛异常
} catch (...) {
    // 处理错误
}
```

### 5. 避免野指针
```cpp
vector<int> v = {1, 2, 3};
int* ptr = &v[0];

v.push_back(4);  // 可能扩容，ptr 失效
// cout << *ptr;  // 未定义行为！

// 安全做法：
int* ptr = v.data();
// 使用 ptr 期间不要改变 vector 大小
```

---

## 常见陷阱

### 1. 下标越界
```cpp
vector<int> v(5);
v[10] = 1;  // 未定义行为！不会报错，但很危险
```

### 2. 迭代器失效
```cpp
vector<int> v = {1, 2, 3};
auto it = v.begin();
v.push_back(4);  // 可能扩容
*it;  // 未定义行为！
```

### 3. 循环中删除元素
```cpp
// 错误：
for (auto it = v.begin(); it != v.end(); ++it) {
    if (condition) {
        v.erase(it);  // it 失效
    }
}

// 正确：
for (auto it = v.begin(); it != v.end(); ) {
    if (condition) {
        it = v.erase(it);
    } else {
        ++it;
    }
}
```

### 4. reserve vs resize 混淆
```cpp
vector<int> v;
v.reserve(10);
v[0] = 1;  // 错误！元素不存在

v.resize(10);
v[0] = 1;  // 正确
```

### 5. 返回局部 vector 的引用
```cpp
// 错误：
vector<int>& getVector() {
    vector<int> v = {1, 2, 3};
    return v;  // 返回局部变量的引用！
}

// 正确：
vector<int> getVector() {
    vector<int> v = {1, 2, 3};
    return v;  // 移动语义，无拷贝开销
}
```

### 6. 多维 vector 大小设置
```cpp
// 错误：
vector<vector<int>> matrix(m);  // 只设置了行数
matrix[0][0] = 1;  // 越界！列不存在

// 正确：
vector<vector<int>> matrix(m, vector<int>(n, 0));
```

### 7. 性能陷阱
```cpp
// 差：频繁插入头部
for (int i = 0; i < 1000; i++) {
    v.insert(v.begin(), i);  // 每次 O(n)
}

// 好：先 push_back，最后反转
for (int i = 0; i < 1000; i++) {
    v.push_back(i);
}
reverse(v.begin(), v.end());

// 差：每次循环都计算 size()（虽然是 O(1)，但可优化）
for (int i = 0; i < v.size(); i++) {
    // ...
}

// 好：缓存 size
int n = v.size();
for (int i = 0; i < n; i++) {
    // ...
}

// 更好：使用范围 for
for (const auto& elem : v) {
    // ...
}
```

---

## 实战示例

### 示例 1：动态规划数组
```cpp
// 斐波那契数列
vector<long long> fib(int n) {
    vector<long long> dp(n + 1);
    dp[0] = 0;
    dp[1] = 1;
    for (int i = 2; i <= n; i++) {
        dp[i] = dp[i-1] + dp[i-2];
    }
    return dp;
}
```

### 示例 2：统计频率
```cpp
// 统计元素出现次数
vector<int> v = {1, 2, 3, 2, 1, 3, 2, 4};
unordered_map<int, int> freq;
for (int x : v) {
    freq[x]++;
}

// 转换为 vector 并按频率排序
vector<pair<int, int>> items(freq.begin(), freq.end());
sort(items.begin(), items.end(), [](const auto& a, const auto& b) {
    return a.second > b.second;  // 按频率降序
});
```

### 示例 3：滑动窗口
```cpp
// 找出数组中和为 k 的最长子数组
int longestSubarraySum(const vector<int>& nums, int k) {
    int maxLen = 0, sum = 0;
    unordered_map<int, int> prefixSum;
    prefixSum[0] = -1;
    
    for (int i = 0; i < nums.size(); i++) {
        sum += nums[i];
        if (prefixSum.count(sum - k)) {
            maxLen = max(maxLen, i - prefixSum[sum - k]);
        }
        if (!prefixSum.count(sum)) {
            prefixSum[sum] = i;
        }
    }
    return maxLen;
}
```

### 示例 4：矩阵操作
```cpp
// 旋转 90 度
void rotate(vector<vector<int>>& matrix) {
    int n = matrix.size();
    // 转置
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            swap(matrix[i][j], matrix[j][i]);
        }
    }
    // 水平翻转
    for (int i = 0; i < n; i++) {
        reverse(matrix[i].begin(), matrix[i].end());
    }
}
```

---

## 总结

### Vector 适用场景
✅ 需要随机访问
✅ 主要在尾部操作
✅ 需要内存连续（缓存友好）
✅ 不知道确切大小但需要动态增长

### Vector 不适用场景
❌ 频繁在头部或中间插入/删除
❌ 大量随机位置插入/删除
❌ 固定大小且已知（用 array）

### 核心要点
1. **内存连续**：随机访问 O(1)，缓存友好
2. **动态扩容**：1.5~2 倍增长，均摊 O(1)
3. **迭代器失效**：插入/删除/扩容时注意
4. **