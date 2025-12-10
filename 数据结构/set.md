# C++ Set/Multiset/Unordered_set 完整学习笔记

## 目录
1. [基本概念](#基本概念)
2. [三种容器对比](#三种容器对比)
3. [Set 详解](#set-详解)
4. [Multiset 详解](#multiset-详解)
5. [Unordered_set 详解](#unordered_set-详解)
6. [内部实现原理](#内部实现原理)
7. [构造与初始化](#构造与初始化)
8. [元素操作](#元素操作)
9. [查找与访问](#查找与访问)
10. [迭代器](#迭代器)
11. [集合运算](#集合运算)
12. [自定义类型](#自定义类型)
13. [性能分析](#性能分析)
14. [最佳实践](#最佳实践)
15. [常见陷阱](#常见陷阱)

---

## 基本概念

### 什么是 Set?
Set 是 C++ STL 中的关联容器，用于存储**唯一的、有序的**元素集合。

### 三种 Set 容器
```cpp
#include <set>                // set, multiset
#include <unordered_set>      // unordered_set, unordered_multiset
using namespace std;
```

| 容器 | 元素唯一性 | 元素顺序 | 底层实现 | 查找复杂度 |
|------|-----------|---------|---------|-----------|
| `set` | 唯一 | 有序（升序） | 红黑树 | O(log n) |
| `multiset` | 可重复 | 有序（升序） | 红黑树 | O(log n) |
| `unordered_set` | 唯一 | 无序 | 哈希表 | O(1) 平均 |
| `unordered_multiset` | 可重复 | 无序 | 哈希表 | O(1) 平均 |

---

## 三种容器对比

### 快速选择指南

```cpp
// 需要元素有序 → set/multiset
set<int> s1 = {3, 1, 4, 1, 5};
// s1 = {1, 3, 4, 5}  自动排序，去重

// 需要元素有序且可重复 → multiset
multiset<int> s2 = {3, 1, 4, 1, 5};
// s2 = {1, 1, 3, 4, 5}  自动排序，保留重复

// 只需快速查找，不关心顺序 → unordered_set
unordered_set<int> s3 = {3, 1, 4, 1, 5};
// s3 = {1, 3, 4, 5}  无序，去重

// 需要快速查找且可重复 → unordered_multiset
unordered_multiset<int> s4 = {3, 1, 4, 1, 5};
// s4 = {1, 1, 3, 4, 5}  无序，保留重复
```

### 详细对比表

| 特性 | set | multiset | unordered_set |
|------|-----|----------|---------------|
| **元素唯一** | ✅ | ❌ | ✅ |
| **自动排序** | ✅ | ✅ | ❌ |
| **插入** | O(log n) | O(log n) | O(1) 平均 |
| **删除** | O(log n) | O(log n) | O(1) 平均 |
| **查找** | O(log n) | O(log n) | O(1) 平均 |
| **范围查询** | ✅ 高效 | ✅ 高效 | ❌ 需遍历 |
| **有序遍历** | ✅ | ✅ | ❌ |
| **内存开销** | 中等 | 中等 | 较高 |
| **迭代器稳定性** | ✅ | ✅ | ⚠️ rehash时失效 |

### 使用场景对比

```cpp
// 1. 去重 + 排序 → set
vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6};
set<int> s(v.begin(), v.end());
// 自动去重并排序

// 2. 保留重复 + 排序 → multiset
multiset<int> ms(v.begin(), v.end());
// 保留所有元素并排序

// 3. 只需快速查找（大数据量） → unordered_set
unordered_set<int> us(v.begin(), v.end());
// 查找速度最快

// 4. 需要范围查询 → set/multiset
set<int> s = {1, 2, 3, 4, 5, 6, 7, 8, 9};
auto it = s.lower_bound(4);  // 找到 >= 4 的第一个元素
// unordered_set 不支持 lower_bound

// 5. 需要最大/最小值 → set/multiset
set<int> s = {3, 1, 4, 1, 5};
int minVal = *s.begin();      // 最小值
int maxVal = *s.rbegin();     // 最大值
// unordered_set 需要遍历才能找到
```

---

## Set 详解

### 基本特性
- 元素**唯一**
- 自动**排序**（默认升序）
- 基于**红黑树**实现
- 插入/删除/查找：**O(log n)**

### 常用操作

```cpp
set<int> s;

// 1. 插入
s.insert(5);           // 返回 pair<iterator, bool>
s.insert(3);
s.insert(7);
s.insert(3);           // 插入失败，已存在
// s = {3, 5, 7}

auto [it, success] = s.insert(9);
if (success) {
    cout << "插入成功：" << *it << endl;
}

// 批量插入
s.insert({1, 2, 4, 6});

// 2. 删除
s.erase(5);            // 删除值为 5 的元素，返回删除个数
s.erase(s.begin());    // 删除第一个元素
s.erase(s.find(3));    // 删除迭代器指向的元素

// 3. 查找
if (s.find(7) != s.end()) {
    cout << "找到 7" << endl;
}

if (s.count(3) > 0) {  // count 返回 0 或 1
    cout << "3 存在" << endl;
}

// 4. 大小
cout << "size: " << s.size() << endl;
cout << "empty: " << s.empty() << endl;

// 5. 清空
s.clear();

// 6. 范围操作
set<int> s = {1, 2, 3, 4, 5, 6, 7, 8, 9};

// lower_bound: >= x 的第一个元素
auto it1 = s.lower_bound(5);  // 指向 5

// upper_bound: > x 的第一个元素
auto it2 = s.upper_bound(5);  // 指向 6

// equal_range: [lower_bound, upper_bound)
auto [first, last] = s.equal_range(5);
```

### Set 示例代码

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    set<int> s;
    
    // 插入元素
    s.insert(5);
    s.insert(2);
    s.insert(8);
    s.insert(2);  // 重复，不会插入
    
    // 遍历（自动升序）
    for (int x : s) {
        cout << x << " ";  // 2 5 8
    }
    cout << endl;
    
    // 查找
    if (s.find(5) != s.end()) {
        cout << "找到 5" << endl;
    }
    
    // 删除
    s.erase(2);
    
    // 最小值和最大值
    cout << "min: " << *s.begin() << endl;    // 5
    cout << "max: " << *s.rbegin() << endl;   // 8
    
    return 0;
}
```

---

## Multiset 详解

### 基本特性
- 元素**可重复**
- 自动**排序**（默认升序）
- 基于**红黑树**实现
- 插入/删除/查找：**O(log n)**

### 与 Set 的区别

```cpp
set<int> s;
s.insert(5);
s.insert(5);
cout << s.size();  // 1 - 只保留一个

multiset<int> ms;
ms.insert(5);
ms.insert(5);
cout << ms.size();  // 2 - 保留所有重复元素
```

### 常用操作

```cpp
multiset<int> ms;

// 1. 插入（总是成功）
ms.insert(5);
ms.insert(5);
ms.insert(3);
ms.insert(7);
ms.insert(3);
// ms = {3, 3, 5, 5, 7}

// 2. 计数
cout << ms.count(3) << endl;  // 2
cout << ms.count(5) << endl;  // 2
cout << ms.count(9) << endl;  // 0

// 3. 查找（返回第一个匹配的迭代器）
auto it = ms.find(3);
if (it != ms.end()) {
    cout << "找到：" << *it << endl;  // 3
}

// 4. 删除
ms.erase(5);           // 删除所有值为 5 的元素，返回删除个数（2）
ms.erase(ms.find(3));  // 只删除迭代器指向的一个元素

// 5. 删除指定个数
multiset<int> ms2 = {1, 1, 1, 2, 2, 3};
// 只删除一个 1
auto it2 = ms2.find(1);
if (it2 != ms2.end()) {
    ms2.erase(it2);
}
// ms2 = {1, 1, 2, 2, 3}

// 6. 范围查询（获取所有等于某值的元素）
multiset<int> ms3 = {1, 2, 2, 2, 3, 4, 5};
auto [first, last] = ms3.equal_range(2);
cout << "值为 2 的元素个数：" << distance(first, last) << endl;  // 3

// 遍历所有值为 2 的元素
for (auto it = first; it != last; ++it) {
    cout << *it << " ";  // 2 2 2
}
```

### Multiset 应用场景

```cpp
// 1. 维护动态排序序列（可重复）
multiset<int> ms;
for (int x : {5, 2, 8, 2, 9, 1}) {
    ms.insert(x);
}
// 自动保持升序：{1, 2, 2, 5, 8, 9}

// 2. 找第 k 小的元素
multiset<int> ms = {3, 1, 4, 1, 5, 9, 2, 6};
auto it = ms.begin();
advance(it, 3);  // 第 4 小的元素（0-indexed: 第3个）
cout << *it << endl;

// 3. 滑动窗口中位数
multiset<int> window;
vector<int> nums = {1, 3, -1, -3, 5, 3, 6, 7};
int k = 3;

for (int i = 0; i < nums.size(); i++) {
    window.insert(nums[i]);
    
    if (window.size() > k) {
        window.erase(window.find(nums[i - k]));
    }
    
    if (window.size() == k) {
        auto it = window.begin();
        advance(it, k / 2);
        cout << "中位数：" << *it << endl;
    }
}

// 4. 事件调度（按时间排序）
struct Event {
    int time;
    string name;
    bool operator<(const Event& other) const {
        return time < other.time;
    }
};

multiset<Event> events;
events.insert({10, "Meeting"});
events.insert({5, "Call"});
events.insert({10, "Lunch"});  // 同一时间可以有多个事件
```

---

## Unordered_set 详解

### 基本特性
- 元素**唯一**
- **无序**存储
- 基于**哈希表**实现
- 插入/删除/查找：**O(1)** 平均，O(n) 最坏

### 常用操作

```cpp
unordered_set<int> us;

// 1. 插入
us.insert(5);
us.insert(3);
us.insert(7);
us.insert(3);  // 插入失败
// us = {7, 3, 5}  顺序不确定

// 2. 查找
if (us.find(5) != us.end()) {
    cout << "找到 5" << endl;
}

if (us.count(3) > 0) {
    cout << "3 存在" << endl;
}

// 3. 删除
us.erase(5);
us.erase(us.find(3));

// 4. 哈希表参数
cout << "桶数量：" << us.bucket_count() << endl;
cout << "负载因子：" << us.load_factor() << endl;
cout << "最大负载因子：" << us.max_load_factor() << endl;

// 5. 预留空间（避免 rehash）
us.reserve(1000);  // 预留空间容纳 1000 个元素
```

### 与 Set 的性能对比

```cpp
#include <chrono>

// 大量插入操作
set<int> s;
unordered_set<int> us;

auto start1 = chrono::high_resolution_clock::now();
for (int i = 0; i < 1000000; i++) {
    s.insert(i);
}
auto end1 = chrono::high_resolution_clock::now();
cout << "set 插入时间：" << 
    chrono::duration_cast<chrono::milliseconds>(end1 - start1).count() 
    << " ms" << endl;

auto start2 = chrono::high_resolution_clock::now();
for (int i = 0; i < 1000000; i++) {
    us.insert(i);
}
auto end2 = chrono::high_resolution_clock::now();
cout << "unordered_set 插入时间：" << 
    chrono::duration_cast<chrono::milliseconds>(end2 - start2).count() 
    << " ms" << endl;

// 通常 unordered_set 更快
```

### Unordered_multiset

```cpp
unordered_multiset<int> ums;

// 可以插入重复元素，无序
ums.insert(5);
ums.insert(5);
ums.insert(3);
// ums = {5, 5, 3}  顺序不确定

cout << ums.count(5) << endl;  // 2

// 删除所有值为 5 的元素
ums.erase(5);
```

---

## 内部实现原理

### Set/Multiset - 红黑树

```
红黑树特性：
1. 每个节点是红色或黑色
2. 根节点是黑色
3. 所有叶子节点（NIL）是黑色
4. 红色节点的子节点必须是黑色
5. 从任意节点到叶子的所有路径包含相同数量的黑色节点

       [7(黑)]
      /        \
   [3(红)]    [11(红)]
   /    \      /     \
[1(黑)] [5(黑)] [9(黑)] [13(黑)]

优点：
- 自平衡，保证 O(log n) 操作
- 有序遍历高效
- 支持范围查询

缺点：
- 内存开销大（每个节点需要存储颜色、父指针、左右子指针）
- 常数因子较大
```

### Unordered_set/Unordered_multiset - 哈希表

```
哈希表结构：
桶数组 + 链表（或红黑树）

bucket[0] → [key1] → [key5] → NULL
bucket[1] → NULL
bucket[2] → [key2] → [key7] → [key9] → NULL
bucket[3] → [key3] → NULL
...

插入过程：
1. 计算 hash(key)
2. hash % bucket_count 得到桶索引
3. 在桶中插入（链表或树）

优点：
- 平均 O(1) 操作
- 内存使用效率高（数据量大时）

缺点：
- 最坏 O(n)（哈希冲突严重时）
- 不支持有序遍历
- 不支持范围查询
- 需要好的哈希函数
- rehash 时性能下降
```

### 负载因子与 Rehash

```cpp
unordered_set<int> us;
us.max_load_factor(0.75);  // 设置最大负载因子

// 当 size / bucket_count > max_load_factor 时触发 rehash
for (int i = 0; i < 100; i++) {
    us.insert(i);
    cout << "size=" << us.size() 
         << ", buckets=" << us.bucket_count()
         << ", load=" << us.load_factor() << endl;
}

// Rehash 过程：
// 1. 分配更大的桶数组（通常 2 倍）
// 2. 重新计算所有元素的桶位置
// 3. 移动所有元素
// 4. 释放旧数组
// 注意：rehash 会使所有迭代器失效！
```

---

## 构造与初始化

### Set/Multiset

```cpp
// 1. 默认构造
set<int> s1;
multiset<int> ms1;

// 2. 初始化列表
set<int> s2 = {3, 1, 4, 1, 5};     // {1, 3, 4, 5}
multiset<int> ms2 = {3, 1, 4, 1, 5};  // {1, 1, 3, 4, 5}

// 3. 范围构造
vector<int> v = {5, 2, 8, 2, 9};
set<int> s3(v.begin(), v.end());
multiset<int> ms3(v.begin(), v.end());

// 4. 拷贝构造
set<int> s4(s2);
set<int> s5 = s2;

// 5. 移动构造
set<int> s6(std::move(s2));

// 6. 自定义比较函数
set<int, greater<int>> s7 = {1, 2, 3, 4, 5};  // 降序：{5, 4, 3, 2, 1}

// 自定义比较器
auto cmp = [](int a, int b) { return a > b; };
set<int, decltype(cmp)> s8(cmp);
s8.insert({1, 2, 3, 4, 5});  // {5, 4, 3, 2, 1}

// 7. 从其他容器构造
int arr[] = {3, 1, 4, 1, 5};
set<int> s9(arr, arr + 5);
```

### Unordered_set/Unordered_multiset

```cpp
// 1. 默认构造
unordered_set<int> us1;
unordered_multiset<int> ums1;

// 2. 初始化列表
unordered_set<int> us2 = {3, 1, 4, 1, 5};

// 3. 指定桶数量
unordered_set<int> us3(100);  // 初始 100 个桶

// 4. 范围构造 + 桶数量
vector<int> v = {1, 2, 3, 4, 5};
unordered_set<int> us4(v.begin(), v.end(), 50);

// 5. 自定义哈希函数
struct MyHash {
    size_t operator()(int x) const {
        return hash<int>()(x);
    }
};

unordered_set<int, MyHash> us5;

// 6. 自定义哈希和相等函数
struct MyEqual {
    bool operator()(int a, int b) const {
        return a == b;
    }
};

unordered_set<int, MyHash, MyEqual> us6;
```

---

## 元素操作

### 插入操作

```cpp
set<int> s;

// 1. insert - 返回 pair<iterator, bool>
auto [it1, success1] = s.insert(5);
cout << "插入 " << *it1 << (success1 ? " 成功" : " 失败") << endl;

auto [it2, success2] = s.insert(5);  // 失败，已存在
cout << "插入 " << *it2 << (success2 ? " 成功" : " 失败") << endl;

// 2. insert - 批量插入
s.insert({1, 2, 3, 4, 5});
s.insert(v.begin(), v.end());

// 3. emplace - 原地构造
s.emplace(10);
auto [it3, success3] = s.emplace(10);

// 4. emplace_hint - 提供位置提示
auto it4 = s.emplace_hint(s.begin(), 0);
```

### Multiset 插入

```cpp
multiset<int> ms;

// insert 总是成功，返回迭代器
auto it = ms.insert(5);
ms.insert(5);  // 可以重复插入
cout << ms.count(5) << endl;  // 2
```

### 删除操作

```cpp
set<int> s = {1, 2, 3, 4, 5, 6, 7};

// 1. erase by value - 返回删除个数
size_t count = s.erase(3);  // 返回 1（set）或删除的个数（multiset）

// 2. erase by iterator
auto it = s.find(5);
if (it != s.end()) {
    s.erase(it);
}

// 3. erase range
s.erase(s.begin(), s.find(4));  // 删除 [begin, find(4))

// 4. clear - 清空所有元素
s.clear();

// Multiset 删除
multiset<int> ms = {1, 2, 2, 2, 3, 4};
ms.erase(2);  // 删除所有 2，返回 3

// 只删除一个元素
auto it2 = ms.find(2);
if (it2 != ms.end()) {
    ms.erase(it2);  // 只删除一个
}
```

### 修改操作

```cpp
// Set 的元素是常量，不能直接修改！
set<int> s = {1, 2, 3, 4, 5};
// *s.begin() = 10;  // 错误！不能修改

// 要修改，必须先删除再插入
int oldVal = 3, newVal = 30;
auto it = s.find(oldVal);
if (it != s.end()) {
    s.erase(it);
    s.insert(newVal);
}

// 或使用 extract (C++17)
auto node = s.extract(3);
if (!node.empty()) {
    node.value() = 30;
    s.insert(std::move(node));
}
```

---

## 查找与访问

### 基本查找

```cpp
set<int> s = {1, 2, 3, 4, 5, 6, 7};

// 1. find - 返回迭代器
auto it = s.find(5);
if (it != s.end()) {
    cout << "找到：" << *it << endl;
} else {
    cout << "未找到" << endl;
}

// 2. count - 返回元素个数（set: 0 或 1，multiset: 实际个数）
if (s.count(5) > 0) {
    cout << "5 存在" << endl;
}

multiset<int> ms = {1, 2, 2, 2, 3};
cout << "2 的个数：" << ms.count(2) << endl;  // 3

// 3. contains - C++20
if (s.contains(5)) {
    cout << "5 存在" << endl;
}
```

### 范围查询（Set/Multiset 独有）

```cpp
set<int> s = {1, 2, 3, 4, 5, 6, 7, 8, 9};

// 1. lower_bound - 返回 >= x 的第一个元素
auto it1 = s.lower_bound(5);
cout << *it1 << endl;  // 5

auto it2 = s.lower_bound(5.5);
cout << *it2 << endl;  // 6（没有 5.5，返回下一个）

// 2. upper_bound - 返回 > x 的第一个元素
auto it3 = s.upper_bound(5);
cout << *it3 << endl;  // 6

// 3. equal_range - 返回 [lower_bound, upper_bound)
auto [first, last] = s.equal_range(5);
// 对于 set：first 指向 5，last 指向 6（如果存在 5）

// 对于 multiset 更有用
multiset<int> ms = {1, 2, 2, 2, 3, 4, 5};
auto [f, l] = ms.equal_range(2);
cout << "值为 2 的元素个数：" << distance(f, l) << endl;  // 3

for (auto it = f; it != l; ++it) {
    cout << *it << " ";  // 2 2 2
}

// 4. 范围查询应用
set<int> s2 = {10, 20, 30, 40, 50, 60, 70, 80, 90};

// 查询 [25, 65) 范围内的所有元素
auto start = s2.lower_bound(25);  // >= 25
auto end = s2.lower_bound(65);    // >= 65

for (auto it = start; it != end; ++it) {
    cout << *it << " ";  // 30 40 50 60
}
```

### 获取最值

```cpp
set<int> s = {3, 1, 4, 1, 5, 9, 2, 6};

// Set 中的最小值
int minVal = *s.begin();
cout << "min: " << minVal << endl;  // 1

// Set 中的最大值
int maxVal = *s.rbegin();
// 或
int maxVal2 = *prev(s.end());
cout << "max: " << maxVal << endl;  // 9

// Unordered_set 需要遍历
unordered_set<int> us = {3, 1, 4, 1, 5, 9, 2, 6};
int minUS = *min_element(us.begin(), us.end());
int maxUS = *max_element(us.begin(), us.end());
```

---

## 迭代器

### Set/Multiset 迭代器

```cpp
set<int> s = {1, 2, 3, 4, 5};

// 1. 正向迭代器
for (auto it = s.begin(); it != s.end(); ++it) {
    cout << *it << " ";  // 1 2 3 4 5
}

// 2. 常量迭代器
for (auto it = s.cbegin(); it != s.cend(); ++it) {
    cout << *it << " ";
    // *it = 10;  // 错误！
}

// 3. 反向迭代器
for (auto it = s.rbegin(); it != s.rend(); ++it) {
    cout << *it << " ";  // 5 4 3 2 1
}

// 4. 范围 for 循环
for (const auto& elem : s) {
    cout << elem << " ";
}

// 注意：set 的元素是 const 的，不能修改
// for (auto& elem : s) {
//     elem = 10;  // 错误！
// }
```

### Unordered_set 迭代器

```cpp
unordered_set<int> us = {1, 2, 3, 4, 5};

// 1. 正向迭代器（顺序不确定）
for (auto it = us.begin(); it != us.end(); ++it) {
    cout << *it << " ";  // 顺序随机，如：3 1 5 2 4
}

// 2. 常量迭代器
for (auto it = us.cbegin(); it != us.cend(); ++it) {
    cout << *it << " ";
}

// 3. 范围 for 循环
for (const auto& elem : us) {
    cout << elem << " ";
}

// 注意：unordered_set 没有反向迭代器！
// us.rbegin();  // 错误！

// 4. 按桶遍历
for (size_t i = 0; i < us.bucket_count(); ++i) {
    cout << "bucket " << i << ": ";
    for (auto it = us.begin(i); it != us.end(i); ++it) {
        cout << *it << " ";
    }
    cout << endl;
}
```

### 迭代器失效

```cpp
// Set/Multiset - 删除操作会使被删除元素的迭代器失效
set<int> s = {1, 2, 3, 4, 5};
auto it = s.find(3);
s.erase(it);
// *it;  // 未定义行为！it 已失效

// 正确做法：
for (auto it = s.begin(); it != s.end(); ) {
    if (*it % 2 == 0) {
        it = s.erase(it);  // erase 返回下一个有效迭代器
    } else {
        ++it;
    }
}

// Unordered_set - rehash 会使所有迭代器失效
unordered_set<int> us;
us.insert(1);
auto it2 = us.begin();
us.reserve(1000);  // 可能触发 rehash
// *it2;  // 可能失效！
```

---

## 集合运算

### 使用 STL 算法

```cpp
#include <algorithm>

set<int> s1 = {1, 2, 3, 4, 5};
set<int> s2 = {4, 5, 6, 7, 8};

// 1. 并集 (Union)
set<int> unionSet;
set_union(s1.begin(), s1.end(),
          s2.begin(), s2.end(),
          inserter(unionSet, unionSet.begin()));
// unionSet = {1, 2, 3, 4, 5, 6, 7, 8}

// 2. 交集 (Intersection)
set<int> intersectionSet;
set_intersection(s1.begin(), s1.end(),
                 s2.begin(), s2.end(),
                 inserter(intersectionSet, intersectionSet.begin()));
// intersectionSet = {4, 5}

// 3. 差集 (Difference) - s1 中有但 s2 中没有
set<int> differenceSet;
set_difference(s1.begin(), s1.end(),
               s2.begin(), s2.end(),
               inserter(differenceSet, differenceSet.begin()));
// differenceSet = {1, 2, 3}

// 4. 对称差集 (Symmetric Difference) - 在 s1 或 s2 中但不在两者中
set<int> symDiffSet;
set_symmetric_difference(s1.begin(), s1.end(),
                         s2.begin(), s2.end(),
                         inserter(symDiffSet, symDiffSet.begin()));
// symDiffSet = {1, 2, 3, 6, 7, 8}

// 5. 判断包含关系
bool isSubset = includes(s2.begin(), s2.end(),
                         set<int>{4, 5}.begin(), 
                         set<int>{4, 5}.end());
// true - {4, 5} 是 s2 的子集
```

### 手动实现集合运算

```cpp
// 并集
set<int> unionSet(const set<int>& s1, const set<int>& s2) {
    set<int> result = s1;
    result.insert(s2.begin(), s2.end());
    return result;
}

// 交集
set<int> intersectionSet(const set<int>& s1, const set<int>& s2) {
    set<int> result;
    for (int x : s1) {
        if (s2.count(x)) {
            result.insert(x);
        }
    }
    return result;
}

// 差集
set<int> differenceSet(const set<int>& s1, const set<int>& s2) {
    set<int> result;
    for (int x : s1) {
        if (!s2.count(x)) {
            result.insert(x);
        }
    }
    return result;
}

// 对称差集
set<int> symDiffSet(const set<int>& s1, const set<int>& s2) {
    set<int> result;
    for (int x : s1) {
        if (!s2.count(x)) {
            result.insert(x);
        }
    }
    for (int x : s2) {
        if (!s1.count(x)) {
            result.insert(x);
        }
    }
    return result;
}

// 判断是否为子集
bool isSubset(const set<int>& subset, const set<int>& superset) {
    for (int x : subset) {
        if (!superset.count(x)) {
            return false;
        }
    }
    return true;
}
```

### Unordered_set 集合运算

```cpp
// Unordered_set 不能直接使用 set_union 等算法
// 需要手动实现

unordered_set<int> us1 = {1, 2, 3, 4, 5};
unordered_set<int> us2 = {4, 5, 6, 7, 8};

// 并集
unordered_set<int> unionUS = us1;
unionUS.insert(us2.begin(), us2.end());

// 交集
unordered_set<int> intersectionUS;
for (int x : us1) {
    if (us2.count(x)) {
        intersectionUS.insert(x);
    }
}

// 差集
unordered_set<int> differenceUS;
for (int x : us1) {
    if (!us2.count(x)) {
        differenceUS.insert(x);
    }
}
```

---

## 自定义类型

### 使用 Set 存储自定义类型

```cpp
// 1. 方法一：重载 operator<
struct Point {
    int x, y;
    
    bool operator<(const Point& other) const {
        if (x != other.x) return x < other.x;
        return y < other.y;
    }
};

set<Point> points;
points.insert({1, 2});
points.insert({3, 4});
points.insert({1, 2});  // 不会插入，已存在

// 2. 方法二：自定义比较器
struct Point2 {
    int x, y;
};

struct PointComparator {
    bool operator()(const Point2& a, const Point2& b) const {
        if (a.x != b.x) return a.x < b.x;
        return a.y < b.y;
    }
};

set<Point2, PointComparator> points2;
points2.insert({1, 2});
points2.insert({3, 4});

// 3. 方法三：使用 lambda（C++11）
auto cmp = [](const Point2& a, const Point2& b) {
    if (a.x != b.x) return a.x < b.x;
    return a.y < b.y;
};
set<Point2, decltype(cmp)> points3(cmp);

// 4. 复杂对象示例
struct Student {
    string name;
    int age;
    double gpa;
    
    bool operator<(const Student& other) const {
        // 按 GPA 降序，GPA 相同按姓名升序
        if (abs(gpa - other.gpa) > 1e-9) {
            return gpa > other.gpa;
        }
        return name < other.name;
    }
};

set<Student> students;
students.insert({"Alice", 20, 3.8});
students.insert({"Bob", 21, 3.6});
students.insert({"Charlie", 19, 3.8});

// 遍历（自动按 GPA 降序，GPA 相同按姓名升序）
for (const auto& s : students) {
    cout << s.name << " " << s.gpa << endl;
}
```

### 使用 Unordered_set 存储自定义类型

```cpp
// 需要提供哈希函数和相等比较函数

struct Point {
    int x, y;
    
    bool operator==(const Point& other) const {
        return x == other.x && y == other.y;
    }
};

// 方法一：特化 std::hash
namespace std {
    template<>
    struct hash<Point> {
        size_t operator()(const Point& p) const {
            // 组合哈希值
            return hash<int>()(p.x) ^ (hash<int>()(p.y) << 1);
        }
    };
}

unordered_set<Point> points;
points.insert({1, 2});
points.insert({3, 4});

// 方法二：自定义哈希函数
struct PointHash {
    size_t operator()(const Point& p) const {
        return hash<int>()(p.x) ^ (hash<int>()(p.y) << 1);
    }
};

struct PointEqual {
    bool operator()(const Point& a, const Point& b) const {
        return a.x == b.x && a.y == b.y;
    }
};

unordered_set<Point, PointHash, PointEqual> points2;

// 方法三：使用 lambda（C++20）
auto hash = [](const Point& p) {
    return hash<int>()(p.x) ^ (hash<int>()(p.y) << 1);
};
auto equal = [](const Point& a, const Point& b) {
    return a.x == b.x && a.y == b.y;
};
unordered_set<Point, decltype(hash), decltype(equal)> points3(10, hash, equal);

// 复杂对象的哈希示例
struct Student {
    string name;
    int age;
    double gpa;
    
    bool operator==(const Student& other) const {
        return name == other.name && age == other.age && 
               abs(gpa - other.gpa) < 1e-9;
    }
};

struct StudentHash {
    size_t operator()(const Student& s) const {
        size_t h1 = hash<string>()(s.name);
        size_t h2 = hash<int>()(s.age);
        size_t h3 = hash<double>()(s.gpa);
        return h1 ^ (h2 << 1) ^ (h3 << 2);
    }
};

unordered_set<Student, StudentHash> students;
```

---

## 性能分析

### 时间复杂度对比

| 操作 | set/multiset | unordered_set/unordered_multiset |
|------|-------------|----------------------------------|
| **插入** | O(log n) | O(1) 平均，O(n) 最坏 |
| **删除** | O(log n) | O(1) 平均，O(n) 最坏 |
| **查找** | O(log n) | O(1) 平均，O(n) 最坏 |
| **遍历** | O(n) 有序 | O(n) 无序 |
| **lower_bound** | O(log n) | 不支持 |
| **upper_bound** | O(log n) | 不支持 |
| **min/max** | O(1) | O(n) |

### 空间复杂度

```cpp
// Set/Multiset (红黑树)
// 每个节点：value + color + parent指针 + 左右子指针
// 空间开销：约 value_size + 32 字节（64位系统）

// Unordered_set (哈希表)
// 桶数组 + 链表节点
// 空间开销：约 value_size + 指针 + 桶数组
// 负载因子越小，空间开销越大
```

### 性能测试示例

```cpp
#include <chrono>
#include <random>

void performanceTest() {
    const int N = 1000000;
    vector<int> data(N);
    
    // 生成随机数据
    random_device rd;
    mt19937 gen(rd());
    uniform_int_distribution<> dis(1, N * 10);
    for (int& x : data) {
        x = dis(gen);
    }
    
    // 测试 set 插入
    auto start = chrono::high_resolution_clock::now();
    set<int> s;
    for (int x : data) {
        s.insert(x);
    }
    auto end = chrono::high_resolution_clock::now();
    cout << "set 插入时间: " 
         << chrono::duration_cast<chrono::milliseconds>(end - start).count()
         << " ms" << endl;
    
    // 测试 unordered_set 插入
    start = chrono::high_resolution_clock::now();
    unordered_set<int> us;
    for (int x : data) {
        us.insert(x);
    }
    end = chrono::high_resolution_clock::now();
    cout << "unordered_set 插入时间: " 
         << chrono::duration_cast<chrono::milliseconds>(end - start).count()
         << " ms" << endl;
    
    // 测试查找
    start = chrono::high_resolution_clock::now();
    for (int i = 0; i < 10000; i++) {
        s.find(dis(gen));
    }
    end = chrono::high_resolution_clock::now();
    cout << "set 查找时间: " 
         << chrono::duration_cast<chrono::milliseconds>(end - start).count()
         << " ms" << endl;
    
    start = chrono::high_resolution_clock::now();
    for (int i = 0; i < 10000; i++) {
        us.find(dis(gen));
    }
    end = chrono::high_resolution_clock::now();
    cout << "unordered_set 查找时间: " 
         << chrono::duration_cast<chrono::milliseconds>(end - start).count()
         << " ms" << endl;
}

// 典型结果（1000000 个元素）：
// set 插入时间: 450 ms
// unordered_set 插入时间: 320 ms
// set 查找时间: 8 ms
// unordered_set 查找时间: 3 ms
```

### 选择建议

```cpp
// 使用 set/multiset 的情况：
// ✅ 需要有序遍历
// ✅ 需要范围查询（lower_bound, upper_bound）
// ✅ 需要频繁获取最大/最小值
// ✅ 需要稳定的性能（避免最坏情况）
// ✅ 元素数量较小（<10000）

// 使用 unordered_set/unordered_multiset 的情况：
// ✅ 只需快速查找/插入/删除
// ✅ 不关心元素顺序
// ✅ 元素数量较大（>10000）
// ✅ 有好的哈希函数
// ✅ 内存充足（可以承受空间开销）
```

---

## 最佳实践

### 1. 选择合适的容器

```cpp
// 去重 + 排序
vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6};
set<int> s(v.begin(), v.end());  // {1, 2, 3, 4, 5, 6, 9}

// 只需去重，不需排序
unordered_set<int> us(v.begin(), v.end());  // 更快

// 需要保留重复 + 排序
multiset<int> ms(v.begin(), v.end());  // {1, 1, 2, 3, 4, 5, 6, 9}

// 判断是否存在
unordered_set<int> lookup = {1, 2, 3, 4, 5};
if (lookup.count(3)) {
    // O(1) 平均
}
```

### 2. 预留空间（Unordered_set）

```cpp
// 差：频繁 rehash
unordered_set<int> us;
for (int i = 0; i < 1000000; i++) {
    us.insert(i);  // 可能多次 rehash
}

// 好：预留空间
unordered_set<int> us2;
us2.reserve(1000000);  // 避免 rehash
for (int i = 0; i < 1000000; i++) {
    us2.insert(i);
}
```

### 3. 使用 emplace 而非 insert

```cpp
struct Point {
    int x, y;
    Point(int x, int y) : x(x), y(y) {
        cout << "构造" << endl;
    }
};

set<Point> s;

// 差：先构造临时对象，再拷贝/移动
s.insert(Point(1, 2));  // 2 次构造

// 好：直接在 set 中构造
s.emplace(1, 2);  // 1 次构造
```

### 4. 批量操作

```cpp
set<int> s;

// 差：多次插入
for (int x : {1, 2, 3, 4, 5}) {
    s.insert(x);
}

// 好：批量插入
s.insert({1, 2, 3, 4, 5});

vector<int> v = {6, 7, 8, 9, 10};
s.insert(v.begin(), v.end());
```

### 5. 避免不必要的查找

```cpp
set<int> s;

// 差：查找两次
if (s.find(5) != s.end()) {
    s.erase(5);
}

// 好：直接删除（返回删除个数）
if (s.erase(5) > 0) {
    cout << "删除成功" << endl;
}

// 插入时检查
// 差：查找 + 插入
if (s.find(5) == s.end()) {
    s.insert(5);
}

// 好：直接插入（检查返回值）
auto [it, success] = s.insert(5);
if (success) {
    cout << "插入成功" << endl;
}
```

### 6. 正确遍历与删除

```cpp
set<int> s = {1, 2, 3, 4, 5, 6, 7, 8, 9};

// 错误：迭代器失效
for (auto it = s.begin(); it != s.end(); ++it) {
    if (*it % 2 == 0) {
        s.erase(it);  // it 失效！
    }
}

// 正确：使用 erase 返回值
for (auto it = s.begin(); it != s.end(); ) {
    if (*it % 2 == 0) {
        it = s.erase(it);  // 获取下一个有效迭代器
    } else {
        ++it;
    }
}

// C++20 更简洁的方式
std::erase_if(s, [](int x) { return x % 2 == 0; });
```

### 7. 传参方式

```cpp
// 只读：const 引用
void process(const set<int>& s) {
    for (int x : s) {
        cout << x << " ";
    }
}

// 修改：非 const 引用
void modify(set<int>& s) {
    s.insert(100);
}

// 转移所有权：移动
set<int> create() {
    set<int> s = {1, 2, 3};
    return s;  // 自动移动
}
```

---

## 常见陷阱

### 1. Set 元素不可修改

```cpp
set<int> s = {1, 2, 3};

// 错误：不能修改元素
// *s.begin() = 10;

// 正确：删除后重新插入
auto it = s.find(2);
if (it != s.end()) {
    int val = *it;
    s.erase(it);
    s.insert(val * 10);
}

// C++17: 使用 extract
auto node = s.extract(2);
if (!node.empty()) {
    node.value() = 20;
    s.insert(std::move(node));
}
```

### 2. 自定义类型忘记定义比较/哈希

```cpp
struct Point {
    int x, y;
};

// 错误：Point 没有定义 operator<
// set<Point> points;  // 编译错误！

// 正确：定义 operator<
struct Point2 {
    int x, y;
    bool operator<(const Point2& other) const {
        return x < other.x || (x == other.x && y < other.y);
    }
};
set<Point2> points;

// 或使用自定义比较器
auto cmp = [](const Point& a, const Point& b) {
    return a.x < b.x || (a.x == b.x && a.y < b.y);
};
set<Point, decltype(cmp)> points2(cmp);
```

### 3. Multiset 删除陷阱

```cpp
multiset<int> ms = {1, 2, 2, 2, 3};

// 删除所有 2
ms.erase(2);  // ms = {1, 3}

// 只删除一个 2
ms = {1, 2, 2, 2, 3};
auto it = ms.find(2);
if (it != ms.end()) {
    ms.erase(it);  // ms = {1, 2, 2, 3}
}
```

### 4. Unordered_set 迭代器失效

```cpp
unordered_set<int> us = {1, 2, 3};
auto it = us.begin();

// rehash 会使所有迭代器失效
us.reserve(1000);
// *it;  // 可能失效！

// 安全做法：reserve 后重新获取迭代器
us.reserve(1000);
it = us.begin();
```

### 5. 比较器一致性

```cpp
// 错误：比较器不一致
struct BadComp {
    bool operator()(int a, int b) const {
        return abs(a) < abs(b);  // 对于 -2 和 2，两个方向都返回 false
    }
};

set<int, BadComp> s;  // 未定义行为！

// 正确：严格弱序
struct GoodComp {
    bool operator()(int a, int b) const {
        if (abs(a) != abs(b)) return abs(a) < abs(b);
        return a < b;  // 绝对值相同时比较原值
    }
};
```

### 6. Set 不支持下标访问

```cpp
set<int> s = {1, 2, 3, 4, 5};

// 错误：set 不支持 []
// cout << s[2];  // 编译错误！

// 正确：使用迭代器
auto it = s.begin();
advance(it, 2);
cout << *it << endl;  // 3

// 或使用 next
cout << *next(s.begin(), 2) << endl;  // 3
```

### 7. 哈希冲突导致性能下降

```cpp
// 差的哈希函数
struct BadHash {
    size_t operator()(int x) const {
        return x % 10;  // 大量冲突
    }
};

unordered_set<int, BadHash> us;
for (int i = 0; i < 1000; i++) {
    us.insert(i);  // 性能退化到 O(n)
}

// 好的哈希函数
struct GoodHash {
    size_t operator()(int x) const {
        return hash<int>()(x);  // 使用标准哈希
    }
};
```

### 8. Count vs Find

```cpp
set<int> s = {1, 2, 3, 4, 5};

// 低效：查找两次
if (s.count(3) > 0) {
    auto it = s.find(3);
    cout << *it << endl;
}

// 高效：只查找一次
auto it = s.find(3);
if (it != s.end()) {
    cout << *it << endl;
}
```

---

## 实战应用示例

### 1. 去重

```cpp
// 数组去重
vector<int> v = {1, 2, 2, 3, 3, 3, 4, 5};
set<int> s(v.begin(), v.end());
v.assign(s.begin(), s.end());  // v = {1, 2, 3, 4, 5}

// 字符串去重
string str = "hello world";
set<char> charSet(str.begin(), str.end());
str.assign(charSet.begin(), charSet.end());  // str = " dehlorw"
```

### 2. 判断是否有重复元素

```cpp
bool hasDuplicate(const vector<int>& nums) {
    unordered_set<int> seen;
    for (int x : nums) {
        if (seen.count(x)) {
            return true;
        }
        seen.insert(x);
    }
    return false;
}

// 或更简洁：
bool hasDuplicate2(const vector<int>& nums) {
    unordered_set<int> s(nums.begin(), nums.end());
    return s.size() != nums.size();
}
```

### 3. 两数之和

```cpp
vector<int> twoSum(const vector<int>& nums, int target) {
    unordered_set<int> seen;
    for (int i = 0; i < nums.size(); i++) {
        int complement = target - nums[i];
        if (seen.count(complement)) {
            return {complement, nums[i]};
        }
        seen.insert(nums[i]);
    }
    return {};
}
```

### 4. 最长连续序列

```cpp
int longestConsecutive(vector<int>& nums) {
    unordered_set<int> numSet(nums.begin(), nums.end());
    int maxLen = 0;
    
    for (int num : numSet) {
        // 只从序列的起点开始
        if (!numSet.count(num - 1)) {
            int currentNum = num;
            int currentLen = 1;
            
            while (numSet.count(currentNum + 1)) {
                currentNum++;
                currentLen++;
            }
            
            maxLen = max(maxLen, currentLen);
        }
    }
    
    return maxLen;
}
```

### 5. 前 K 个高频元素

```cpp
vector<int> topKFrequent(vector<int>& nums, int k) {
    // 统计频率
    unordered_map<int, int> freq;
    for (int x : nums) {
        freq[x]++;
    }
    
    // 使用 multiset 按频率排序
    multiset<pair<int, int>> freqSet;  // {频率, 值}
    for (auto [num, count] : freq) {
        freqSet.insert({count, num});
        if (freqSet.size() > k) {
            freqSet.erase(freqSet.begin());  // 删除频率最小的
        }
    }
    
    vector<int> result;
    for (auto [count, num] : freqSet) {
        result.push_back(num);
    }
    return result;
}
```

### 6. 滑动窗口唯一元素

```cpp
vector<int> slidingWindowUnique(vector<int>& nums, int k) {
    vector<int> result;
    unordered_set<int> window;
    
    for (int i = 0; i < nums.size(); i++) {
        window.insert(nums[i]);
        
        if (i >= k) {
            window.erase(nums[i - k]);
        }
        
        if (i >= k - 1) {
            result.push_back(window.size());