# C++ 迭代器详细笔记

## 目录
1. [基础概念](#基础概念)
2. [迭代器类型](#迭代器类型)
3. [容器的迭代器](#容器的迭代器)
4. [迭代器操作](#迭代器操作)
5. [范围for与迭代器](#范围for与迭代器)
6. [迭代器适配器](#迭代器适配器)
7. [算法与迭代器](#算法与迭代器)
8. [自定义迭代器](#自定义迭代器)
9. [迭代器失效问题](#迭代器失效问题)

---

## 基础概念

### 什么是迭代器？

**迭代器（Iterator）**是一种设计模式，提供了一种**统一的方式**来访问容器中的元素，而不需要暴露容器的内部结构。

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3, 4, 5};
    
    // 使用迭代器遍历
    for (auto it = vec.begin(); it != vec.end(); ++it) {
        cout << *it << " ";  // 解引用获取元素
    }
    cout << endl;
    
    return 0;
}
```

### 迭代器的本质

迭代器本质上是一个**类指针对象**，重载了以下运算符：
- `*` - 解引用，访问元素
- `++` - 移动到下一个元素
- `==` / `!=` - 比较两个迭代器
- `--` - 移动到上一个元素（双向迭代器）
- `+` / `-` - 随机访问（随机访问迭代器）

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {10, 20, 30};
    
    auto it = vec.begin();
    
    cout << *it << endl;      // 10 (解引用)
    ++it;                     // 移动到下一个
    cout << *it << endl;      // 20
    
    if (it != vec.end()) {    // 比较
        cout << "Not at end" << endl;
    }
    
    return 0;
}
```

### 为什么需要迭代器？

#### 1. **统一接口**
不同容器有不同的内部实现，但迭代器提供了统一的访问方式。

```cpp
#include <iostream>
#include <vector>
#include <list>
#include <set>
using namespace std;

template<typename Container>
void printContainer(const Container& c) {
    // 对所有容器都适用
    for (auto it = c.begin(); it != c.end(); ++it) {
        cout << *it << " ";
    }
    cout << endl;
}

int main() {
    vector<int> vec = {1, 2, 3};
    list<int> lst = {4, 5, 6};
    set<int> st = {7, 8, 9};
    
    printContainer(vec);  // vector
    printContainer(lst);  // list
    printContainer(st);   // set
    
    return 0;
}
```

#### 2. **解耦容器和算法**
STL 算法通过迭代器操作容器，不需要知道容器的具体类型。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> vec = {5, 2, 8, 1, 9};
    
    // sort 通过迭代器工作
    sort(vec.begin(), vec.end());
    
    for (int x : vec) {
        cout << x << " ";  // 1 2 5 8 9
    }
    cout << endl;
    
    return 0;
}
```

---

## 迭代器类型

C++ 定义了**5种迭代器类型**，功能逐渐增强。

### 1. 输入迭代器 (Input Iterator)

**特点**：
- 只读，单向移动
- 只能遍历一次
- 支持：`*it`（读取）、`++it`、`it++`、`==`、`!=`

```cpp
#include <iostream>
#include <iterator>
using namespace std;

int main() {
    // istream_iterator 是输入迭代器
    istream_iterator<int> input(cin);
    istream_iterator<int> eof;
    
    // 从标准输入读取整数
    while (input != eof) {
        cout << "Read: " << *input << endl;
        ++input;
    }
    
    return 0;
}
```

### 2. 输出迭代器 (Output Iterator)

**特点**：
- 只写，单向移动
- 支持：`*it = value`（写入）、`++it`、`it++`

```cpp
#include <iostream>
#include <iterator>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3, 4, 5};
    
    // ostream_iterator 是输出迭代器
    ostream_iterator<int> output(cout, " ");
    
    // 输出到标准输出
    copy(vec.begin(), vec.end(), output);
    cout << endl;
    
    return 0;
}
```

### 3. 前向迭代器 (Forward Iterator)

**特点**：
- 可读写，单向移动
- 可以多次遍历
- 支持：输入迭代器的所有操作 + 可写

```cpp
#include <iostream>
#include <forward_list>
using namespace std;

int main() {
    forward_list<int> flist = {1, 2, 3, 4, 5};
    
    // forward_list 使用前向迭代器
    for (auto it = flist.begin(); it != flist.end(); ++it) {
        *it *= 2;  // 可以修改
        cout << *it << " ";
    }
    cout << endl;
    
    return 0;
}
```

### 4. 双向迭代器 (Bidirectional Iterator)

**特点**：
- 可读写，双向移动
- 支持：前向迭代器的所有操作 + `--it`、`it--`

```cpp
#include <iostream>
#include <list>
using namespace std;

int main() {
    list<int> lst = {1, 2, 3, 4, 5};
    
    // list 使用双向迭代器
    auto it = lst.end();
    
    // 反向遍历
    while (it != lst.begin()) {
        --it;
        cout << *it << " ";  // 5 4 3 2 1
    }
    cout << endl;
    
    return 0;
}
```

### 5. 随机访问迭代器 (Random Access Iterator)

**特点**：
- 最强大的迭代器
- 支持：双向迭代器的所有操作 + 随机访问
- 支持：`it + n`、`it - n`、`it[n]`、`<`、`>`、`<=`、`>=`

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {10, 20, 30, 40, 50};
    
    auto it = vec.begin();
    
    // 随机访问
    cout << *(it + 2) << endl;     // 30
    cout << it[3] << endl;         // 40
    
    // 指针算术
    auto it2 = it + 4;
    cout << *it2 << endl;          // 50
    
    // 距离计算
    cout << (it2 - it) << endl;    // 4
    
    // 比较
    if (it < it2) {
        cout << "it < it2" << endl;
    }
    
    return 0;
}
```

### 迭代器类型对比表

| 迭代器类型 | 读 | 写 | 前进 | 后退 | 随机访问 | 典型容器 |
|------------|----|----|------|------|----------|----------|
| 输入迭代器 | ✅ | ❌ | ✅ | ❌ | ❌ | istream |
| 输出迭代器 | ❌ | ✅ | ✅ | ❌ | ❌ | ostream |
| 前向迭代器 | ✅ | ✅ | ✅ | ❌ | ❌ | forward_list |
| 双向迭代器 | ✅ | ✅ | ✅ | ✅ | ❌ | list, set, map |
| 随机访问迭代器 | ✅ | ✅ | ✅ | ✅ | ✅ | vector, deque, array |

---

## 容器的迭代器

### vector - 随机访问迭代器

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3, 4, 5};
    
    // 正向迭代器
    for (auto it = vec.begin(); it != vec.end(); ++it) {
        cout << *it << " ";
    }
    cout << endl;
    
    // 反向迭代器
    for (auto it = vec.rbegin(); it != vec.rend(); ++it) {
        cout << *it << " ";  // 5 4 3 2 1
    }
    cout << endl;
    
    // 随机访问
    auto it = vec.begin();
    cout << it[2] << endl;        // 3
    cout << *(it + 3) << endl;    // 4
    
    return 0;
}
```

### list - 双向迭代器

```cpp
#include <iostream>
#include <list>
using namespace std;

int main() {
    list<int> lst = {1, 2, 3, 4, 5};
    
    auto it = lst.begin();
    
    // 可以前进
    ++it;
    cout << *it << endl;  // 2
    
    // 可以后退
    --it;
    cout << *it << endl;  // 1
    
    // ❌ 不能随机访问
    // cout << it[2];     // 编译错误
    // cout << *(it + 2); // 编译错误
    
    return 0;
}
```

### set/map - 双向迭代器

```cpp
#include <iostream>
#include <set>
#include <map>
using namespace std;

int main() {
    set<int> st = {3, 1, 4, 1, 5};
    
    // set 自动排序且去重
    for (auto it = st.begin(); it != st.end(); ++it) {
        cout << *it << " ";  // 1 3 4 5
    }
    cout << endl;
    
    // map 迭代器
    map<string, int> mp = {{"Alice", 90}, {"Bob", 85}, {"Carol", 95}};
    
    for (auto it = mp.begin(); it != mp.end(); ++it) {
        cout << it->first << ": " << it->second << endl;
        // it->first 是 key
        // it->second 是 value
    }
    
    return 0;
}
```

### string - 随机访问迭代器

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello";
    
    // 正向遍历
    for (auto it = str.begin(); it != str.end(); ++it) {
        *it = toupper(*it);  // 转大写
    }
    cout << str << endl;  // "HELLO"
    
    // 反向遍历
    for (auto it = str.rbegin(); it != str.rend(); ++it) {
        cout << *it;  // "OLLEH"
    }
    cout << endl;
    
    return 0;
}
```

---

## 迭代器操作

### 基本操作

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {10, 20, 30, 40, 50};
    
    auto it = vec.begin();
    
    // 1. 解引用 *
    cout << *it << endl;           // 10
    
    // 2. 前进 ++
    ++it;
    cout << *it << endl;           // 20
    
    // 3. 后退 -- (双向迭代器)
    --it;
    cout << *it << endl;           // 10
    
    // 4. 比较
    auto it2 = vec.begin();
    if (it == it2) {
        cout << "Iterators are equal" << endl;
    }
    
    // 5. 箭头运算符 -> (访问成员)
    struct Point { int x, y; };
    vector<Point> points = {{1, 2}, {3, 4}};
    auto pit = points.begin();
    cout << "x: " << pit->x << ", y: " << pit->y << endl;
    
    return 0;
}
```

### 迭代器算术 (随机访问迭代器)

**重要**：只有**随机访问迭代器**（vector, deque, array, string）支持加减常数运算。

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {10, 20, 30, 40, 50};
    
    auto it = vec.begin();
    
    // ========== 加法运算 ==========
    
    // 1. 迭代器 + 整数：前进 n 步
    auto it2 = it + 3;
    cout << *it2 << endl;          // 40
    
    // 2. 整数 + 迭代器：同样有效
    auto it2b = 3 + it;
    cout << *it2b << endl;         // 40
    
    // 3. 复合赋值 +=
    it += 2;
    cout << *it << endl;           // 30
    
    // ========== 减法运算 ==========
    
    // 4. 迭代器 - 整数：后退 n 步
    auto it3 = it2 - 2;
    cout << *it3 << endl;          // 20
    
    // 5. 复合赋值 -=
    it -= 1;
    cout << *it << endl;           // 20
    
    // 6. 迭代器 - 迭代器：计算距离
    cout << (it2 - it) << endl;    // 距离：2
    
    // ========== 下标访问 ==========
    
    // 7. 下标运算符 []
    cout << it[0] << endl;         // 20 (当前位置)
    cout << it[2] << endl;         // 40 (向前2步)
    cout << it[-1] << endl;        // 10 (向后1步)
    
    // ========== 比较运算 ==========
    
    // 8. 比较迭代器位置
    if (it < it2) {
        cout << "it is before it2" << endl;
    }
    
    if (it2 > it) {
        cout << "it2 is after it" << endl;
    }
    
    return 0;
}
```

### 迭代器算术详细说明

#### 1. **it + n** / **n + it**

```cpp
vector<int> vec = {0, 10, 20, 30, 40};
auto it = vec.begin();

// 向前移动3步
auto it2 = it + 3;     // 指向 vec[3] = 30
auto it3 = 3 + it;     // 同样指向 vec[3] = 30

cout << *it2 << endl;  // 30
cout << *it3 << endl;  // 30
```

#### 2. **it - n**

```cpp
vector<int> vec = {0, 10, 20, 30, 40};
auto it = vec.end();   // 指向末尾后一位

// 向后移动2步
auto it2 = it - 2;     // 指向 vec[3] = 30
cout << *it2 << endl;  // 30

// 访问最后一个元素
auto last = vec.end() - 1;
cout << *last << endl; // 40
```

#### 3. **it1 - it2** (计算距离)

```cpp
vector<int> vec = {0, 10, 20, 30, 40};
auto it1 = vec.begin();
auto it2 = vec.begin() + 3;

// 计算距离（返回类型是 ptrdiff_t）
ptrdiff_t dist = it2 - it1;
cout << "Distance: " << dist << endl;  // 3

// 也可以反过来（结果是负数）
cout << "Distance: " << (it1 - it2) << endl;  // -3
```

#### 4. **it += n** / **it -= n**

```cpp
vector<int> vec = {0, 10, 20, 30, 40};
auto it = vec.begin();

// 就地修改迭代器
it += 2;
cout << *it << endl;   // 20

it -= 1;
cout << *it << endl;   // 10
```

#### 5. **it[n]** (下标访问)

```cpp
vector<int> vec = {0, 10, 20, 30, 40};
auto it = vec.begin() + 2;  // 指向 vec[2] = 20

// 下标访问（相对于当前位置）
cout << it[0] << endl;   // 20 (当前位置)
cout << it[1] << endl;   // 30 (向前1步)
cout << it[2] << endl;   // 40 (向前2步)
cout << it[-1] << endl;  // 10 (向后1步)
cout << it[-2] << endl;  // 0  (向后2步)

// 等价于
cout << *(it + 1) << endl;  // 30
cout << *(it - 1) << endl;  // 10
```

#### 6. **比较运算符** (< > <= >=)

```cpp
vector<int> vec = {0, 10, 20, 30, 40};
auto it1 = vec.begin();
auto it2 = vec.begin() + 3;

if (it1 < it2) {
    cout << "it1 is before it2" << endl;  // ✅
}

if (it2 > it1) {
    cout << "it2 is after it1" << endl;   // ✅
}

if (it1 <= it2) {
    cout << "it1 <= it2" << endl;         // ✅
}
```

### ⚠️ 重要限制

#### 只有随机访问迭代器支持算术运算

```cpp
#include <iostream>
#include <vector>
#include <list>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3, 4, 5};
    list<int> lst = {1, 2, 3, 4, 5};
    
    // ✅ vector 支持（随机访问迭代器）
    auto vit = vec.begin();
    vit = vit + 3;        // 正确
    cout << vit[1] << endl;  // 正确
    
    // ❌ list 不支持（双向迭代器）
    auto lit = lst.begin();
    // lit = lit + 3;     // 编译错误！
    // cout << lit[1];    // 编译错误！
    
    // ✅ list 只能用 ++ 和 --
    ++lit;
    ++lit;
    ++lit;
    cout << *lit << endl;  // 正确
    
    return 0;
}
```

### 不同迭代器类型支持的操作

| 操作 | 随机访问 | 双向 | 前向 | 输入/输出 |
|------|----------|------|------|-----------|
| `++it` | ✅ | ✅ | ✅ | ✅ |
| `--it` | ✅ | ✅ | ❌ | ❌ |
| `it + n` | ✅ | ❌ | ❌ | ❌ |
| `it - n` | ✅ | ❌ | ❌ | ❌ |
| `it += n` | ✅ | ❌ | ❌ | ❌ |
| `it -= n` | ✅ | ❌ | ❌ | ❌ |
| `it[n]` | ✅ | ❌ | ❌ | ❌ |
| `it1 - it2` | ✅ | ❌ | ❌ | ❌ |
| `it1 < it2` | ✅ | ❌ | ❌ | ❌ |

### 实用示例

#### 示例1：访问中间元素

```cpp
vector<int> vec = {0, 10, 20, 30, 40, 50, 60};

// 访问中间元素
auto mid = vec.begin() + vec.size() / 2;
cout << "Middle: " << *mid << endl;  // 30
```

#### 示例2：范围操作

```cpp
vector<int> vec = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

// 对中间部分排序
sort(vec.begin() + 2, vec.begin() + 7);

// 反转后半部分
reverse(vec.begin() + 5, vec.end());
```

#### 示例3：二分查找

```cpp
vector<int> vec = {1, 3, 5, 7, 9, 11, 13, 15};
int target = 9;

auto left = vec.begin();
auto right = vec.end();

while (left < right) {
    auto mid = left + (right - left) / 2;  // 计算中点
    
    if (*mid == target) {
        cout << "Found at position: " << (mid - vec.begin()) << endl;
        break;
    } else if (*mid < target) {
        left = mid + 1;
    } else {
        right = mid;
    }
}
```

#### 示例4：滑动窗口

```cpp
vector<int> vec = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int windowSize = 3;

// 遍历所有大小为3的窗口
for (auto it = vec.begin(); it + windowSize <= vec.end(); ++it) {
    cout << "Window: ";
    for (auto wit = it; wit != it + windowSize; ++wit) {
        cout << *wit << " ";
    }
    cout << endl;
}
```

### 迭代器辅助函数

```cpp
#include <iostream>
#include <vector>
#include <iterator>
using namespace std;

int main() {
    vector<int> vec = {10, 20, 30, 40, 50};
    
    auto it = vec.begin();
    
    // advance: 移动迭代器 n 步
    advance(it, 3);
    cout << *it << endl;           // 40
    
    // distance: 计算两个迭代器之间的距离
    auto it2 = vec.begin();
    cout << distance(it2, it) << endl;  // 3
    
    // next: 返回前进 n 步后的迭代器（不修改原迭代器）
    auto it3 = next(it2, 2);
    cout << *it3 << endl;          // 30
    cout << *it2 << endl;          // 10 (it2 未改变)
    
    // prev: 返回后退 n 步后的迭代器
    auto it4 = prev(vec.end(), 1);
    cout << *it4 << endl;          // 50
    
    return 0;
}
```

---

## 范围for与迭代器

### 范围for的本质

范围for循环本质上是迭代器的语法糖。

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3, 4, 5};
    
    // 范围for
    for (int x : vec) {
        cout << x << " ";
    }
    cout << endl;
    
    // 等价于使用迭代器
    for (auto it = vec.begin(); it != vec.end(); ++it) {
        cout << *it << " ";
    }
    cout << endl;
    
    return 0;
}
```

### 引用修改元素

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3, 4, 5};
    
    // 使用引用修改
    for (int& x : vec) {
        x *= 2;
    }
    
    // 等价于
    for (auto it = vec.begin(); it != vec.end(); ++it) {
        *it *= 2;
    }
    
    for (int x : vec) {
        cout << x << " ";  // 4 8 12 16 20
    }
    cout << endl;
    
    return 0;
}
```

