# C++ string 详细笔记

## 目录
1. [基础概念](#基础概念)
2. [创建和初始化](#创建和初始化)
3. [输入输出](#输入输出)
4. [访问字符](#访问字符)
5. [字符串操作](#字符串操作)
6. [查找和搜索](#查找和搜索)
7. [修改操作](#修改操作)
8. [比较操作](#比较操作)
9. [转换操作](#转换操作)
10. [迭代器](#迭代器)

---

## 基础概念

### 什么是 string？

`string` 是 C++ 标准库提供的**字符串类**，定义在 `<string>` 头文件中。

```cpp
#include <string>
using namespace std;
```

### string vs C 风格字符串

| 特性 | C++ string | C 字符串 (char[]) |
|------|------------|-------------------|
| 类型 | 类对象 | 字符数组 |
| 动态大小 | ✅ 自动管理 | ❌ 固定大小 |
| 内存管理 | ✅ 自动 | ❌ 手动 |
| 字符串操作 | ✅ 丰富的成员函数 | ❌ 需要 C 库函数 |
| 安全性 | ✅ 高（自动边界检查）| ❌ 低（容易越界）|
| 空字符 '\0' | 自动管理 | 需要手动添加 |

```cpp
// C++ string
string str = "Hello";
str += " World";  // 简单拼接

// C 字符串
char arr[20] = "Hello";
strcat(arr, " World");  // 需要确保空间足够
```

---

## 创建和初始化

### 基本初始化方式

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    // 方式1：默认构造函数（空字符串）
    string s1;
    cout << "s1: [" << s1 << "]" << endl;  // []
    
    // 方式2：字符串字面量
    string s2 = "Hello";
    string s3("World");
    
    // 方式3：拷贝构造
    string s4 = s2;
    string s5(s2);
    
    // 方式4：重复字符
    string s6(5, 'a');  // "aaaaa"
    
    // 方式5：从子串构造
    string s7("Hello World", 5);  // "Hello" (前5个字符)
    
    // 方式6：从另一个 string 的子串
    string s8(s2, 1, 3);  // "ell" (从位置1开始，长度3)
    
    // 方式7：从迭代器范围
    string s9(s2.begin(), s2.begin() + 3);  // "Hel"
    
    // 方式8：C++11 初始化列表
    string s10 = {'H', 'i', '!'};  // "Hi!"
    
    return 0;
}
```

### 特殊初始化

```cpp
#include <string>
using namespace std;

int main() {
    // 空字符串
    string empty1;
    string empty2 = "";
    string empty3("");
    
    // 包含空格的字符串
    string spaces = "   ";
    
    // 多行字符串（C++11）
    string multiline = R"(
        Line 1
        Line 2
        Line 3
    )";
    
    // Unicode 字符串（C++11）
    string utf8 = u8"Hello 世界";
    
    return 0;
}
```

---

## 输入输出

### 基本输入输出

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string name;
    
    // 输出
    cout << "Enter name: ";
    
    // 方式1：cin >> (遇到空格停止)
    cin >> name;
    cout << "Hello, " << name << endl;
    
    // 方式2：getline (读取整行)
    getline(cin, name);
    cout << "Hello, " << name << endl;
    
    return 0;
}
```

### cin >> vs getline

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string s1, s2;
    
    // cin >> 只读取到第一个空格
    cout << "Test cin >>: ";
    cin >> s1;
    cout << "Got: [" << s1 << "]" << endl;
    
    // 清除缓冲区
    cin.ignore(1000, '\n');
    
    // getline 读取整行
    cout << "Test getline: ";
    getline(cin, s2);
    cout << "Got: [" << s2 << "]" << endl;
    
    return 0;
}
```

**输入示例：**
```
Test cin >>: Hello World
Got: [Hello]
Test getline: Hello World
Got: [Hello World]
```

### 格式化输出

```cpp
#include <iostream>
#include <iomanip>
#include <string>
using namespace std;

int main() {
    string str = "Hello";
    
    // 设置宽度
    cout << setw(10) << str << endl;        // "     Hello"
    cout << left << setw(10) << str << endl; // "Hello     "
    
    // 填充字符
    cout << setfill('*') << setw(10) << str << endl; // "*****Hello"
    
    return 0;
}
```

### 文件输入输出

```cpp
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

int main() {
    // 写入文件
    ofstream outFile("output.txt");
    string data = "Hello World";
    outFile << data << endl;
    outFile.close();
    
    // 读取文件
    ifstream inFile("input.txt");
    string line;
    
    // 逐行读取
    while (getline(inFile, line)) {
        cout << line << endl;
    }
    
    inFile.close();
    return 0;
}
```

---

## 访问字符

### 使用下标运算符 []

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello";
    
    // 读取字符
    char c = str[0];  // 'H'
    cout << c << endl;
    
    // 修改字符
    str[0] = 'h';     // "hello"
    cout << str << endl;
    
    // ⚠️ 注意：[] 不检查边界
    // char bad = str[100];  // 未定义行为！
    
    return 0;
}
```

### 使用 at() 函数（推荐）

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello";
    
    try {
        // at() 会检查边界
        char c = str.at(0);  // 'H'
        cout << c << endl;
        
        str.at(0) = 'h';     // "hello"
        cout << str << endl;
        
        // 越界会抛出异常
        char bad = str.at(100);
    } catch (const out_of_range& e) {
        cout << "Error: " << e.what() << endl;
    }
    
    return 0;
}
```

### front() 和 back()

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello";
    
    // 第一个字符
    char first = str.front();  // 'H'
    str.front() = 'h';         // "hello"
    
    // 最后一个字符
    char last = str.back();    // 'o'
    str.back() = '!';          // "hell!"
    
    cout << str << endl;
    
    return 0;
}
```

### 遍历字符串

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello";
    
    // 方式1：下标访问
    for (size_t i = 0; i < str.size(); i++) {
        cout << str[i] << " ";
    }
    cout << endl;
    
    // 方式2：范围 for 循环（推荐）
    for (char c : str) {
        cout << c << " ";
    }
    cout << endl;
    
    // 方式3：引用修改
    for (char& c : str) {
        c = toupper(c);
    }
    cout << str << endl;  // "HELLO"
    
    // 方式4：迭代器
    for (auto it = str.begin(); it != str.end(); ++it) {
        cout << *it << " ";
    }
    cout << endl;
    
    return 0;
}
```

---

## 字符串操作

### 获取长度

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello";
    
    // size() 和 length() 完全相同
    cout << "size(): " << str.size() << endl;       // 5
    cout << "length(): " << str.length() << endl;   // 5
    
    // 容量（分配的空间）
    cout << "capacity(): " << str.capacity() << endl;
    
    // 最大长度
    cout << "max_size(): " << str.max_size() << endl;
    
    // 检查是否为空
    if (str.empty()) {
        cout << "String is empty" << endl;
    } else {
        cout << "String is not empty" << endl;
    }
    
    return 0;
}
```

### 拼接字符串

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string s1 = "Hello";
    string s2 = "World";
    
    // 方式1：+ 运算符
    string s3 = s1 + " " + s2;  // "Hello World"
    
    // 方式2：+= 运算符
    s1 += " ";
    s1 += s2;  // "Hello World"
    
    // 方式3：append()
    string s4 = "Hello";
    s4.append(" World");  // "Hello World"
    
    // append 多个字符
    s4.append(3, '!');  // "Hello World!!!"
    
    // 方式4：拼接 C 字符串
    string s5 = "Hello";
    s5 += " World";
    
    // 方式5：拼接字符
    string s6 = "Hello";
    s6 += '!';  // "Hello!"
    
    cout << s3 << endl;
    cout << s1 << endl;
    cout << s4 << endl;
    
    return 0;
}
```

### 子串操作

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello World";
    
    // substr(起始位置, 长度)
    string sub1 = str.substr(0, 5);   // "Hello"
    string sub2 = str.substr(6);      // "World" (到末尾)
    string sub3 = str.substr(6, 3);   // "Wor"
    
    cout << sub1 << endl;
    cout << sub2 << endl;
    cout << sub3 << endl;
    
    // ⚠️ 注意：越界会抛出异常
    try {
        string bad = str.substr(100);
    } catch (const out_of_range& e) {
        cout << "Error: " << e.what() << endl;
    }
    
    return 0;
}
```

### 清空和重置

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello World";
    
    // 清空字符串
    str.clear();
    cout << "After clear: [" << str << "]" << endl;  // []
    cout << "Empty? " << str.empty() << endl;        // 1 (true)
    
    // 重新赋值
    str = "New String";
    cout << str << endl;
    
    // 调整大小
    str.resize(5);      // "New S"
    cout << str << endl;
    
    str.resize(10, '*'); // "New S*****"
    cout << str << endl;
    
    return 0;
}
```

---

## 查找和搜索

### find() - 查找子串

**.find()返还的size_t表示0-base下的坐标，同时表示此位置（除去这个位置）之前的1-base string长度**

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello World, Hello C++";
    
    // 从头开始查找第一个匹配
    size_t pos1 = str.find("Hello");
    cout << "First 'Hello' at: " << pos1 << endl;  // 0
    
    // 从指定位置开始查找
    size_t pos2 = str.find("Hello", 1);
    cout << "Next 'Hello' at: " << pos2 << endl;   // 13
    
    // 查找字符
    size_t pos3 = str.find('W');
    cout << "'W' at: " << pos3 << endl;            // 6
    
    // 查找不存在的子串
    size_t pos4 = str.find("Python");
    if (pos4 == string::npos) {
        cout << "'Python' not found" << endl;
    }
    
    return 0;
}
```

### rfind() - 从后向前查找

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello World, Hello C++";
    
    // 从后向前查找（找到最后一个）
    size_t pos = str.rfind("Hello");
    cout << "Last 'Hello' at: " << pos << endl;  // 13
    
    // 从指定位置开始向前查找
    pos = str.rfind("Hello", 10);
    cout << "Previous 'Hello' at: " << pos << endl;  // 0
    
    return 0;
}
```

### find_first_of() 和 find_last_of()

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello World";
    string vowels = "aeiou";
    
    // 查找第一个元音字母
    size_t pos1 = str.find_first_of(vowels);
    cout << "First vowel at: " << pos1 << endl;  // 1 ('e')
    
    // 查找最后一个元音字母
    size_t pos2 = str.find_last_of(vowels);
    cout << "Last vowel at: " << pos2 << endl;   // 7 ('o')
    
    // 查找第一个不是元音的字母
    size_t pos3 = str.find_first_not_of(vowels);
    cout << "First consonant at: " << pos3 << endl;  // 0 ('H')
    
    return 0;
}
```

### 查找函数总览

| 函数 | 说明 | 示例 |
|------|------|------|
| `find(str, pos)` | 从 pos 开始查找 str | `s.find("hello", 0)` |
| `rfind(str, pos)` | 从 pos 向前查找 str | `s.rfind("hello")` |
| `find_first_of(chars)` | 查找 chars 中任一字符首次出现 | `s.find_first_of("aeiou")` |
| `find_last_of(chars)` | 查找 chars 中任一字符最后出现 | `s.find_last_of("aeiou")` |
| `find_first_not_of(chars)` | 查找第一个不在 chars 中的字符 | `s.find_first_not_of(" ")` |
| `find_last_not_of(chars)` | 查找最后一个不在 chars 中的字符 | `s.find_last_not_of(" ")` |

### 实用示例：去除前后空格

```cpp
#include <iostream>
#include <string>
using namespace std;

string trim(const string& str) {
    // 找到第一个非空格字符
    size_t first = str.find_first_not_of(" \t\n\r");
    if (first == string::npos) return "";
    
    // 找到最后一个非空格字符
    size_t last = str.find_last_not_of(" \t\n\r");
    
    return str.substr(first, last - first + 1);
}

int main() {
    string str = "   Hello World   ";
    string trimmed = trim(str);
    cout << "[" << trimmed << "]" << endl;  // [Hello World]
    
    return 0;
}
```

---

## 修改操作

### 插入 insert()

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello World";
    
    // 在位置 5 插入字符串
    str.insert(5, ",");
    cout << str << endl;  // "Hello, World"
    
    // 插入多个字符
    str.insert(6, 3, '-');
    cout << str << endl;  // "Hello,--- World"
    
    // 插入另一个 string
    string extra = " Beautiful";
    str.insert(6, extra);
    cout << str << endl;
    
    return 0;
}
```

### 删除 erase()

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello World";
    
    // 删除从位置 5 开始的 6 个字符
    str.erase(5, 6);
    cout << str << endl;  // "Hello"
    
    // 删除从位置 3 到末尾
    str = "Hello World";
    str.erase(5);
    cout << str << endl;  // "Hello"
    
    // 删除所有内容
    str.erase();
    cout << "[" << str << "]" << endl;  // []
    
    return 0;
}
```

### 替换 replace()

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello World";
    
    // 从位置 6 开始，替换 5 个字符为 "C++"
    str.replace(6, 5, "C++");
    cout << str << endl;  // "Hello C++"
    
    // 替换所有出现的子串
    str = "Hello World, Hello C++";
    size_t pos = 0;
    while ((pos = str.find("Hello", pos)) != string::npos) {
        str.replace(pos, 5, "Hi");
        pos += 2;  // 移动到替换后的位置之后
    }
    cout << str << endl;  // "Hi World, Hi C++"
    
    return 0;
}
```

### 交换 swap()

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string s1 = "Hello";
    string s2 = "World";
    
    cout << "Before: " << s1 << ", " << s2 << endl;
    
    // 交换两个字符串
    s1.swap(s2);
    
    cout << "After: " << s1 << ", " << s2 << endl;
    
    // 或使用全局 swap
    swap(s1, s2);
    cout << "After swap again: " << s1 << ", " << s2 << endl;
    
    return 0;
}
```

