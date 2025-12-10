# deque

头文件：`<deque>`

`deque` (double-ended queue) 是一种序列容器，支持在序列的头部和尾部进行高效的插入和删除操作。与 `vector` 不同，`deque` 的内存不是连续的，因此它不支持 `capacity()` 和 `reserve()` 操作。但它提供了与 `vector` 类似的随机访问迭代器和通过索引访问元素的能力。

## 函数列表

| 分类 | 函数名 | 主要功能 |
| :--- | :--- | :--- |
| **构造函数与赋值** | `deque()` | 创建 deque（默认、填充、范围、拷贝/移动、初始化列表构造）。 |
| | `operator=` | 将另一个 deque 的内容赋给当前 deque。 |
| **迭代器** | `begin`, `cbegin` | 返回指向第一个元素的迭代器。 |
| | `end`, `cend` | 返回指向尾端 "之后" 的迭代器。 |
| | `rbegin`, `crbegin` | 返回指向最后一个元素的反向迭代器。 |
| | `rend`, `crend` | 返回指向第一个元素 "之前" 的反向迭代器。 |
| **容量** | `empty` | 检查 deque 是否为空。 |
| | `size` | 返回 deque 中元素的数量。 |
| | `max_size` | 返回 deque 可能容纳的最大元素数量。 |
| | `shrink_to_fit` | 请求释放未使用的内存。 |
| **元素访问** | `operator[]` | 返回对位置 `n` 处元素的引用（不进行边界检查）。 |
| | `at` | 返回对位置 `n` 处元素的引用（进行边界检查）。 |
| | `front` | 返回对 deque 中第一个元素的引用。 |
| | `back` | 返回对 deque 中最后一个元素的引用。 |
| **修改器** | `push_front`, `emplace_front` | 在 deque 的头部添加一个新元素。 |
| | `pop_front` | 移除 deque 的第一个元素。 |
| | `push_back`, `emplace_back` | 在 deque 的尾部添加一个新元素。 |
| | `pop_back` | 移除 deque 的最后一个元素。 |
| | `insert`, `emplace` | 在指定位置插入元素。 |
| | `erase` | 移除指定位置或范围的元素。 |
| | `resize` | 改变 deque 的大小。 |
| | `clear` | 移除所有元素，使 `size` 变为 0。 |
| | `swap` | 交换两个 deque 的内容。 |
| **非成员函数** | `operator==, !=, <, ...` | 对两个 deque 对象进行字典序比较。 |
| | `swap` | 交换两个 deque 的内容（非成员版本）。 |

## 1. 构造函数与赋值 (Constructors & Assignment)

这部分负责 `deque` 对象的创建和赋值，其接口与 `vector` 非常相似。

### 原型与解释

```cpp
// (1) 默认构造函数
// 创建一个空的 deque。
explicit deque( const Allocator& alloc = Allocator() );

// (2) 填充构造函数
// 创建一个包含 n 个 value 副本的 deque。
explicit deque( size_type n, const value_type& val = value_type(),
                const Allocator& alloc = Allocator() );

// (3) 范围构造函数
// 创建一个包含迭代器范围 [first, last) 内元素的 deque。
template< class InputIterator >
deque( InputIterator first, InputIterator last,
       const Allocator& alloc = Allocator() );

// (4) 拷贝/移动构造函数 (C++11)
deque( const deque& other );
deque( deque&& other ); // C++11

// (5) 初始化列表构造函数 (C++11)
deque( initializer_list<value_type> il,
       const Allocator& alloc = Allocator() );

// (6) 赋值操作
// 将另一个 deque 的内容赋给当前 deque。
deque& operator=( const deque& other );
deque& operator=( deque&& other ); // C++11 移动赋值
deque& operator=( initializer_list<value_type> il ); // C++11
```

### 使用示例