### const 引用

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3, 4, 5};
    
    // const 引用（只读，避免拷贝）
    for (const int& x : vec) {
        cout << x << " ";
        // x *= 2;  // ❌ 错误：不能修改
    }
    cout << endl;
    
    return 0;
}
```

---

## 迭代器适配器

### 反向迭代器 (Reverse Iterator)

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3, 4, 5};
    
    // rbegin() 和 rend() 返回反向迭代器
    cout << "Reverse: ";
    for (auto it = vec.rbegin(); it != vec.rend(); ++it) {
        cout << *it << " ";  // 5 4 3 2 1
    }
    cout << endl;
    
    // 修改元素
    for (auto it = vec.rbegin(); it != vec.rend(); ++it) {
        *it *= 10;
    }
    
    for (int x : vec) {
        cout << x << " ";  // 10 20 30 40 50
    }
    cout << endl;
    
    return 0;
}
```

### 插入迭代器 (Insert Iterator)

```cpp
#include <iostream>
#include <vector>
#include <iterator>
#include <algorithm>
using namespace std;

int main() {
    vector<int> vec1 = {1, 2, 3};
    vector<int> vec2;
    
    // back_inserter: 在容器末尾插入
    copy(vec1.begin(), vec1.end(), back_inserter(vec2));
    
    for (int x : vec2) {
        cout << x << " ";  // 1 2 3
    }
    cout << endl;
    
    // front_inserter: 在容器开头插入 (需要 push_front)
    // inserter: 在指定位置插入
    
    return 0;
}
```

