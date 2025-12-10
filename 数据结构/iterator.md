# `iterator` (迭代器)

头文件：`<iterator>`

`iterator`（迭代器）是一种行为**类似指针的对象**，它充当算法（如 `find`, `sort`）和容器（如 `vector`, `list`）之间的桥梁。迭代器使得任何容器类型都可以使用相同的算法进行操作，是 C++ 泛型编程的核心。

## 1\. 迭代器分类 (Iterator Categories)

C++11 根据迭代器的能力（可进行的操作）将其分为五类，形成一个层次结构：

1.  **输入迭代器 (Input Iterator)**

      * `input_iterator_tag`
      * **能力**：只能向前移动（`++`），只能读取一次（`*`）。
      * **特点**：单遍算法。一旦递增，不能保证前一个迭代器仍然有效。
      * **例如**：从 `cin` 读取的 `istream_iterator`。

2.  **输出迭代器 (Output Iterator)**

      * `output_iterator_tag`
      * **能力**：只能向前移动（`++`），只能写入一次（`* = val`）。
      * **特点**：单遍算法。只能写，不能读。
      * **例如**：写入 `cout` 的 `ostream_iterator`。

3.  **前向迭代器 (Forward Iterator)**

      * `forward_iterator_tag`
      * **能力**：具备输入和输出迭代器的所有功能（`++`, `*` 读写, `==`, `!=`）。
      * **特点**：多遍算法。可以保存迭代器的位置，并多次遍历序列。
      * **例如**：`forward_list` 的迭代器。

4.  **双向迭代器 (Bidirectional Iterator)**

      * `bidirectional_iterator_tag`
      * **能力**：具备前向迭代器的所有功能，并额外支持**向后移动**（`--`）。
      * **例如**：`list`, `set`, `map` 的迭代器。

5.  **随机访问迭代器 (Random Access Iterator)**

      * `random_access_iterator_tag`
      * **能力**：具备双向迭代器的所有功能，并额外支持**常数时间**的算术运算（`+`, `-`, `+=`, `-=`）和全比较（`<`, `>` 等）。
      * **例如**：`vector`, `deque`, `string`, `array` 的迭代器，以及普通指针。

## 2\. STL 容器与迭代器分类

| 容器 (Container) | 迭代器分类 (Iterator Category) |
| :--- | :--- |
| `vector` | 随机访问 (Random Access) |
| `deque` | 随机访问 (Random Access) |
| `list` | 双向 (Bidirectional) |
| `string` | 随机访问 (Random Access) |
| `array` (C++11) | 随机访问 (Random Access) |
| `forward_list` (C++11) | 前向 (Forward) |
| `set` / `multiset` | 双向 (Bidirectional) |
| `map` / `multimap` | 双向 (Bidirectional) |
| `unordered_set` (C++11) | 至少是前向 (At least Forward)¹ |
| `unordered_map` (C++11) | 至少是前向 (At least Forward)¹ |
| C 风格数组 (e.g., `int arr[]`) | 随机访问 (Random Access) |
| `istream_iterator` | 输入 (Input) |
| `ostream_iterator` | 输出 (Output) |

(¹ C++11 标准仅保证 `unordered_` 容器的迭代器至少是前向的。在 C++17 中，它们被要求是双向的。)

## 3\. 迭代器支持的操作（按分类）

### (A) 输入迭代器 (Input Iterator)

*用于只读的、单遍的向前访问。*

#### 原型与解释

```cpp
// (1) 比较 (Equality Comparable)
bool operator==(const InputIterator& other) const;
bool operator!=(const InputIterator& other) const;

// (2) 解引用 (Dereferenceable)
// 返回对元素的引用（或一个可转换为 value_type 的代理对象）
// 注意：只保证在 ++ 之前可以解引用一次
reference operator*() const;
pointer operator->() const;

// (3) 递增 (Incrementable)
// 前进到下一个元素
iterator& operator++();    // 前缀
iterator operator++(int); // 后缀
```