### push_back() 和 pop_back()

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello";
    
    // 在末尾添加字符
    str.push_back('!');
    cout << str << endl;  // "Hello!"
    
    // 删除最后一个字符
    str.pop_back();
    cout << str << endl;  // "Hello"
    
    return 0;
}
```

---

## 比较操作

### 使用运算符比较

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string s1 = "apple";
    string s2 = "banana";
    string s3 = "apple";
    
    // 相等比较
    if (s1 == s3) {
        cout << "s1 equals s3" << endl;
    }
    
    // 不等比较
    if (s1 != s2) {
        cout << "s1 not equals s2" << endl;
    }
    
    // 字典序比较
    if (s1 < s2) {
        cout << "s1 < s2" << endl;  // apple < banana
    }
    
    if (s2 > s1) {
        cout << "s2 > s1" << endl;
    }
    
    return 0;
}
```

### 使用 compare() 函数

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string s1 = "apple";
    string s2 = "banana";
    string s3 = "apple";
    
    // compare() 返回：
    // < 0: s1 < s2
    // = 0: s1 == s2
    // > 0: s1 > s2
    
    int result1 = s1.compare(s2);
    cout << "s1.compare(s2) = " << result1 << endl;  // < 0
    
    int result2 = s1.compare(s3);
    cout << "s1.compare(s3) = " << result2 << endl;  // = 0
    
    // 比较子串
    string str = "Hello World";
    int result3 = str.compare(0, 5, "Hello");
    cout << "First 5 chars compare to 'Hello': " << result3 << endl;  // = 0
    
    return 0;
}
```

### 大小写不敏感比较

```cpp
#include <iostream>
#include <string>
#include <algorithm>
using namespace std;