### 流迭代器 (Stream Iterator)

```cpp
#include <iostream>
#include <iterator>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    // istream_iterator: 从输入流读取
    cout << "Enter numbers (Ctrl+D to end):" << endl;
    istream_iterator<int> input(cin), eof;
    vector<int> vec(input, eof);
    
    // ostream_iterator: 写入输出流
    cout << "Numbers entered: ";
    ostream_iterator<int> output(cout, " ");
    copy(vec.begin(), vec.end(), output);
    cout << endl;
    
    return 0;
}
```

### 移动迭代器 (Move Iterator) - C++11

```cpp
#include <iostream>
#include <vector>
#include <iterator>
#include <string>
using namespace std;

int main() {
    vector<string> vec1 = {"Hello", "World", "C++"};
    vector<string> vec2;
    
    // 移动而非拷贝
    vec2.reserve(vec1.size());
    move(make_move_iterator(vec1.begin()),
         make_move_iterator(vec1.end()),
         back_inserter(vec2));
    
    // vec1 的元素被移动后变为空
    cout << "vec1[0]: [" << vec1[0] << "]" << endl;  // []
    cout << "vec2[0]: [" << vec2[0] << "]" << endl;  // [Hello]
    
    return 0;
}
```

---

## 算法与迭代器

### 常用算法

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> vec = {5, 2, 8, 1, 9, 3};
    
    // 1. sort: 排序
    sort(vec.begin(), vec.end());
    for (int x : vec) cout << x << " ";  // 1 2 3 5 8 9
    cout << endl;
    
    // 2. find: 查找
    auto it = find(vec.begin(), vec.end(), 5);
    if (it != vec.end()) {
        cout << "Found: " << *it << endl;
    }
    
    // 3. count: 计数
    int cnt = count(vec.begin(), vec.end(), 5);
    cout << "Count of 5: " << cnt << endl;
    
    // 4. reverse: 反转
    reverse(vec.begin(), vec.end());
    for (int x : vec) cout << x << " ";  // 9 8 5 3 2 1
    cout << endl;
    
    // 5. max_element: 最大元素
    auto maxIt = max_element(vec.begin(), vec.end());
    cout << "Max: " << *maxIt << endl;  // 9
    
    // 6. accumulate: 求和 (需要 <numeric>)
    #include <numeric>
    int sum = accumulate(vec.begin(), vec.end(), 0);
    cout << "Sum: " << sum << endl;
    
    return 0;
}
```

### 范围操作

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    
    // 对部分范围排序
    sort(vec.begin() + 2, vec.begin() + 7);
    
    // 查找范围内的元素
    auto it = find(vec.begin() + 3, vec.end(), 8);
    if (it != vec.end()) {
        cout << "Found at position: " << distance(vec.begin(), it) << endl;
    }
    
    // 填充范围
    fill(vec.begin(), vec.begin() + 3, 0);
    for (int x : vec) cout << x << " ";
    cout << endl;
    
    return 0;
}
```

