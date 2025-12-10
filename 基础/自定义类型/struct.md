# 结构体(struct)

## 目录
- [基础概念](#基础概念)
- [结构体的定义与声明](#结构体的定义与声明)
- [结构体的初始化](#结构体的初始化)
- [结构体的访问与操作](#结构体的访问与操作)
- [结构体与函数](#结构体与函数)
- [结构体与指针](#结构体与指针)
- [结构体数组](#结构体数组)
- [结构体嵌套](#结构体嵌套)
- [结构体与类的区别](#结构体与类的区别)

---

## 基础概念

### 什么是结构体？

**结构体(struct)** 是C++中一种用户自定义的数据类型，可以将不同类型的数据组合在一起，形成一个新的复合数据类型。

### 为什么需要结构体？

```cpp
// 没有结构体时：需要多个独立变量
string studentName = "Alice";
int studentAge = 20;
double studentScore = 85.5;
string studentID = "2021001";

// 有结构体后：数据组织更清晰
struct Student {
    string name;
    int age;
    double score;
    string id;
};

Student s1 = {"Alice", 20, 85.5, "2021001"};
```

**优势**：
- ✅ 逻辑相关的数据组织在一起
- ✅ 代码更易读、易维护
- ✅ 便于传递和管理数据
- ✅ 可以创建复杂的数据结构

---

## 结构体的定义与声明

### 基本语法

```cpp
// 语法格式
struct 结构体名 {
    数据类型 成员1;
    数据类型 成员2;
    // ...
};  // 注意：分号不能省略！
```

### 定义方式1：标准定义

```cpp
#include <iostream>
#include <string>
using namespace std;

// 定义结构体
struct Student {
    string name;
    int age;
    double score;
    string id;
};

int main() {
    // 声明结构体变量
    Student s1;
    Student s2, s3;  // 一次声明多个
    
    return 0;
}
```

### 定义方式2：定义时声明变量

```cpp
// 定义结构体的同时声明变量
struct Student {
    string name;
    int age;
    double score;
} s1, s2, s3;  // s1, s2, s3 是 Student 类型的变量

int main() {
    s1.name = "Alice";
    return 0;
}
```

### 定义方式3：匿名结构体

```cpp
// 匿名结构体（不推荐，只能使用一次）
struct {
    string name;
    int age;
} person1, person2;

int main() {
    person1.name = "Bob";
    // 但无法再创建这个类型的其他变量
    return 0;
}
```

### 定义方式4：使用 typedef

```cpp
// 使用 typedef 简化类型名
typedef struct Student {
    string name;
    int age;
    double score;
} Stu;  // Stu 是 Student 的别名

int main() {
    Student s1;  // 可以使用 Student
    Stu s2;      // 也可以使用 Stu
    
    return 0;
}
```

### C++11 using 关键字

```cpp
// C++11 更现代的方式
struct Student {
    string name;
    int age;
    double score;
};

using Stu = Student;  // 创建别名

int main() {
    Stu s1;
    s1.name = "Charlie";
    return 0;
}
```

---

## 结构体的初始化

### 方式1：声明后逐个赋值

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Student {
    string name;
    int age;
    double score;
};

int main() {
    Student s1;
    s1.name = "Alice";
    s1.age = 20;
    s1.score = 85.5;
    
    cout << s1.name << endl;  // Alice
    return 0;
}
```

### 方式2：定义时初始化（列表初始化）

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Student {
    string name;
    int age;
    double score;
};

int main() {
    // C 风格初始化
    Student s1 = {"Alice", 20, 85.5};
    
    // C++11 列表初始化（推荐）
    Student s2{"Bob", 21, 90.0};
    
    // 部分初始化（其余成员使用默认值）
    Student s3{"Charlie", 19};  // score 默认为 0.0
    
    // 完全省略花括号
    Student s4 = {"David", 22, 88.0};
    
    return 0;
}
```

### 方式3：使用构造函数（C++特性）

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Student {
    string name;
    int age;
    double score;
    
    // 默认构造函数
    Student() {
        name = "Unknown";
        age = 0;
        score = 0.0;
    }
    
    // 带参数的构造函数
    Student(string n, int a, double s) {
        name = n;
        age = a;
        score = s;
    }
    
    // C++11 成员初始化列表（推荐）
    Student(string n, int a, double s) : name(n), age(a), score(s) {}
};

int main() {
    Student s1;                      // 使用默认构造函数
    Student s2("Alice", 20, 85.5);   // 使用带参数构造函数
    Student s3{"Bob", 21, 90.0};     // 直接初始化
    
    return 0;
}
```

### 方式4：C++20 指定初始化器

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Student {
    string name;
    int age;
    double score;
};

int main() {
    // C++20 指定初始化器（可以跳过某些成员）
    Student s1 {
        .name = "Alice",
        .age = 20,
        .score = 85.5
    };
    
    Student s2 {
        .name = "Bob",
        .age = 21
        // score 使用默认值
    };
    
    return 0;
}
```

### 方式5：默认成员初始化

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Student {
    string name = "Unknown";  // 默认值
    int age = 0;
    double score = 0.0;
};

int main() {
    Student s1;  // 使用默认值
    cout << s1.name << endl;  // Unknown
    
    Student s2{"Alice", 20, 85.5};  // 覆盖默认值
    cout << s2.name << endl;  // Alice
    
    return 0;
}
```

---

## 结构体的访问与操作

### 成员访问运算符

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Point {
    int x;
    int y;
};

int main() {
    Point p1;
    
    // 使用点运算符(.)访问成员
    p1.x = 10;
    p1.y = 20;
    
    cout << "Point: (" << p1.x << ", " << p1.y << ")" << endl;
    // 输出: Point: (10, 20)
    
    return 0;
}
```

### 结构体赋值

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Student {
    string name;
    int age;
    double score;
};

int main() {
    Student s1{"Alice", 20, 85.5};
    
    // 结构体之间可以直接赋值（浅拷贝）
    Student s2 = s1;
    
    cout << s2.name << endl;  // Alice
    
    // 修改 s2 不影响 s1
    s2.name = "Bob";
    cout << s1.name << endl;  // Alice（未改变）
    cout << s2.name << endl;  // Bob
    
    return 0;
}
```

### 结构体比较

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Point {
    int x;
    int y;
    
    // 重载 == 运算符
    bool operator==(const Point& other) const {
        return x == other.x && y == other.y;
    }
    
    // 重载 != 运算符
    bool operator!=(const Point& other) const {
        return !(*this == other);
    }
};

int main() {
    Point p1{10, 20};
    Point p2{10, 20};
    Point p3{30, 40};
    
    // 不能直接用 == 比较（除非重载）
    // if (p1 == p2) { }  // 错误（如果没有重载）
    
    // 使用重载后的运算符
    if (p1 == p2) {
        cout << "p1 和 p2 相等" << endl;
    }
    
    if (p1 != p3) {
        cout << "p1 和 p3 不相等" << endl;
    }
    
    return 0;
}
```

---

## 结构体与函数

### 内部自定义排序

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Player {
    string name;
    int score;

    // 静态成员函数用于排序
    static bool cmp(const Player &a, const Player &b) {
        return a.score > b.score; // 按分数降序
    }
};

int main() {
    vector<Player> players = {
        {"Alice", 90},
        {"Bob", 75},
        {"Charlie", 85}
    };

    // 使用struct内的静态函数排序
    sort(players.begin(), players.end(), Player::cmp);

    for (auto &p : players)
        cout << p.name << " " << p.score << endl;
}

```

### 传值调用（值传递）

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Student {
    string name;
    int age;
    double score;
};

// 传值调用：会复制整个结构体
void printStudent(Student s) {
    cout << "Name: " << s.name << endl;
    cout << "Age: " << s.age << endl;
    cout << "Score: " << s.score << endl;
    
    // 修改不会影响原结构体
    s.age = 100;
}

int main() {
    Student s1{"Alice", 20, 85.5};
    
    printStudent(s1);
    
    cout << s1.age << endl;  // 20（未改变）
    
    return 0;
}
```

### 传引用调用（引用传递，推荐）

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Student {
    string name;
    int age;
    double score;
};

// 传引用：不复制，效率高
void printStudent(const Student& s) {  // const 防止修改
    cout << "Name: " << s.name << endl;
    cout << "Age: " << s.age << endl;
    cout << "Score: " << s.score << endl;
}

// 传引用：可以修改原结构体
void updateScore(Student& s, double newScore) {
    s.score = newScore;
}

int main() {
    Student s1{"Alice", 20, 85.5};
    
    printStudent(s1);
    
    updateScore(s1, 95.0);
    cout << s1.score << endl;  // 95.0（已修改）
    
    return 0;
}
```

### 返回结构体

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Point {
    int x;
    int y;
};

// 返回结构体（按值返回）
Point createPoint(int x, int y) {
    Point p;
    p.x = x;
    p.y = y;
    return p;  // 返回副本（可能会优化为移动）
}

// 更简洁的写法
Point createPoint2(int x, int y) {
    return {x, y};  // C++11 列表初始化
}

int main() {
    Point p1 = createPoint(10, 20);
    Point p2 = createPoint2(30, 40);
    
    cout << "p1: (" << p1.x << ", " << p1.y << ")" << endl;
    cout << "p2: (" << p2.x << ", " << p2.y << ")" << endl;
    
    return 0;
}
```

### 结构体作为函数参数的最佳实践

```cpp
#include <iostream>
#include <string>
using namespace std;

struct LargeData {
    int data[1000];
    string info;
};

// ❌ 不推荐：按值传递大型结构体（开销大）
void processBad(LargeData d) {
    // 复制整个结构体，性能差
}

// ✅ 推荐：按 const 引用传递（只读）
void processGood(const LargeData& d) {
    // 不复制，效率高，且不能修改
}

// ✅ 推荐：按引用传递（需要修改）
void modifyGood(LargeData& d) {
    // 不复制，效率高，可以修改
    d.info = "Modified";
}

// ✅ 推荐：按指针传递（C 风格，也可用）
void modifyByPointer(LargeData* d) {
    if (d != nullptr) {
        d->info = "Modified by pointer";
    }
}

int main() {
    LargeData data;
    
    processGood(data);         // 高效的只读访问
    modifyGood(data);          // 高效的修改
    modifyByPointer(&data);    // 通过指针修改
    
    return 0;
}
```

---

## 结构体与指针

### 基本用法

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Student {
    string name;
    int age;
    double score;
};

int main() {
    Student s1{"Alice", 20, 85.5};
    
    // 指向结构体的指针
    Student* ptr = &s1;
    
    // 通过指针访问成员：使用箭头运算符(->)
    cout << ptr->name << endl;   // Alice
    cout << ptr->age << endl;    // 20
    cout << ptr->score << endl;  // 85.5
    
    // 也可以使用解引用加点运算符（不推荐，不美观）
    cout << (*ptr).name << endl;  // Alice
    
    // 通过指针修改成员
    ptr->age = 21;
    cout << s1.age << endl;  // 21（s1 被修改）
    
    return 0;
}
```

### 动态分配结构体

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Student {
    string name;
    int age;
    double score;
};

int main() {
    // 动态分配单个结构体
    Student* ptr = new Student;
    ptr->name = "Alice";
    ptr->age = 20;
    ptr->score = 85.5;
    
    cout << ptr->name << endl;
    
    delete ptr;  // 记得释放内存
    
    // 动态分配结构体数组
    int n = 5;
    Student* arr = new Student[n];
    
    for (int i = 0; i < n; i++) {
        arr[i].name = "Student" + to_string(i);
        arr[i].age = 20 + i;
        arr[i].score = 80.0 + i;
    }
    
    delete[] arr;  // 释放数组内存
    
    return 0;
}
```

### 智能指针（C++11，推荐）

```cpp
#include <iostream>
#include <string>
#include <memory>
using namespace std;

struct Student {
    string name;
    int age;
    double score;
    
    ~Student() {
        cout << "Student " << name << " destroyed" << endl;
    }
};

int main() {
    // unique_ptr：独占所有权
    unique_ptr<Student> ptr1 = make_unique<Student>();
    ptr1->name = "Alice";
    ptr1->age = 20;
    ptr1->score = 85.5;
    
    cout << ptr1->name << endl;
    // 自动释放，无需 delete
    
    // shared_ptr：共享所有权
    shared_ptr<Student> ptr2 = make_shared<Student>();
    ptr2->name = "Bob";
    
    {
        shared_ptr<Student> ptr3 = ptr2;  // 共享所有权
        cout << ptr3->name << endl;       // Bob
    }  // ptr3 离开作用域，但对象不会销毁
    
    cout << ptr2->name << endl;  // Bob（仍然有效）
    
    return 0;
}  // 所有智能指针离开作用域，自动释放内存
```

---

## 结构体数组

### 定义和初始化

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Student {
    string name;
    int age;
    double score;
};

int main() {
    // 方式1：定义后逐个赋值
    Student students[3];
    students[0] = {"Alice", 20, 85.5};
    students[1] = {"Bob", 21, 90.0};
    students[2] = {"Charlie", 19, 88.0};
    
    // 方式2：定义时初始化
    Student students2[3] = {
        {"Alice", 20, 85.5},
        {"Bob", 21, 90.0},
        {"Charlie", 19, 88.0}
    };
    
    // 方式3：部分初始化
    Student students3[5] = {
        {"Alice", 20, 85.5},
        {"Bob", 21, 90.0}
        // 其余3个使用默认值
    };
    
    // 遍历数组
    for (int i = 0; i < 3; i++) {
        cout << students2[i].name << ": " << students2[i].score << endl;
    }
    
    return 0;
}
```

### 使用 vector（推荐）

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;

struct Student {
    string name;
    int age;
    double score;
};

int main() {
    // 使用 vector（动态数组）
    vector<Student> students;
    
    // 添加元素
    students.push_back({"Alice", 20, 85.5});
    students.push_back({"Bob", 21, 90.0});
    students.push_back({"Charlie", 19, 88.0});
    
    // 使用 emplace_back（更高效）
    students.emplace_back("David", 22, 92.0);
    
    // 遍历（range-based for loop）
    for (const auto& s : students) {
        cout << s.name << ": " << s.score << endl;
    }
    
    // 访问元素
    cout << students[0].name << endl;  // Alice
    cout << students.at(1).name << endl;  // Bob（带边界检查）
    
    // 修改元素
    students[0].score = 95.0;
    
    // 获取大小
    cout << "Total students: " << students.size() << endl;
    
    return 0;
}
```

### 结构体数组排序

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

struct Student {
    string name;
    int age;
    double score;
};

int main() {
    vector<Student> students = {
        {"Alice", 20, 85.5},
        {"Bob", 21, 90.0},
        {"Charlie", 19, 88.0},
        {"David", 22, 92.0}
    };
    
    // 按分数降序排序
    sort(students.begin(), students.end(), [](const Student& a, const Student& b) {
        return a.score > b.score;
    });
    
    cout << "按分数排序:" << endl;
    for (const auto& s : students) {
        cout << s.name << ": " << s.score << endl;
    }
    
    // 按年龄升序排序
    sort(students.begin(), students.end(), [](const Student& a, const Student& b) {
        return a.age < b.age;
    });
    
    cout << "\n按年龄排序:" << endl;
    for (const auto& s : students) {
        cout << s.name << ": " << s.age << endl;
    }
    
    return 0;
}
```

---

## 结构体嵌套

### 基本嵌套

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Date {
    int year;
    int month;
    int day;
};

struct Student {
    string name;
    int age;
    Date birthday;  // 嵌套结构体
};

int main() {
    // 初始化嵌套结构体
    Student s1 = {
        "Alice",
        20,
        {2003, 5, 15}  // 初始化 birthday
    };
    
    // 访问嵌套成员
    cout << s1.name << endl;
    cout << "Birthday: " << s1.birthday.year << "-" 
         << s1.birthday.month << "-" << s1.birthday.day << endl;
    
    // 修改嵌套成员
    s1.birthday.year = 2004;
    
    return 0;
}
```

### 复杂嵌套

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;

struct Address {
    string street;
    string city;
    string zipCode;
};

struct Contact {
    string phone;
    string email;
};

struct Person {
    string name;
    int age;
    Address address;       // 嵌套结构体1
    Contact contact;       // 嵌套结构体2
};

struct Company {
    string name;
    Person manager;        // 嵌套 Person 结构体
    vector<Person> employees;  // Person 结构体数组
};

int main() {
    Company company = {
        "Tech Corp",
        {
            "Alice",
            35,
            {"123 Main St", "New York", "10001"},
            {"555-1234", "alice@tech.com"}
        },
        {
            {"Bob", 28, {"456 Oak Ave", "Boston", "02101"}, {"555-5678", "bob@tech.com"}},
            {"Charlie", 30, {"789 Elm St", "Chicago", "60601"}, {"555-9012", "charlie@tech.com"}}
        }
    };
    
    // 访问深层嵌套成员
    cout << "Company: " << company.name << endl;
    cout << "Manager: " << company.manager.name << endl;
    cout << "Manager Email: " << company.manager.contact.email << endl;
    cout << "Manager City: " << company.manager.address.city << endl;
    
    cout << "\nEmployees:" << endl;
    for (const auto& emp : company.employees) {
        cout << emp.name << " - " << emp.contact.email << endl;
    }
    
    return 0;
}
```

### 自引用结构体（链表）

```cpp
#include <iostream>
using namespace std;

struct Node {
    int data;
    Node* next;  // 指向下一个节点的指针
    
    // 构造函数
    Node(int val) : data(val), next(nullptr) {}
};

int main() {
    // 创建链表: 1 -> 2 -> 3 -> NULL
    Node* head = new Node(1);
    head->next = new Node(2);
    head->next->next = new Node(3);
    
    // 遍历链表
    Node* current = head;
    while (current != nullptr) {
        cout << current->data << " -> ";
        current = current->next;
    }
    cout << "NULL" << endl;
    
    // 释放内存
    current = head;
    while (current != nullptr) {
        Node* temp = current;
        current = current->next;
        delete temp;
    }
    
    return 0;
}
```

---

## 结构体与类的区别

### 主要区别

```cpp
#include <iostream>
using namespace std;

// 结构体：默认 public
struct MyStruct {
    int x;        // public
    void func() { // public
        cout << "Struct function" << endl;
    }
};

// 类：默认 private
class MyClass {
    int x;        // private
    void func() { // private
        cout << "Class function" << endl;
    }
};

int main() {
    MyStruct s;
    s.x = 10;     // ✅ 可以访问（public）
    s.func();     // ✅ 可以访问（public）
    
    MyClass c;
    // c.x = 10;  // ❌ 错误：x 是 private
    // c.func();  // ❌ 错误：func 是 private
    
    return 0;
}
```

### 详细对比表

| 特性 | struct | class |
|------|--------|-------|
| **默认访问权限** | public | private |
| **默认继承方式** | public | private |
| **用途** | 数据结构、POD类型 | 完整的对象封装 |
| **C兼容性** | 与C兼容（纯数据时） | C++独有 |
| **推荐使用场景** | 简单数据聚合 | 需要封装的复杂对象 |

### 何时使用 struct vs class

```cpp
// ✅ 使用 struct：纯数据容器
struct Point {
    int x;
    int y;
};

struct Color {
    int r, g, b;
};

// ✅ 使用 class：需要封装和行为
class BankAccount {
private:
    double balance;  // 私有数据
    
public:
    void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }
    
    double getBalance() const {
        return balance;
    }
};

// 🤔 两者都可以：简单数据 + 少量方法
struct Rectangle {
    int width;
    int height;
    
    int area() const {
        return width * height;
    }
};
```

### C++中 struct 的完整功能

```cpp
#include <iostream>
#include <string>
using namespace std;

// struct 在 C++ 中也可以有：
struct AdvancedStruct {
    // 1. 私有成员
private:
    int privateData;
    
public:
    // 2. 构造函数
    AdvancedStruct() : privateData(0) {}
    AdvancedStruct(int val) : privateData(val) {}
    
    // 3. 析构函数
    ~AdvancedStruct() {
        cout << "Destructor called" << endl;
    }
    
    // 4. 成员函数
    void display() const {
        cout << "Data: " << privateData << endl;
    }
    
    // 5. 运算符重载
    bool operator==(const AdvancedStruct& other) const {
        return privateData == other.privateData;
    }
    
    // 6. 静态成员
    static int count;
    
    // 7. 友元函数
    friend void accessPrivate(const AdvancedStruct& obj);
};

int AdvancedStruct::count = 0;

void accessPrivate(const AdvancedStruct& obj) {
    cout << "Private data: " << obj.privateData << endl;
}

// 8. 继承
struct Base {
    int x;
};

struct Derived : Base {  // 默认 public 继承
    int y;
};

int main() {
    AdvancedStruct obj(42);
    obj.display();
    
    return 0;
}
```

---

## 高级特性

### 成员函数

```cpp
#include <iostream>
#include <string>
#include <cmath>
using namespace std;

struct Point {
    double x;
    double y;
    
    // 成员函数：计算到原点的距离
    double distanceToOrigin() const {
        return sqrt(x * x + y * y);
    }
    
    // 成员函数：计算到另一点的距离
    double distanceTo(const Point& other) const {
        double dx = x - other.