```cpp
// (1) 默认构造
deque<int> d1;

// (2) 填充构造
deque<int> d2(4, 100); // 内容: {100, 100, 100, 100}

// (3) 范围构造
int myints[] = {1, 2, 3};
deque<int> d3(myints, myints + 3); // 内容: {1, 2, 3}

// (4) 拷贝构造
deque<int> d4(d3);

// (5) 初始化列表构造 (C++11)
deque<int> d5 = {10, 20, 30};

// (6) 赋值
d1 = d5; // d1 的内容变为 {10, 20, 30}
```



## 2. 迭代器 (Iterators)

`deque` 提供随机访问迭代器，用法与 `vector` 的迭代器完全相同。

### 原型与解释

```cpp
// (1) begin() / cbegin() (C++11)
// 返回指向第一个元素的迭代器。
iterator begin();
const_iterator cbegin() const;

// (2) end() / cend() (C++11)
// 返回指向尾端 "之后" 的迭代器。
iterator end();
const_iterator cend() const;

// (3) rbegin() / crbegin() (C++11)
// 返回指向最后一个元素的反向迭代器。
reverse_iterator rbegin();
const_reverse_iterator crbegin() const;

// (4) rend() / crend() (C++11)
// 返回指向第一个元素 "之前" 的反向迭代器。
reverse_iterator rend();
const_reverse_iterator crend() const;
```

### 使用示例

```cpp
deque<int> mydeque = {1, 2, 3, 4, 5};

// 正向遍历
for (auto it = mydeque.cbegin(); it != mydeque.cend(); ++it) {
    cout << *it << " "; // 输出: 1 2 3 4 5
}

// 反向遍历
for (auto it = mydeque.crbegin(); it != mydeque.crend(); ++it) {
    cout << *it << " "; // 输出: 5 4 3 2 1
}
```



## 3. 容量 (Capacity)

用于查询 `deque` 的大小状态。注意，`deque` 没有 `capacity()` 或 `reserve()`。

### 原型与解释

```cpp
// (1) empty()
// 检查 deque 是否为空。
bool empty() const;

// (2) size()
// 返回 deque 中元素的数量。
size_type size() const;

// (3) max_size()
// 返回 deque 可能容纳的最大元素数量。
size_type max_size() const;

// (4) shrink_to_fit() (C++11)
// 请求释放未使用的内存。这是一个非强制性请求。
void shrink_to_fit();
```

### 使用示例

```cpp
deque<int> d(100);
cout << "Size: " << d.size() << endl; // 输出: Size: 100

d.clear();
cout << "Is empty? " << boolalpha << d.empty() << endl; // 输出: Is empty? true

d.shrink_to_fit(); // 请求释放为 100 个元素分配的内存
```

## 4. 元素访问 (Element Access)

`deque` 提供了与 `vector` 几乎相同的元素访问接口，支持通过索引进行随机访问。

### 原型与解释

```cpp
// (1) operator[]
// 返回对位置 n 处元素的引用。
// 不进行边界检查，如果 n 超出范围，行为是未定义的。
reference operator[](size_type n);
const_reference operator[](size_type n) const;

// (2) at()
// 返回对位置 n 处元素的引用。
// 会进行边界检查，如果 n 不在有效范围内，会抛出 out_of_range 异常。
reference at(size_type n);
const_reference at(size_type n) const;

// (3) front()
// 返回对 deque 中第一个元素的引用。
// 在一个空 deque 上调用此函数是未定义的。
reference front();
const_reference front() const;

// (4) back()
// 返回对 deque 中最后一个元素的引用。
// 在一个空 deque 上调用此函数是未定义的。
reference back();
const_reference back() const;
```

### 使用示例

```cpp
deque<int> d = {10, 20, 30, 40, 50};

// (1) operator[]
int first_element = d[0]; // first_element is 10
d[0] = 15; // d is now {15, 20, 30, 40, 50}

// (2) at()
int third_element = d.at(2); // third_element is 30
try {
    d.at(10) = 100; // 索引 10 超出范围
} catch (const out_of_range& oor) {
    // 捕获异常，程序不会崩溃
    cerr << "Out of Range error: " << oor.what() << '\n';
}

// (3) front()
int& front_ref = d.front(); // front_ref 是对第一个元素 (15) 的引用
front_ref++; // d is now {16, 20, 30, 40, 50}

// (4) back()
int& back_ref = d.back(); // back_ref 是对最后一个元素 (50) 的引用
back_ref -= 5; // d is now {16, 20, 30, 40, 45}
```