### lambda 与算法

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    
    // for_each: 对每个元素执行操作
    for_each(vec.begin(), vec.end(), [](int& x) {
        x *= 2;
    });
    
    // count_if: 条件计数
    int evenCount = count_if(vec.begin(), vec.end(), [](int x) {
        return x % 2 == 0;
    });
    cout << "Even count: " << evenCount << endl;
    
    // find_if: 条件查找
    auto it = find_if(vec.begin(), vec.end(), [](int x) {
        return x > 10;
    });
    if (it != vec.end()) {
        cout << "First > 10: " << *it << endl;
    }
    
    // remove_if: 条件删除（需要配合 erase）
    vec.erase(remove_if(vec.begin(), vec.end(), [](int x) {
        return x % 4 == 0;
    }), vec.end());
    
    for (int x : vec) cout << x << " ";
    cout << endl;
    
    return 0;
}
```

---

## 自定义迭代器

### 简单的迭代器示例

```cpp
#include <iostream>
using namespace std;

template<typename T>
class SimpleArray {
private:
    T* data;
    size_t size;

public:
    SimpleArray(size_t n) : size(n) {
        data = new T[n];
    }
    
    ~SimpleArray() {
        delete[] data;
    }
    
    T& operator[](size_t i) {
        return data[i];
    }
    
