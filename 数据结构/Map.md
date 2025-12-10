# `map` 笔记



`std::map` 是 C++ 标准模板库（STL）中的一种关联容器（Associative Container）。它存储**键值对（Key-Value Pair）**，其中每个键（Key）都是**唯一**的，并且容器中的元素是**按键的升序**自动排序的。

------



## 🧐 1. 基本特性与内部结构





### 关键特性



| **特性**       | **描述**                                                     |
| -------------- | ------------------------------------------------------------ |
| **键唯一性**   | 每个键（Key）只能出现一次。如果你插入一个已存在的键，新的值会覆盖旧的值。 |
| **自动排序**   | 元素总是根据键（Key）进行排序，默认使用 `std::less`（从小到大）。 |
| **存储结构**   | 通常由**红黑树 (Red-Black Tree)** 实现，是一种自平衡二叉查找树。 |
| **查找复杂度** | 由于是树结构，查找、插入和删除操作的时间复杂度都是对数时间：**O(log \*N\*)**，效率高且稳定。 |
| **迭代器**     | 支持**双向迭代器**（Bidirectional Iterator）。               |



### 模板定义



C++

```
template<
    class Key,
    class T, // Value Type
    class Compare = std::less<Key>,
    class Allocator = std::allocator<std::pair<const Key, T>>
> class map;
```

- `Key`: 键的类型。
- `T`: 值的类型。
- `Compare`: 用于排序的比较函数对象（默认是升序）。

------



## 🏗️ 2. 构造与初始化





### 默认构造



创建一个空的 `map`。

C++

```
std::map<int, std::string> m1; // Key: int, Value: string
```



### 列表初始化 (C++11)



在创建时直接指定键值对。

C++

```
std::map<std::string, int> ages = {
    {"Alice", 30},
    {"Bob", 25},
    {"Charlie", 35}
}; 
// 元素将按键的字母顺序排序: Alice, Bob, Charlie
```



### 拷贝与移动



C++

```
std::map<int, int> m3 = m2;        // 拷贝构造
std::map<int, int> m4 = std::move(m2); // 移动构造
```

------



## ➕ 3. 元素操作 (插入)



有多种方法可以将元素插入到 `map` 中。



### 方法一：使用 **下标操作符 `[]`** (最常用)



如果键不存在，会创建新元素并用**值的默认构造函数**初始化，然后返回其引用；如果键已存在，则**覆盖**其值。

C++

```
std::map<int, std::string> students;

students[101] = "Zhang San"; // 插入新元素
students[102] = "Li Si";     // 插入新元素
students[101] = "Wang Wu";   // 覆盖 Key 为 101 的值
```

> ⚠️ **注意：** `map[key]` 的操作会涉及元素的插入，所以即使你只是想查找元素，如果键不存在，它也会默默地插入一个默认初始化的元素。



### 方法二：使用 `insert()`





#### 1. 插入 `std::pair` 或 `std::initializer_list`



`insert()` 返回一个 `std::pair<iterator, bool>`，`bool` 部分指示是否成功插入（即键是否已存在）。

C++

```
// 使用 std::make_pair
students.insert(std::make_pair(103, "Zhao Liu"));

// 使用 std::pair 构造
students.insert({104, "Qian Qi"}); 

// 使用 std::initializer_list (C++11)
students.insert({{105, "Sun Ba"}, {106, "Zheng Jiu"}});
```



#### 2. 使用 `emplace()` (推荐用于性能)



直接在容器内部构造元素，避免了不必要的拷贝或移动，性能通常更好。

C++

```
// 避免创建 pair 对象，直接在 map 内部构造
students.emplace(107, "Wu Shi"); 
```

------



## 🔎 4. 元素访问与查找





### 访问元素



| **方法**        | **描述**                                                     | **行为**                |
| --------------- | ------------------------------------------------------------ | ----------------------- |
| **`m[key]`**    | **访问或插入**。如果键存在，返回值的引用；如果键**不存在**，则**插入**一个默认值元素。 | **可能改变 `map` 大小** |
| **`m.at(key)`** | **访问**。如果键存在，返回值的引用；如果键**不存在**，则抛出 `std::out_of_range` 异常。 | **安全访问**            |



### 查找元素