#### 使用示例

```cpp
// 假设从标准输入读取整数，直到遇到非整数
// istream_iterator 是一种输入迭代器
istream_iterator<int> in_it(cin);
istream_iterator<int> end_it; // 默认构造的 istream_iterator 代表 "end"

int sum = 0;
while (in_it != end_it) {
    sum += *in_it; // (2) 解引用读取
    ++in_it;       // (3) 递增
}
```

### (B) 输出迭代器 (Output Iterator)

*用于只写的、单遍的向前访问。*

#### 原型与解释

```cpp
// (1) 解引用 (Dereferenceable for assignment)
// 返回一个可被赋值的对象（通常是迭代器自身）
// 对 *it 赋值，即代表“输出”该值
iterator& operator*();

// (2) 递增 (Incrementable)
// 前进到下一个输出位置
iterator& operator++();    // 前缀
iterator operator++(int); // 后缀
```

#### 使用示例

```cpp
// 假设将 vector 的内容输出到标准输出
// ostream_iterator 是一种输出迭代器
vector<int> v = {10, 20, 30};
ostream_iterator<int> out_it(cout, " "); // 第二个参数是分隔符

for (int x : v) {
    *out_it = x; // (1) 赋值即输出
    ++out_it;    // (2) 递增 (对于 ostream_iterator，这步通常是空操作)
}
// 输出: 10 20 30 
```

### (C) 前向迭代器 (Forward Iterator)

*支持所有输入迭代器的操作，且支持多遍扫描。*

#### 原型与解释

```cpp
// (1) 默认构造 (Default Constructible)
// 允许创建迭代器副本，并保存其状态
ForwardIterator();

// (2) 具备所有输入迭代器的操作
// reference operator*() const;
// bool operator!=(const ForwardIterator& other) const;
// iterator& operator++();
// ...等等
```

#### 使用示例

```cpp
forward_list<int> fl = {1, 2, 3};
auto it1 = fl.begin();
auto it2 = it1; // (1) 可以被复制和保存

// (2) 支持多遍扫描
// 第一次遍历
while (it1 != fl.end()) {
    cout << *it1 << " ";
    ++it1;
}
// it2 仍然有效，可以进行第二次遍历
while (it2 != fl.end()) {
    // ...
    ++it2;
}
```

### (D) 双向迭代器 (Bidirectional Iterator)

*支持所有前向迭代器的操作，并增加了向后移动的能力。*

#### 原型与解释

```cpp
// (1) 具备所有前向迭代器的操作
// ...

// (2) 递减 (Decrementable)
// 后退到前一个元素
iterator& operator--();    // 前缀
iterator operator--(int); // 后缀
```

#### 使用示例

```cpp
list<int> l = {10, 20, 30};
auto it = l.end(); // it 指向 30 之后的 "past-the-end"

// (2) 使用递减
--it; // it 指向 30
cout << *it << " "; // 输出 30
--it; // it 指向 20
cout << *it << " "; // 输出 20
```

### (E) 随机访问迭代器 (Random Access Iterator)

*支持所有双向迭代器的操作，并增加了常数时间的算术运算。*

#### 原型与解释

```cpp
// (1) 具备所有双向迭代器的操作
// ...

// (2) 算术运算 (Arithmetic)
// 前进 n 步
iterator operator+(difference_type n) const;
// 后退 n 步
iterator operator-(difference_type n) const;
// 复合赋值
iterator& operator+=(difference_type n);
iterator& operator-=(difference_type n);
// 计算两个迭代器之间的距离
difference_type operator-(const iterator& other) const;

// (3) 偏移访问 (Offset dereference)
// 访问距离当前位置 n 处的元素
reference operator[](difference_type n) const;

// (4) 关系比较 (Relational operators)
bool operator<(const iterator& other) const;
bool operator>(const iterator& other) const;
bool operator<=(const iterator& other) const;
bool operator>=(const iterator& other) const;
```