    // 迭代器类
    class Iterator {
    private:
        T* ptr;
    
    public:
        Iterator(T* p) : ptr(p) {}
        
        // 解引用
        T& operator*() {
            return *ptr;
        }
        
        // 前进
        Iterator& operator++() {
            ++ptr;
            return *this;
        }
        
        // 比较
        bool operator!=(const Iterator& other) const {
            return ptr != other.ptr;
        }
    };
    
    // begin 和 end
    Iterator begin() {
        return Iterator(data);
    }
    
    Iterator end() {
        return Iterator(data + size);
    }
};

int main() {
    SimpleArray<int> arr(5);
    
    // 填充数据
    for (size_t i = 0; i < 5; i++) {
        arr[i] = i * 10;
    }
    
    // 使用自定义迭代器
    for (auto it = arr.begin(); it != arr.end(); ++it) {
        cout << *it << " ";  // 0 10 20 30 40
    }
    cout << endl;
    
    // 也可以使用范围for
    for (int x : arr) {
        cout << x << " ";
    }
    cout << endl;
    
    return 0;
}
```

### 实现双向迭代器

```cpp
#include <iostream>
using namespace std;

template<typename T>
class DoublyLinkedList {
private:
    struct Node {
        T data;
        Node* next;
        Node* prev;
        Node(const T& d) : data(d), next(nullptr), prev(nullptr) {}
    };
    