| **方法**                 | **描述**                                          | **返回值**                                                   | **复杂度** |
| ------------------------ | ------------------------------------------------- | ------------------------------------------------------------ | ---------- |
| **`m.count(key)`**       | 检查键是否存在。                                  | `1`（存在）或 `0`（不存在），因为键唯一。                    | O(log *N*) |
| **`m.find(key)`**        | 查找键。                                          | **迭代器**。如果找到，返回指向该元素的迭代器；如果**未找到**，返回 `m.end()`。 | O(log *N*) |
| **`m.lower_bound(key)`** | 返回指向**键不小于** `key` 的第一个元素的迭代器。 | 迭代器                                                       | O(log *N*) |
| **`m.upper_bound(key)`** | 返回指向**键大于** `key` 的第一个元素的迭代器。   | 迭代器                                                       | O(log *N*) |



#### `find()` 示例



C++

```
if (students.find(102) != students.end()) {
    std::cout << "找到学生 102." << std::endl;
} else {
    std::cout << "未找到学生 102." << std::endl;
}
```

------



## ➖ 5. 删除操作



| **方法**                   | **描述**                            | **返回值**                             | **复杂度**  |
| -------------------------- | ----------------------------------- | -------------------------------------- | ----------- |
| **`m.erase(key)`**         | 删除键为 `key` 的元素。             | 成功删除的元素数量（`0` 或 `1`）。     | O(log *N*)  |
| **`m.erase(pos)`**         | 删除迭代器 `pos` 指向的元素。       | 指向被删除元素**后一个**元素的迭代器。 | O(1) (均摊) |
| **`m.erase(first, last)`** | 删除范围 `[first, last)` 内的元素。 | 指向范围后一个元素的迭代器。           | O(*N*)      |
| **`m.clear()`**            | 清空所有元素。                      | 无                                     | O(*N*)      |

C++

```
// 1. 按键删除
students.erase(101); 

// 2. 按迭代器删除
auto it = students.find(102);
if (it != students.end()) {
    students.erase(it);
}
```

------



## 🔁 6. 遍历 `map`



`map` 的遍历始终是按照键的**排序顺序**进行的。



### 方法一：范围-based for 循环 (Range-based for loop) - 推荐



使用 C++17 的**结构化绑定**最为简洁高效。

C++

```
// Key: int, Value: string
for (const auto& [id, name] : students) {
    std::cout << "ID: " << id << ", Name: " << name << std::endl;
}
```



### 方法二：使用迭代器



C++

```
for (auto it = students.begin(); it != students.end(); ++it) {
    // it->first 是 Key
    // it->second 是 Value
    std::cout << "Key: " << it->first << ", Value: " << it->second << std::endl;
}
```

------



## 🆚 7. 与其他关联容器的对比





### `std::map` vs `std::unordered_map`



| **特性**           | **std::map**                       | **std::unordered_map**                                       |
| ------------------ | ---------------------------------- | ------------------------------------------------------------ |
| **底层实现**       | 红黑树（Red-Black Tree）           | 哈希表（Hash Table）                                         |
| **元素顺序**       | **按键有序**（自动排序）           | **无序**（取决于哈希函数）                                   |
| **查找/插入/删除** | O(log *N*)                         | 平均 O(1)，最坏 O(*N*)                                       |
| **键类型要求**     | 需要支持**比较操作** (`operator<`) | 需要支持**哈希操作** (`std::hash`) 和**相等比较** (`operator==`) |
| **内存开销**       | 通常略高（需要存储树节点信息）     | 通常较低（哈希表）                                           |

> **选择建议：**
>
> - 如果你需要元素保持**有序**，或者需要使用**范围查找**（如 `lower_bound`），请使用 `std::map`。
> - 如果你追求**最快**的平均访问速度，并且**不关心**元素的顺序，请使用 `std::unordered_map`。



### `std::map` vs `std::multimap`



- `std::map`：**键是唯一**的。
- `std::multimap`：**允许键重复**。适用于一个键对应多个值的情况。

------



## 8. 排序规则自定义



默认情况下，`std::map` 使用 `std::less<Key>` 进行升序排序。你可以通过提供自定义的比较类型来实现降序或其他排序规则。



### 示例：降序排序



C++

```
#include <functional> // for std::greater

// 使用 std::greater<int> 使键按降序排序 (从大到小)
std::map<int, std::string, std::greater<int>> desc_map = {
    {10, "Ten"},
    {20, "Twenty"},
    {30, "Thirty"}
};

// 遍历输出的顺序是: 30, 20, 10
for (const auto& [key, val] : desc_map) {
    std::cout << key << ": " << val << std::endl;
}
```