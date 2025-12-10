# C++ size_t 简明笔记

## 什么是 size_t？

`size_t` 是 C++ 中的一个**无符号整数类型**，用于表示**对象的大小**和**数组索引**。

### 核心特点

- ✅ **无符号类型**：只能表示非负整数（≥ 0）
- ✅ **足够大**：保证能表示系统中任何对象的大小
- ✅ **平台相关**：
  - 32位系统：通常是 `unsigned int`（4字节）
  - 64位系统：通常是 `unsigned long`（8字节）

---

## 基本用法

### 1. sizeof 返回 size_t

```cpp
#include <iostream>
using namespace std;

int main() {
    int arr[10];
    
    size_t size = sizeof(arr);      // 返回 size_t 类型
    cout << "Size: " << size << endl;  // 40 (假设 int 是 4 字节)
    
    size_t len = sizeof(arr) / sizeof(arr[0]);
    cout << "Length: " << len << endl;  // 10
    
    return 0;
}
```

### 2. 字符串和容器的 size() / length()

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;

int main() {
    string str = "Hello";
    size_t len = str.size();        // 返回 size_t
    cout << "Length: " << len << endl;  // 5
    
    vector<int> vec = {1, 2, 3, 4, 5};
    size_t count = vec.size();      // 返回 size_t
    cout << "Count: " << count << endl;  // 5
    
    return 0;
}
```

### 3. 字符串查找函数

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello World";
    
    // find、rfind 等返回 size_t
    size_t pos = str.find("World");
    
    if (pos != string::npos) {  // npos 是 size_t 的最大值
        cout << "Found at: " << pos << endl;  // 6
    } else {
        cout << "Not found" << endl;
    }
    
    return 0;
}
```

---

## 常见陷阱

### ⚠️ 陷阱1：无符号类型的比较

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3};
    
    // ❌ 危险：size_t 是无符号的，永远 ≥ 0
    for (size_t i = vec.size() - 1; i >= 0; i--) {
        cout << vec[i] << " ";
        // 当 i = 0 时，i-- 会变成很大的正数（下溢）！
        // 导致无限循环或越界访问
    }
    
    return 0;
}
```

**正确写法：**

```cpp
// 方法1：使用有符号类型
for (int i = vec.size() - 1; i >= 0; i--) {
    cout << vec[i] << " ";
}

// 方法2：使用不同的循环逻辑
for (size_t i = 0; i < vec.size(); i++) {
    cout << vec[vec.size() - 1 - i] << " ";
}

// 方法3：使用反向迭代器
for (auto it = vec.rbegin(); it != vec.rend(); ++it) {
    cout << *it << " ";
}
```

### ⚠️ 陷阱2：与负数比较

```cpp
#include <iostream>
using namespace std;

int main() {
    size_t a = 5;
    int b = -1;
    
    // ❌ 危险：b 会被转换为无符号类型
    if (a > b) {
        cout << "a > b" << endl;  // 不会执行！
    } else {
        cout << "a <= b" << endl; // 会执行！
    }
    
    // 原因：-1 转换为 size_t 后变成最大值
    cout << "b as size_t: " << (size_t)b << endl;
    // 输出：18446744073709551615 (64位系统)
    
    return 0;
}
```

**正确写法：**

```cpp
// 显式转换为相同类型
if (a > (size_t)b || b < 0) {
    cout << "a > b" << endl;
}

// 或者使用有符号类型
int a_signed = static_cast<int>(a);
if (a_signed > b) {
    cout << "a > b" << endl;
}
```

### ⚠️ 陷阱3：减法下溢

```cpp
#include <iostream>
using namespace std;

int main() {
    size_t a = 5;
    size_t b = 10;
    
    // ❌ 危险：结果应该是负数，但 size_t 是无符号的
    size_t diff = a - b;  // 下溢！
    cout << "Diff: " << diff << endl;
    // 输出一个很大的正数，而不是 -5
    
    return 0;
}
```

**正确写法：**

```cpp
// 方法1：先比较大小
if (a > b) {
    size_t diff = a - b;
} else {
    size_t diff = b - a;
}

// 方法2：使用有符号类型
int diff = static_cast<int>(a) - static_cast<int>(b);
```

---

## 与 int 的比较

| 特性 | size_t | int |
|------|--------|-----|
| 符号 | 无符号 | 有符号 |
| 范围 | 0 ~ 最大值 | 负数 ~ 正数 |
| 用途 | 大小、索引 | 通用整数 |
| 编译器警告 | 与 int 比较时可能警告 | 无 |
| 下溢风险 | ✅ 有 | ❌ 无 |

```cpp
#include <iostream>
using namespace std;