    Node* head;
    Node* tail;

public:
    DoublyLinkedList() : head(nullptr), tail(nullptr) {}
    
    void push_back(const T& value) {
        Node* newNode = new Node(value);
        if (!head) {
            head = tail = newNode;
        } else {
            tail->next = newNode;
            newNode->prev = tail;
            tail = newNode;
        }
    }
    
    // 双向迭代器
    class Iterator {
    private:
        Node* node;
    
    public:
        Iterator(Node* n) : node(n) {}
        
        T& operator*() {
            return node->data;
        }
        
        // 前进
        Iterator& operator++() {
            node = node->next;
            return *this;
        }
        
        // 后退
        Iterator& operator--() {
            node = node->prev;
            return *this;
        }
        
        bool operator!=(const Iterator& other) const {
            return node != other.node;
        }
    };
    
    Iterator begin() {
        return Iterator(head);
    }
    
    Iterator end() {
        return Iterator(nullptr);
    }
};

int main() {
    DoublyLinkedList<int> list;
    list.push_back(10);
    list.push_back(20);
    list.push_back(30);
    
    // 正向遍历
    for (auto it = list.begin(); it != list.end(); ++it) {
        cout << *it << " ";  // 10 20 30
    }
    cout << endl;
    
    return 0;
}
```

## 迭代器失效问题

| 容器类型                 | 插入操作 (Insert/Push)                             | 删除操作 (Erase/Pop)                         |
| :----------------------- | :------------------------------------------------- | :------------------------------------------- |
| **vector / string**      | **扩容**：全部失效<br>**未扩容**：插入点之后失效   | 删除点及其之后失效                           |
| **deque**                | **任意位置**：所有迭代器失效                       | **中间**：全部失效<br>**两端**：仅被删者失效 |
| **list / forward\_list** | **永不失效**                                       | 仅被删除的那个失效                           |
| **map / set / multiset** | **永不失效**                                       | 仅被删除的那个失效                           |
| **unordered\_**\*        | **扩容(Rehash)**：全部失效<br>**未扩容**：永不失效 | 仅被删除的那个失效                           |

### (A) 数组型容器 (vector, string)

底层是**连续内存**。

#### 插入 (push\_back, insert)

- 如果发生**扩容**（size \> capacity），内存重新分配，**所有**迭代器、指针、引用全部失效。
- 如果没有扩容，**插入点之后**的迭代器失效（因为数据发生了移动），插入点之前的有效。

```cpp
vector<int> v = {1, 2, 3};
v.reserve(4); // 预留空间，确保暂不扩容
auto it_before = v.begin();     // 指向 1
auto it_after = v.begin() + 2;  // 指向 3