## 5. 修改器 (Modifiers)

这组函数用于改变 `deque` 的内容。`deque` 的主要优势在于它提供了在头部和尾部进行 `O(1)` 时间复杂度插入和删除的函数。

### 原型与解释

```cpp
// (1) push_front() / emplace_front() (C++11)
// 在 deque 的头部添加一个新元素。
// emplace_front() 在头部就地构造元素，效率更高。
void push_front( const value_type& val );
void push_front( value_type&& val ); // C++11
template< class... Args > void emplace_front( Args&&... args ); // C++11

// (2) pop_front()
// 移除 deque 的第一个元素。
void pop_front();

// (3) push_back() / emplace_back() (C++11)
// 在 deque 的尾部添加一个新元素。
// emplace_back() 在尾部就地构造元素，效率更高。
void push_back( const value_type& val );
void push_back( value_type&& val ); // C++11
template< class... Args > void emplace_back( Args&&... args ); // C++11

// (4) pop_back()
// 移除 deque 的最后一个元素。
void pop_back();

// (5) insert() / emplace() (C++11)
// 在指定位置插入元素，用法与 vector 相同。
// 注意：在 deque 中间插入元素比在两端慢。
iterator insert( const_iterator position, const value_type& val );
// ... 其他 insert 重载版本
template< class... Args > iterator emplace( const_iterator position, Args&&... args );

// (6) erase()
// 移除指定位置或范围的元素，用法与 vector 相同。
iterator erase( const_iterator position );
iterator erase( const_iterator first, const_iterator last );

// (7) resize()
// 改变 deque 的大小，用法与 vector 相同。
void resize( size_type n );
void resize( size_type n, const value_type& val );

// (8) clear()
// 移除所有元素，使 size 变为 0。
void clear();

// (9) swap()
// 交换两个 deque 的内容，这是一个 O(1) 的高效操作。
void swap( deque& other );
```

### 使用示例

```cpp
deque<int> d = {10, 20, 30};

// (1) push_front()
d.push_front(5); // d is now {5, 10, 20, 30}

// (2) pop_front()
d.pop_front(); // d is now {10, 20, 30}

// (3) push_back()
d.push_back(40); // d is now {10, 20, 30, 40}

// (4) pop_back()
d.pop_back(); // d is now {10, 20, 30}

// (5) insert()
d.insert(d.begin() + 1, 15); // d is now {10, 15, 20, 30}

// (6) erase()
d.erase(d.begin() + 2); // d is now {10, 15, 30}

// (7) resize()
d.resize(5, 100); // d is now {10, 15, 30, 100, 100}

// (8) clear()
d.clear(); // d is now empty

// (9) swap()
deque<int> d2 = {1, 2, 3};
d.swap(d2); // d is now {1, 2, 3}, d2 is empty
```



## 6. 非成员函数 (Non-member functions)

这些是与 `deque` 配合使用的独立函数。

### 原型与解释

```cpp
// (1) 关系运算符
// 对两个 deque 对象进行字典序比较。
// (==, !=, <, <=, >, >=)
template <class T, class Alloc>
bool operator==(const deque<T,Alloc>& lhs, const deque<T,Alloc>& rhs);
// ... 其他运算符类似

// (2) swap() (非成员版本)
// 与成员函数 swap 功能相同，交换两个 deque 的内容。
template <class T, class Alloc>
void swap (deque<T,Alloc>& x, deque<T,Alloc>& y);
```

### 使用示例

```cpp
deque<int> d_a = {1, 2};
deque<int> d_b = {1, 2};
deque<int> d_c = {3, 4};

// (1) 关系运算符
if (d_a == d_b) {
    cout << "d_a and d_b are equal." << endl;
}
if (d_a < d_c) {
    cout << "d_a is less than d_c." << endl;
}

// (2) swap
swap(d_a, d_c); // d_a is now {3, 4}, d_c is {1, 2}
```