int main() {
    size_t s = 10;
    int i = 10;
    
    // 可能产生编译器警告
    if (s == i) {  // 比较无符号和有符号
        cout << "Equal" << endl;
    }
    
    return 0;
}
```

---

## string::npos 详解

`string::npos` 是 `size_t` 类型的特殊值，表示"未找到"。

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    // npos 是 size_t 的最大值
    cout << "npos = " << string::npos << endl;
    // 64位系统输出: 18446744073709551615
    
    string str = "Hello World";
    size_t pos = str.find("xyz");
    
    if (pos == string::npos) {
        cout << "Not found" << endl;
    }
    
    // ⚠️ 错误用法：与 -1 比较
    // if (pos == -1) { }  // 不推荐！应该用 npos
    
    return 0;
}
```

### 为什么用 npos？

```cpp
// ❌ 错误：-1 会被转换为 size_t 的最大值
size_t pos = str.find("xyz");  // 返回 npos（最大值）
if (pos == -1) {  // -1 转为 size_t 也是最大值
    // 这样能工作，但不推荐
}

// ✅ 正确：使用 npos 更清晰、更安全
if (pos == string::npos) {
    cout << "Not found" << endl;
}
```

---

## 实用技巧

### 技巧1：安全的循环

```cpp
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3, 4, 5};
    
    // ✅ 正向遍历：安全
    for (size_t i = 0; i < vec.size(); i++) {
        cout << vec[i] << " ";
    }
    
    // ✅ 反向遍历：使用不同的逻辑
    for (size_t i = vec.size(); i > 0; i--) {
        cout << vec[i - 1] << " ";
    }
    
    // ✅ 使用迭代器：最推荐
    for (auto x : vec) {
        cout << x << " ";
    }
    
    return 0;
}
```

### 技巧2：类型转换

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello";
    
    // size_t 转 int
    int len = static_cast<int>(str.size());
    
    // int 转 size_t（确保非负）
    int num = 10;
    size_t idx = static_cast<size_t>(num);
    
    return 0;
}
```

### 技巧3：避免编译器警告

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3};
    
    // ⚠️ 可能产生警告：比较有符号和无符号
    for (int i = 0; i < vec.size(); i++) {
        // ...
    }
    
    // ✅ 解决方案1：使用 size_t
    for (size_t i = 0; i < vec.size(); i++) {
        // ...
    }
    
    // ✅ 解决方案2：显式转换
    for (int i = 0; i < static_cast<int>(vec.size()); i++) {
        // ...
    }
    
    // ✅ 解决方案3：使用 auto（C++11）
    for (auto i = 0; i < vec.size(); i++) {
        // i 自动推导为 int（因为 0 是 int）
    }
    
    return 0;
}
```

---

## 快速参考

### 常见函数返回 size_t

| 函数 | 返回类型 | 示例 |
|------|----------|------|
| `sizeof()` | `size_t` | `sizeof(int)` |
| `strlen()` | `size_t` | `strlen("hello")` |
| `string::size()` | `size_t` | `str.size()` |
| `string::length()` | `size_t` | `str.length()` |
| `string::find()` | `size_t` | `str.find("x")` |
| `string::rfind()` | `size_t` | `str.rfind("x")` |
| `vector::size()` | `size_t` | `vec.size()` |

### 何时使用 size_t？

✅ **推荐使用：**
- 数组/容器的大小和索引
- 循环计数器（正向遍历）
- 字符串长度和位置
- 与 `size()`、`sizeof` 等返回值比较

⚠️ **谨慎使用：**
- 需要负数的场合
- 反向循环（容易下溢）
- 与有符号类型混合运算

---

## 总结

### 核心要点

1. `size_t` 是**无符号整数类型**，表示大小和索引
2. **不能为负**，减法可能下溢
3. **反向循环**时要特别小心
4. 使用 `string::npos` 检查"未找到"，不要用 -1
5. 与 int 比较时可能产生警告

### 记住

> **size_t 用于大小和索引，永远非负；反向循环需谨慎，避免下溢陷阱。**

### 最佳实践

```cpp
// ✅ 推荐
for (size_t i = 0; i < vec.size(); i++)  // 正向遍历
if (pos != string::npos)                  // 检查未找到
size_t len = str.size();                  // 存储大小

// ❌ 避免
for (size_t i = n-1; i >= 0; i--)        // 反向循环下溢
if (pos == -1)                            // 用 -1 而不是 npos
size_t diff = a - b;  // b > a           // 减法下溢
```