bool compareIgnoreCase(string s1, string s2) {
    // 转换为小写后比较
    transform(s1.begin(), s1.end(), s1.begin(), ::tolower);
    transform(s2.begin(), s2.end(), s2.begin(), ::tolower);
    return s1 == s2;
}

int main() {
    string s1 = "Hello";
    string s2 = "HELLO";
    
    if (compareIgnoreCase(s1, s2)) {
        cout << "Strings are equal (ignore case)" << endl;
    }
    
    return 0;
}
```

---

## 转换操作

### string 转数字

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    // string 转 int
    string s1 = "123";
    int num1 = stoi(s1);
    cout << "int: " << num1 << endl;
    
    // string 转 long
    string s2 = "1234567890";
    long num2 = stol(s2);
    cout << "long: " << num2 << endl;
    
    // string 转 long long
    string s3 = "9223372036854775807";
    long long num3 = stoll(s3);
    cout << "long long: " << num3 << endl;
    
    // string 转 float
    string s4 = "3.14";
    float num4 = stof(s4);
    cout << "float: " << num4 << endl;
    
    // string 转 double
    string s5 = "3.14159265359";
    double num5 = stod(s5);
    cout << "double: " << num5 << endl;
    
    // 处理转换错误
    try {
        string invalid = "abc";
        int bad = stoi(invalid);
    } catch (const invalid_argument& e) {
        cout << "Invalid argument: " << e.what() << endl;
    } catch (const out_of_range& e) {
        cout << "Out of range: " << e.what() << endl;
    }
    
    return 0;
}
```

