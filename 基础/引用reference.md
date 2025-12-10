# C++ 引用(&)笔记

## 目录
- [基础概念](#基础概念)
- [引用的声明和初始化](#引用的声明和初始化)
- [引用 vs 指针](#引用-vs-指针)
- [引用作为函数参数](#引用作为函数参数)
- [引用作为函数返回值](#引用作为函数返回值)
- [常量引用](#常量引用)
- [右值引用](#右值引用)
- [常见陷阱](#常见陷阱)

---

## 基础概念

### 什么是引用？

**引用(Reference)** 是C++中的一种特殊类型，它是已存在变量的**别名**（另一个名字）。

```cpp
int a = 10;
int& ref = a;  // ref 是 a 的引用（别名）

ref = 20;      // 修改 ref 就是修改 a
cout << a;     // 输出: 20
```

### 核心特点

- ✅ 引用**必须在声明时初始化**
- ✅ 引用一旦绑定，**不能再绑定到其他变量**
- ✅ 引用本身**不占用额外内存**（编译器优化）
- ✅ 操作引用就是操作原变量

---

## 引用的声明和初始化

### 基本语法

```cpp
#include <iostream>
using namespace std;

int main() {
    int a = 10;
    
    // 声明引用
    int& ref = a;    // ref 是 a 的别名
    
    cout << a << endl;     // 10
    cout << ref << endl;   // 10
    
    ref = 20;              // 修改 ref
    cout << a << endl;     // 20（a 也改变了）
    
    a = 30;                // 修改 a
    cout << ref << endl;   // 30（ref 也改变了）
    
    return 0;
}
```

### 常见错误

```cpp
int main() {
    // ❌ 错误：引用必须初始化
    int& ref1;
    
    // ❌ 错误：不能引用字面值（常量）
    int& ref2 = 10;
    
    // ✅ 正确：可以用 const 引用绑定字面值
    const int& ref3 = 10;
    
    // ❌ 错误：引用的类型必须匹配
    double d = 3.14;
    int& ref4 = d;  // 类型不匹配
    
    // ✅ 正确
    int a = 10;
    int& ref5 = a;
    
    return 0;
}
```

### 引用的引用

```cpp
#include <iostream>
using namespace std;

int main() {
    int a = 10;
    int& ref1 = a;
    int& ref2 = ref1;  // ref2 也是 a 的引用（不是 ref1 的引用）
    
    ref2 = 30;
    cout << a << endl;     // 30
    cout << ref1 << endl;  // 30
    cout << ref2 << endl;  // 30
    
    // 所有引用都指向同一个变量 a
    
    return 0;
}
```

---

## 引用 vs 指针

### 对比表格

| 特性 | 引用 | 指针 |
|------|------|------|
| **符号** | `&` | `*` |
| **初始化** | 必须初始化 | 可以不初始化 |
| **空值** | 不能为空 | 可以为 `nullptr` |
| **重新绑定** | 不能改变指向 | 可以改变指向 |
| **语法** | 直接使用，像普通变量 | 需要解引用 `*` |
| **内存** | 不占用额外空间（通常） | 占用指针大小空间 |
| **使用场景** | 函数参数、返回值 | 动态内存、数据结构 |

### 代码对比

```cpp
#include <iostream>
using namespace std;

int main() {
    int a = 10;
    int b = 20;
    
    // === 引用 ===
    int& ref = a;
    ref = 30;        // 修改 a
    cout << a << endl;  // 30
    
    ref = b;         // 不是改变引用的绑定，而是把 b 的值赋给 a
    cout << a << endl;  // 20
    cout << b << endl;  // 20
    
    // === 指针 ===
    int* ptr = &a;
    *ptr = 40;       // 修改 a
    cout << a << endl;  // 40
    
    ptr = &b;        // 改变指针的指向
    *ptr = 50;       // 修改 b
    cout << b << endl;  // 50
    cout << a << endl;  // 40（a 不变）
    
    return 0;
}
```

### 何时使用引用 vs 指针

```cpp
// ✅ 使用引用：
// 1. 函数参数（避免拷贝）
void process(const string& str) { }

// 2. 函数返回值（返回成员）
class MyClass {
    int data;
public:
    int& getData() { return data; }
};

// 3. 范围for循环
for (const auto& item : vec) { }

// ✅ 使用指针：
// 1. 需要改变指向
int* ptr = &a;
ptr = &b;  // 指向不同的变量

// 2. 可能为空
int* ptr = nullptr;
if (condition) {
    ptr = &a;
}

// 3. 动态内存分配
int* arr = new int[100];

// 4. 数据结构（链表、树等）
struct Node {
    int data;
    Node* next;
};
```

---

## 引用作为函数参数

### 值传递 vs 引用传递

```cpp
#include <iostream>
#include <vector>
using namespace std;

// 值传递：复制参数，开销大
void funcByValue(vector<int> vec) {
    vec[0] = 100;  // 修改的是副本
}

// 引用传递：不复制，可以修改原数据
void funcByReference(vector<int>& vec) {
    vec[0] = 100;  // 修改原数据
}

// const 引用：不复制，不能修改（最常用）
void funcByConstReference(const vector<int>& vec) {
    // vec[0] = 100;  // ❌ 错误：不能修改
    cout << vec[0] << endl;  // ✅ 可以读取
}

int main() {
    vector<int> data = {1, 2, 3, 4, 5};
    
    funcByValue(data);
    cout << data[0] << endl;  // 1（未改变）
    
    funcByReference(data);
    cout << data[0] << endl;  // 100（已改变）
    
    funcByConstReference(data);  // 高效且安全
    
    return 0;
}
```

### 交换两个变量

```cpp
#include <iostream>
using namespace std;

// ❌ 错误：值传递，交换的是副本
void swapWrong(int a, int b) {
    int temp = a;
    a = b;
    b = temp;
}

// ✅ 正确：引用传递，交换原变量
void swapCorrect(int& a, int& b) {
    int temp = a;
    a = b;
    b = temp;
}

int main() {
    int x = 10, y = 20;
    
    swapWrong(x, y);
    cout << x << ", " << y << endl;  // 10, 20（未交换）
    
    swapCorrect(x, y);
    cout << x << ", " << y << endl;  // 20, 10（已交换）
    
    return 0;
}
```

### 最佳实践

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;

struct LargeData {
    int arr[10000];
};

// ❌ 不推荐：小数据类型用引用（开销相当）
void processInt(const int& x) { }

// ✅ 推荐：小数据类型用值传递
void processInt2(int x) { }

// ❌ 不推荐：大对象用值传递（复制开销大）
void processLargeData(LargeData data) { }

// ✅ 推荐：大对象用 const 引用（只读）
void processLargeData2(const LargeData& data) { }

// ✅ 推荐：需要修改用引用
void modifyLargeData(LargeData& data) {
    data.arr[0] = 100;
}

// 通用规则：
// - 内置类型(int, double等)：值传递
// - 大对象(string, vector, 自定义类型)：const引用（只读）或引用（修改）
```

---

## 引用作为函数返回值

### 返回局部变量的引用（危险！）

```cpp
#include <iostream>
using namespace std;

// ❌ 危险：返回局部变量的引用
int& dangerousFunc() {
    int local = 10;
    return local;  // local 在函数结束后销毁！
}

int main() {
    int& ref = dangerousFunc();
    cout << ref << endl;  // 未定义行为（访问已销毁的内存）
    
    return 0;
}
```

### 正确的用法

```cpp
#include <iostream>
#include <vector>
using namespace std;

// ✅ 正确：返回成员变量的引用
class MyClass {
    int data;
public:
    MyClass(int val) : data(val) {}
    
    int& getData() {
        return data;  // data 的生命周期与对象相同
    }
    
    const int& getData() const {  // const 版本
        return data;
    }
};

// ✅ 正确：返回参数的引用
int& max(int& a, int& b) {
    return (a > b) ? a : b;
}

// ✅ 正确：返回容器元素的引用
int& getElement(vector<int>& vec, int index) {
    return vec[index];
}

int main() {
    MyClass obj(42);
    obj.getData() = 100;  // 可以修改
    cout << obj.getData() << endl;  // 100
    
    int x = 10, y = 20;
    max(x, y) = 30;  // 修改较大的那个
    cout << y << endl;  // 30
    
    vector<int> vec = {1, 2, 3, 4, 5};
    getElement(vec, 2) = 100;  // 修改 vec[2]
    cout << vec[2] << endl;  // 100
    
    return 0;
}
```

### 链式调用

```cpp
#include <iostream>
using namespace std;

class Calculator {
    int value;
public:
    Calculator(int val = 0) : value(val) {}
    
    // 返回 *this 的引用，支持链式调用
    Calculator& add(int x) {
        value += x;
        return *this;
    }
    
    Calculator& subtract(int x) {
        value -= x;
        return *this;
    }
    
    Calculator& multiply(int x) {
        value *= x;
        return *this;
    }
    
    int getValue() const {
        return value;
    }
};

int main() {
    Calculator calc(10);
    
    // 链式调用
    int result = calc.add(5)
                     .multiply(2)
                     .subtract(3)
                     .getValue();
    
    cout << result << endl;  // (10 + 5) * 2 - 3 = 27
    
    return 0;
}
```

---

## 常量引用

### 基本用法

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    int a = 10;
    
    // const 引用：不能通过引用修改原变量
    const int& ref = a;
    
    cout << ref << endl;  // ✅ 可以读取: 10
    // ref = 20;          // ❌ 错误：不能修改
    
    a = 20;               // ✅ 可以通过原变量修改
    cout << ref << endl;  // 20
    
    return 0;
}
```

### const 引用的特殊能力

```cpp
#include <iostream>
using namespace std;

int main() {
    // const 引用可以绑定字面值
    const int& ref1 = 10;   // ✅ 正确
    // int& ref2 = 10;      // ❌ 错误：非 const 引用不能绑定字面值
    
    // const 引用可以绑定临时对象
    const string& ref3 = string("Hello");  // ✅ 正确
    
    // const 引用可以绑定不同类型（会创建临时对象）
    double d = 3.14;
    const int& ref4 = d;    // ✅ 正确（d 转换为 int 临时对象）
    // int& ref5 = d;       // ❌ 错误
    
    return 0;
}
```

### 函数参数中的 const 引用

```cpp
#include <iostream>
#include <string>
using namespace std;

// 推荐：大对象用 const 引用传递
void print(const string& str) {
    cout << str << endl;
    // str[0] = 'X';  // ❌ 错误：不能修改
}

int main() {
    string s = "Hello";
    print(s);              // 传递变量
    print("World");        // 传递字面值（const 引用可以接受）
    print(string("C++"));  // 传递临时对象
    
    return 0;
}
```

---

## 右值引用

### 左值 vs 右值

```cpp
#include <iostream>
using namespace std;

int main() {
    // 左值：有名字，可以取地址
    int a = 10;       // a 是左值
    int& ref1 = a;    // ✅ 左值引用绑定左值
    
    // 右值：临时对象，不能取地址
    int b = 20 + 30;  // 20 + 30 是右值（表达式结果）
    // int& ref2 = 20 + 30;  // ❌ 错误：左值引用不能绑定右值
    
    // const 左值引用可以绑定右值
    const int& ref3 = 20 + 30;  // ✅ 正确
    
    return 0;
}
```

### 右值引用（C++11）

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 右值引用：使用 &&
    int&& rref = 10;       // 绑定右值
    cout << rref << endl;  // 10
    
    rref = 20;             // 可以修改
    cout << rref << endl;  // 20
    
    // ❌ 错误：右值引用不能绑定左值
    int a = 30;
    // int&& rref2 = a;
    
    // ✅ 正确：使用 std::move 将左值转换为右值
    int&& rref3 = std::move(a);
    
    return 0;
}
```

### 移动语义

```cpp
#include <iostream>
#include <vector>
#include <utility>
using namespace std;

class MyVector {
    int* data;
    size_t size;
    
public:
    // 构造函数
    MyVector(size_t n) : size(n) {
        data = new int[n];
        cout << "Constructor: allocating " << n << " elements" << endl;
    }
    
    // 拷贝构造函数
    MyVector(const MyVector& other) : size(other.size) {
        data = new int[size];
        copy(other.data, other.data + size, data);
        cout << "Copy constructor: copying " << size << " elements" << endl;
    }
    
    // 移动构造函数（C++11）
    MyVector(MyVector&& other) noexcept : data(other.data), size(other.size) {
        other.data = nullptr;
        other.size = 0;
        cout << "Move constructor: stealing resources" << endl;
    }
    
    ~MyVector() {
        delete[] data;
        cout << "Destructor" << endl;
    }
};

MyVector createVector() {
    return MyVector(1000);  // 返回临时对象
}

int main() {
    cout << "=== 拷贝 ===" << endl;
    MyVector v1(100);
    MyVector v2 = v1;  // 调用拷贝构造函数
    
    cout << "\n=== 移动 ===" << endl;
    MyVector v3 = createVector();  // 调用移动构造函数（高效！）
    
    return 0;
}
```

### 完美转发

```cpp
#include <iostream>
#include <utility>
using namespace std;

void process(int& x) {
    cout << "左值引用版本: " << x << endl;
}

void process(int&& x) {
    cout << "右值引用版本: " << x << endl;
}

// 完美转发：保持参数的值类别
template<typename T>
void wrapper(T&& arg) {
    process(std::forward<T>(arg));  // 完美转发
}

int main() {
    int a = 10;
    
    wrapper(a);         // 传递左值，调用左值引用版本
    wrapper(20);        // 传递右值，调用右值引用版本
    wrapper(move(a));   // 将左值转为右值，调用右值引用版本
    
    return 0;
}
```

---

## 常见陷阱

### 1. 悬空引用

```cpp
#include <iostream>
using namespace std;

int& dangerousFunc() {
    int x = 10;
    return x;  // ❌ 返回局部变量的引用
}

int main() {
    int& ref = dangerousFunc();
    cout << ref << endl;  // 未定义行为！
    
    return 0;
}
```

### 2. 引用数组（不存在）

```cpp
int main() {
    // ❌ 错误：不存在引用的数组
    int& refs[10];  // 编译错误
    
    // ✅ 正确：数组元素的引用（指针数组）
    int a = 1, b = 2, c = 3;
    int* ptrs[3] = {&a, &b, &c};
    
    // ✅ 正确：使用 vector
    vector<int> vec = {1, 2, 3};
    for (int& ref : vec) {
        ref *= 2;
    }
    
    return 0;
}
```

### 3. const 引用的生命周期

```cpp
#include <iostream>
#include <string>
using namespace std;

const string& getString() {
    return "Hello";  // 返回临时对象的引用
}

int main() {
    // ❌ 危险：临时对象在表达式结束后销毁
    const string& ref = getString();
    // cout << ref << endl;  // 未定义行为
    
    // ✅ 正确：const 引用绑定字面值时，生命周期延长
    const string& ref2 = "Hello";  // 临时对象生命周期延长
    cout << ref2 << endl;  // 安全
    
    return 0;
}
```

### 4. 引用成员变量

```cpp
#include <iostream>
using namespace std;

class MyClass {
    int& ref;  // 引用成员
    
public:
    // 必须在初始化列表中初始化引用成员
    MyClass(int& r) : ref(r) {}  // ✅ 正确
    
    // MyClass(int& r) {
    //     ref = r;  // ❌ 错误：不能在构造函数体中初始化引用
    // }
    
    void print() {
        cout << ref << endl;
    }
};

int main() {
    int x = 10;
    MyClass obj(x);
    obj.print();  // 10
    
    x = 20;
    obj.print();  // 20（引用的变量改变）
    
    return 0;
}
```

### 5. 范围 for 循环中的引用

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3, 4, 5};
    
    // ❌ 不推荐：值拷贝（对于大对象开销大）
    for (int x : vec) {
        x *= 2;  // 修改的是副本
    }
    cout << vec[0] << endl;  // 1（未改变）
    
    // ✅ 推荐：引用（可以修改）
    for (int& x : vec) {
        x *= 2;  // 修改原元素
    }
    cout << vec[0] << endl;  // 2（已改变）
    
    // ✅ 推荐：const 引用（只读，高效）
    for (const int& x : vec) {
        cout << x << " ";
        // x *= 2;  // ❌ 错误：不能修改
    }
    cout << endl;
    
    // ✅ 推荐：auto 引用（类型推导）
    for (auto& x : vec) {
        x += 1;
    }
    
    for (const auto& x : vec) {
        cout << x << " ";
    }
    cout << endl;
    
    return 0;
}
```

---

## 总结

### 核心要点

1. **引用是别名**：操作引用就是操作原变量
2. **必须初始化**：声明时必须绑定到某个变量
3. **不能重新绑定**：一旦绑定，不能改变
4. **函数参数优先用 const 引用**：避免拷贝，提高效率
5. **小心悬空引用**：不要返回局部变量的引用

### 最佳实践

```cpp
// ✅ 函数参数
void func(const LargeObject& obj);  // 大对象用 const 引用
void modify(LargeObject& obj);      // 需要修改时用引用
void simple(int x);                 // 小对象用值传递

// ✅ 范围 for 循环
for (const auto& item : container) { }  // 只读
for (auto& item : container) { }        // 需要修改

// ✅ 返回值
int& getElement();           // 返回成员或容器元素
MyClass& chainable();        // 链式调用返回 *this

// ❌ 避免
int& local() { int x; return x; }  // 返回局部变量引用
```

### 引用类型速查表

| 类型 | 语法 | 说明 | 用途 |
|------|------|------|------|
| **左值引用** | `int& ref` | 绑定左值 | 函数参数、返回值 |
| **const左值引用** | `const int& ref` | 绑定左值或右值 | 只读参数（最常用） |
| **右值引用** | `int&& ref` | 绑定右值 | 移动语义、完美转发 |
| **const右值引用** | `const int&& ref` | 绑定右值（少用） | 特殊场景 |