#### 使用示例

```cpp
vector<int> v = {10, 20, 30, 40, 50};
auto it = v.begin();

// (2) 算术运算
auto it2 = it + 3; // it2 指向 40
it2 -= 1; // it2 指向 30
int dist = v.end() - v.begin(); // dist 为 5

// (3) 偏移访问
cout << it[2]; // 输出 30

// (4) 关系比较
if (it < it2) { // true
    cout << "it is before it2";
}
```

## 4\. 迭代器辅助函数 (Iterator Utilities)

C++11 在 `<iterator>` 头文件中提供了一组非常有用的非成员函数，用于操作迭代器。

### (A) 迭代器移动

*用于移动迭代器或计算距离。*

#### 原型与解释

```cpp
// (1) advance()
// 将迭代器 it 前进 n 步。
// 它会自动选择最高效的方式（O(1) for Random Access, O(N) for Bidirectional/Forward）。
// 对于双向迭代器，n 可以为负数。
void advance(InputIterator& it, difference_type n);

// (2) distance()
// 计算两个迭代器 first 和 last 之间的距离。
// 自动选择 O(1) 或 O(N) 的方式。
difference_type distance(InputIterator first, InputIterator last);

// (3) next() (C++11)
// 返回一个迭代器，它指向 it 之后的第 n 个元素，但不修改 it 本身。
template <class ForwardIterator>
ForwardIterator next(ForwardIterator it, difference_type n = 1);

// (4) prev() (C++11)
// 返回一个迭代器，它指向 it 之前的第 n 个元素，但不修改 it 本身。
template <class BidirectionalIterator>
BidirectionalIterator prev(BidirectionalIterator it, difference_type n = 1);
```

#### 使用示例

```cpp
list<int> l = {10, 20, 30, 40};
auto it = l.begin(); // it 指向 10

// (1) advance()
advance(it, 2); // it 现在指向 30

// (2) distance()
auto it2 = l.end();
int d = distance(it, it2); // d 为 2 (从 30 到 end 有 2 步)

// (3) next() (C++11)
auto it_next = next(it); // it_next 指向 40 (it 仍指向 30)

// (4) prev() (C++11)
auto it_prev = prev(it); // it_prev 指向 20 (it 仍指向 30)
```

### (B) 全局 begin/end (C++11)

*用于统一获取容器或 C 风格数组的迭代器。*

#### 原型与解释

```cpp
// (1) begin()
// 获取容器或数组的 begin 迭代器。
template <class C> auto begin(C& c) -> decltype(c.begin());
template <class C> auto begin(const C& c) -> decltype(c.begin());
template <class T, size_t N> T* begin(T (&array)[N]);

// (2) end()
// 获取容器或数组的 end 迭代器。
template <class C> auto end(C& c) -> decltype(c.end());
template <class C> auto end(const C& c) -> decltype(c.end());
template <class T, size_t N> T* end(T (&array)[N]);
```

#### 使用示例

```cpp
vector<int> v = {10, 20, 30};
int arr[] = {1, 2, 3};

// (1) & (2) 对容器使用
// sort(begin(v), end(v));

// (1) & (2) 对 C 风格数组使用
// sort(begin(arr), end(arr));
```

## 5\. 迭代器失效问题

| 容器类型 | 插入操作 (Insert/Push) | 删除操作 (Erase/Pop) |
| :--- | :--- | :--- |
| **vector / string** | **扩容**：全部失效<br>**未扩容**：插入点之后失效 | 删除点及其之后失效 |
| **deque** | **任意位置**：所有迭代器失效 | **中间**：全部失效<br>**两端**：仅被删者失效 |
| **list / forward\_list**| **永不失效** | 仅被删除的那个失效 |
| **map / set / multiset** | **永不失效** | 仅被删除的那个失效 |
| **unordered\_**\* | **扩容(Rehash)**：全部失效<br>**未扩容**：永不失效 | 仅被删除的那个失效 |

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