### 数字转 string

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    // int 转 string
    int num1 = 123;
    string s1 = to_string(num1);
    cout << "string: " << s1 << endl;
    
    // double 转 string
    double num2 = 3.14159;
    string s2 = to_string(num2);
    cout << "string: " << s2 << endl;
    
    // long long 转 string
    long long num3 = 9223372036854775807LL;
    string s3 = to_string(num3);
    cout << "string: " << s3 << endl;
    
    return 0;
}
```

### C 字符串转换

```cpp
#include <iostream>
#include <string>
#include <cstring>
using namespace std;

int main() {
    // string 转 C 字符串
    string str = "Hello World";
    
    // 方式1：c_str() - 返回 const char*
    const char* cstr1 = str.c_str();
    cout << cstr1 << endl;
    
    // 方式2：data() - 返回 const char*
    const char* cstr2 = str.data();
    cout << cstr2 << endl;
    
    // C 字符串转 string
    char carr[] = "Hello";
    string s1 = carr;
    string s2(carr);
    
    // 使用 strlen (C 函数)
    cout << "C string length: " << strlen(cstr1) << endl;
    
    return 0;
}
```

### 大小写转换

```cpp
#include <iostream>
#include <string>
#include <algorithm>
using namespace std;

int main() {
    string str = "Hello World";
    
    // 转小写
    string lower = str;
    transform(lower.begin(), lower.end(), lower.begin(), ::tolower);
    cout << "Lower: " << lower << endl;  // "hello world"
    
    // 转大写
    string upper = str;
    transform(upper.begin(), upper.end(), upper.begin(), ::toupper);
    cout << "Upper: " << upper << endl;  // "HELLO WORLD"
    
    // 手动实现
    for (char& c : str) {
        c = tolower(c);
    }
    cout << "Manual lower: " << str << endl;
    
    return 0;
}
```

---

## 迭代器

### 基本迭代器

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "Hello";
    
    // 正向迭代器
    cout << "Forward: ";
    for (auto it = str.begin(); it != str.end(); ++it) {
        cout << *it << " ";
    }
    cout << endl;
    
    // 反向迭代器
    cout << "Reverse: ";
    for (auto it = str.rbegin(); it != str.rend(); ++it) {
        cout << *it << " ";
    }
    cout << endl;
    
    // const 迭代器
    const string cstr = "World";
    for (auto it = cstr.cbegin(); it != cend(); ++it) {
        cout << *it << " ";
        // *it = 'x';  // ❌ 错误：不能修改
    }
    cout << endl;
    
    return 0;
}
```

### 迭代器类型

| 迭代器 | 说明 |
|--------|------|
| `begin()` / `end()` | 正向迭代器 |
| `rbegin()` / `rend()` | 反向迭代器 |
| `cbegin()` / `cend()` | const 正向迭代器 |
| `crbegin()` / `crend()` | const 反向迭代器 |

### 使用迭代器修改

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str = "hello";
    
    // 使用迭代器修改字符
    for (auto it = str.begin(); it != str.end(); ++it) {
        *it = toupper(*it);
    }
    cout << str << endl;  // "HELLO"
    
    // 删除元素
    str = "Hello";
    str.erase(str.begin() + 1);  // 删除 'e'