v.insert(v.begin() + 1, 99); // 在索引1插入

cout << *it_before; // OK: 插入点之前，有效
// cout << *it_after;  // Error: 插入点之后，失效（因为3被向后挪动了）
```

#### 删除 (erase, pop_back)

- **删除点之后**的迭代器失效。
- **删除点及其之前**的迭代器有效（除被删除者外）。

```cpp
vector<int> v = {1, 2, 3, 4};
// 错误示范：
// for (auto it = v.begin(); it != v.end(); ++it) {
//     if (*it % 2 == 0) v.erase(it); // erase后it失效，++it导致崩溃
// }

// 正确示范：
for (auto it = v.begin(); it != v.end(); ) {
    if (*it % 2 == 0) {
        it = v.erase(it); // erase返回下一个有效的迭代器
    } else {
        ++it;
    }
}
```

### (B) 双端队列 (deque)

底层是**分段连续内存**（中控器 map + 缓冲区 buffer）。

#### 插入 (push)

* **两端插入** (`push_front`, `push_back`)：**所有迭代器失效**，但指向元素的引用/指针可能不失效（C++标准特例）。
* **中间插入** (`insert`)：**所有**迭代器、引用、指针失效。

```cpp
deque<int> d = {1, 2, 3};
auto it = d.begin() + 1; // 指向 2

d.push_front(0); 

// Error: deque插入操作（即使在两端）会使所有迭代器失效
// cout << *it; 
```

#### 删除 (erase)

* **两端删除** (`pop_front`, `pop_back`)：只有被擦除元素的迭代器失效。
* **中间删除** (`erase`)：**所有**迭代器、引用、指针失效。

```cpp
deque<int> d = {1, 2, 3, 4, 5};
auto it_mid = d.begin() + 2; // 指向 3

// 中间删除，全体失效
d.erase(d.begin() + 1); 

// cout << *it_mid; // Error: 迭代器已失效
```

### (C) 节点式容器 (list, map, set, unordered\_\*)

底层是**非连续内存**，通过指针链接。

#### 插入 (insert)

* **所有迭代器均不失效**。因为节点只需修改指针指向，不需要移动现有数据。

```cpp
map<int, string> m;
m[1] = "one";
auto it = m.find(1);

m[2] = "two"; // 插入新元素

cout << it->second; // OK: 插入不影响现有迭代器
```

#### 删除 (erase)

* **仅被删除元素的迭代器失效**，其余所有迭代器均有效。

```cpp
list<int> l = {1, 2, 3};

// 正确写法1：利用返回值 (C++11)
auto it = l.begin();
it = l.erase(it); // 删除1，it指向2

// 正确写法2：后置递增 (C++98 经典写法，常用于 map/set)
map<int, int> m = {{1,1}, {2,2}};
auto mit = m.begin();
m.erase(mit++); // 先把mit传给erase，mit自身再自增指向下一个
// 此时 mit 指向 {2,2}，有效
```

