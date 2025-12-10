# C++ Class 详细笔记

## 目录
1. [基础概念](#基础概念)
2. [类的定义与使用](#类的定义与使用)
3. [访问控制](#访问控制)
4. [构造函数](#构造函数)
5. [析构函数](#析构函数)
6. [成员函数](#成员函数)
7. [静态成员](#静态成员)
8. [友元](#友元)
9. [运算符重载](#运算符重载)
10. [继承](#继承)
11. [多态](#多态)
12. [常用技巧](#常用技巧)

---

## 基础概念

### 什么是类（Class）？
- **类**是用户自定义的数据类型
- 类是**对象的模板/蓝图**
- 类将**数据（属性）**和**操作数据的方法（函数）**封装在一起
- `struct` 是从 **C语言结构体** 演化而来，本来就用来公开存数据的；
- `class` 是为 **面向对象编程（封装、继承、多态）** 设计的，默认就要保护数据。

### 类 vs 结构体
```cpp
// struct 默认 public
struct Point {
    int x, y;  // 默认 public
};

// class 默认 private
class Point {
    int x, y;  // 默认 private
};
```

---

## 类的定义与使用

### 基本语法

```cpp
class ClassName {
private:
    // 私有成员（只能在类内访问）
    int privateData;

protected:
    // 保护成员（类内和派生类可访问）
    int protectedData;

public:
    // 公有成员（任何地方都可访问）
    int publicData;
    
    // 成员函数
    void memberFunction();
};
```

### 完整示例

```cpp
#include <iostream>
#include <string>
using namespace std;

class Student {
private:
    string name;
    int age;
    double gpa;

public:
    // 成员函数
    void setInfo(string n, int a, double g) {
        name = n;
        age = a;
        gpa = g;
    }
    
    void display() {
        cout << "Name: " << name << endl;
        cout << "Age: " << age << endl;
        cout << "GPA: " << gpa << endl;
    }
};

int main() {
    Student s1;  // 创建对象
    s1.setInfo("Alice", 20, 3.8);
    s1.display();
    return 0;
}
```

### 类定义的两种方式

#### 方式1：类内定义
```cpp
class Rectangle {
public:
    int width, height;
    
    // 直接在类内定义
    int area() {
        return width * height;
    }
};
```

#### 方式2：类外定义（推荐）
```cpp
class Rectangle {
public:
    int width, height;
    
    // 类内声明
    int area();
};

// 类外定义，使用作用域解析运算符 ::
int Rectangle::area() {
    return width * height;
}
```

---

## 访问控制

### 三种访问修饰符

| 修饰符 | 类内 | 派生类 | 类外 |
|--------|------|--------|------|
| `private` | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ |

### 示例

```cpp
class Example {
private:
    int a;  // 只能在类内访问

protected:
    int b;  // 类内和派生类可访问

public:
    int c;  // 任何地方都可访问
    
    void test() {
        a = 1;  // ✅ 可以访问
        b = 2;  // ✅ 可以访问
        c = 3;  // ✅ 可以访问
    }
};

int main() {
    Example obj;
    // obj.a = 1;  // ❌ 错误：private
    // obj.b = 2;  // ❌ 错误：protected
    obj.c = 3;     // ✅ 正确：public
}
```

### Getter 和 Setter（访问器和修改器）

```cpp
class BankAccount {
private:
    double balance;  // 私有数据

public:
    // Getter：读取私有数据
    double getBalance() const {
        return balance;
    }
    
    // Setter：修改私有数据（可添加验证）
    void setBalance(double b) {
        if (b >= 0) {
            balance = b;
        } else {
            cout << "Invalid balance!" << endl;
        }
    }
};
```

---

## 构造函数

### 什么是构造函数？
- **构造函数**是创建对象时自动调用的特殊函数
- 函数名与类名相同
- 没有返回类型（连 void 也不写）
- 用于初始化对象

### 默认构造函数

```cpp
class Point {
private:
    int x, y;

public:
    // 默认构造函数（无参数）
    Point() {
        x = 0;
        y = 0;
        cout << "Default constructor called" << endl;
    }
};

int main() {
    Point p;  // 调用默认构造函数
}
```

### 带参数的构造函数

```cpp
class Point {
private:
    int x, y;

public:
    // 带参数的构造函数
    Point(int a, int b) {
        x = a;
        y = b;
    }
    
    void display() {
        cout << "(" << x << ", " << y << ")" << endl;
    }
};

int main() {
    Point p1(10, 20);  // 调用带参构造函数
    p1.display();
}
```

### 初始化列表（推荐方式）

```cpp
class Point {
private:
    int x, y;

public:
    // 使用初始化列表
    Point(int a, int b) : x(a), y(b) {
        // 构造函数体
    }
    
    // const 成员必须用初始化列表
    const int id;
    Point(int a, int b, int i) : x(a), y(b), id(i) {}
};
```

### 拷贝构造函数

```cpp
class Point {
private:
    int x, y;

public:
    Point(int a, int b) : x(a), y(b) {}
    
    // 拷贝构造函数
    Point(const Point& other) {
        x = other.x;
        y = other.y;
        cout << "Copy constructor called" << endl;
    }
};

int main() {
    Point p1(10, 20);
    Point p2 = p1;  // 调用拷贝构造函数
    Point p3(p1);   // 调用拷贝构造函数
}
```

### 委托构造函数（C++11）

```cpp
class Point {
private:
    int x, y;

public:
    Point(int a, int b) : x(a), y(b) {}
    
    // 委托给另一个构造函数
    Point() : Point(0, 0) {}
    Point(int a) : Point(a, a) {}
};
```

### 默认参数构造函数

```cpp
class Point {
private:
    int x, y;

public:
    // 使用默认参数
    Point(int a = 0, int b = 0) : x(a), y(b) {}
};

int main() {
    Point p1;           // (0, 0)
    Point p2(10);       // (10, 0)
    Point p3(10, 20);   // (10, 20)
}
```

---

## 析构函数

### 什么是析构函数？
- 对象销毁时自动调用
- 函数名是 `~类名`
- 没有参数，没有返回类型
- 用于清理资源（释放内存、关闭文件等）

### 基本示例

```cpp
class MyClass {
public:
    MyClass() {
        cout << "Constructor called" << endl;
    }
    
    ~MyClass() {
        cout << "Destructor called" << endl;
    }
};

int main() {
    MyClass obj;
    // 程序结束时自动调用析构函数
}
```

### 动态内存管理

```cpp
class DynamicArray {
private:
    int* arr;
    int size;

public:
    DynamicArray(int s) : size(s) {
        arr = new int[size];
        cout << "Memory allocated" << endl;
    }
    
    ~DynamicArray() {
        delete[] arr;
        cout << "Memory freed" << endl;
    }
};

int main() {
    DynamicArray da(10);
    // 对象销毁时自动释放内存
}
```

---

## 成员函数

### 常量成员函数

```cpp
class Point {
private:
    int x, y;

public:
    Point(int a, int b) : x(a), y(b) {}
    
    // const 成员函数：不修改对象
    int getX() const {
        return x;
    }
    
    void display() const {
        cout << "(" << x << ", " << y << ")" << endl;
        // x = 10;  // ❌ 错误：const 函数不能修改成员
    }
    
    // 非 const 成员函数
    void setX(int a) {
        x = a;
    }
};

int main() {
    const Point p(10, 20);
    p.display();  // ✅ 可以调用 const 函数
    // p.setX(30);  // ❌ 错误：const 对象不能调用非 const 函数
}
```

### 内联函数

```cpp
class Point {
private:
    int x, y;

public:
    // 方式1：类内定义自动成为内联函数
    int getX() const {
        return x;
    }
    
    // 方式2：显式声明 inline
    inline int getY() const;
};

inline int Point::getY() const {
    return y;
}
```

### this 指针

```cpp
class Point {
private:
    int x, y;

public:
    Point(int x, int y) {
        // 使用 this 区分成员变量和参数
        this->x = x;
        this->y = y;
    }
    
    // 返回对象自身的引用
    Point& setX(int x) {
        this->x = x;
        return *this;  // 返回当前对象
    }
    
    Point& setY(int y) {
        this->y = y;
        return *this;
    }
};

int main() {
    Point p(0, 0);
    // 链式调用
    p.setX(10).setY(20);
}
```

---

## 静态成员

### 静态成员变量

```cpp
class Counter {
private:
    static int count;  // 静态成员变量声明

public:
    Counter() {
        count++;
    }
    
    static int getCount() {
        return count;
    }
};

// 静态成员变量必须在类外初始化
int Counter::count = 0;

int main() {
    Counter c1, c2, c3;
    cout << Counter::getCount() << endl;  // 输出: 3
}
```

### 静态成员函数

```cpp
class MathUtils {
public:
    // 静态成员函数
    static int add(int a, int b) {
        return a + b;
    }
    
    static double PI;
};

double MathUtils::PI = 3.14159;

int main() {
    // 通过类名调用，不需要创建对象
    int sum = MathUtils::add(5, 3);
    cout << MathUtils::PI << endl;
}
```

### 静态成员的特点

- **共享**：所有对象共享同一个静态成员
- **独立**：静态成员独立于对象存在
- **限制**：静态成员函数只能访问静态成员

```cpp
class Example {
private:
    int x;
    static int y;

public:
    static void staticFunc() {
        // y = 10;     // ✅ 可以访问静态成员
        // x = 20;     // ❌ 错误：不能访问非静态成员
    }
    
    void normalFunc() {
        x = 20;  // ✅ 可以访问非静态成员
        y = 10;  // ✅ 也可以访问静态成员
    }
};
```

---

## 友元

### 友元函数

```cpp
class Box {
private:
    int width;

public:
    Box(int w) : width(w) {}
    
    // 声明友元函数
    friend void printWidth(Box& b);
};

// 友元函数定义（可以访问私有成员）
void printWidth(Box& b) {
    cout << "Width: " << b.width << endl;
}

int main() {
    Box box(10);
    printWidth(box);
}
```

### 友元类

```cpp
class Box {
private:
    int width;

public:
    Box(int w) : width(w) {}
    
    // 声明 Printer 为友元类
    friend class Printer;
};

class Printer {
public:
    void print(Box& b) {
        // 可以访问 Box 的私有成员
        cout << "Width: " << b.width << endl;
    }
};
```

### 友元的特点

- 友元关系是**单向的**（A 是 B 的友元，B 不一定是 A 的友元）
- 友元关系**不能继承**
- 友元关系**不能传递**
- **破坏封装性**，应谨慎使用

---

## 运算符重载

### 基本语法

```cpp
返回类型 operator运算符(参数列表)
```

### 成员函数重载

```cpp
class Complex {
private:
    double real, imag;

public:
    Complex(double r = 0, double i = 0) : real(r), imag(i) {}
    
    // 重载 + 运算符
    Complex operator+(const Complex& other) const {
        return Complex(real + other.real, imag + other.imag);
    }
    
    // 重载 - 运算符
    Complex operator-(const Complex& other) const {
        return Complex(real - other.real, imag - other.imag);
    }
    
    // 重载 == 运算符
    bool operator==(const Complex& other) const {
        return real == other.real && imag == other.imag;
    }
    
    void display() const {
        cout << real << " + " << imag << "i" << endl;
    }
};

int main() {
    Complex c1(3, 4), c2(1, 2);
    Complex c3 = c1 + c2;  // 调用 operator+
    c3.display();
}
```

### 友元函数重载

```cpp
class Complex {
private:
    double real, imag;

public:
    Complex(double r = 0, double i = 0) : real(r), imag(i) {}
    
    // 声明友元
    friend Complex operator+(const Complex& a, const Complex& b);
    friend ostream& operator<<(ostream& os, const Complex& c);
};

// 友元函数定义
Complex operator+(const Complex& a, const Complex& b) {
    return Complex(a.real + b.real, a.imag + b.imag);
}

// 重载 << 运算符（必须用友元）
ostream& operator<<(ostream& os, const Complex& c) {
    os << c.real << " + " << c.imag << "i";
    return os;
}

int main() {
    Complex c1(3, 4), c2(1, 2);
    Complex c3 = c1 + c2;
    cout << c3 << endl;  // 直接输出
}
```

### 重载 [] 下标运算符

```cpp
class Array {
private:
    int arr[10];

public:
    // 重载 [] 运算符
    int& operator[](int index) {
        return arr[index];
    }
    
    // const 版本
    const int& operator[](int index) const {
        return arr[index];
    }
};

int main() {
    Array a;
    a[0] = 10;
    cout << a[0] << endl;
}
```

### 重载 ++ 和 -- 运算符

```cpp
class Counter {
private:
    int count;

public:
    Counter(int c = 0) : count(c) {}
    
    // 前置 ++
    Counter& operator++() {
        count++;
        return *this;
    }
    
    // 后置 ++（参数 int 用于区分）
    Counter operator++(int) {
        Counter temp = *this;
        count++;
        return temp;
    }
    
    int getCount() const { return count; }
};

int main() {
    Counter c(5);
    ++c;     // 前置：先加后用
    c++;     // 后置：先用后加
}
```

### 不能重载的运算符

- `::`（作用域解析）
- `.`（成员访问）
- `.*`（成员指针访问）
- `?:`（三元运算符）
- `sizeof`

---

## 继承

### 基本语法

```cpp
class 派生类名 : 访问修饰符 基类名 {
    // 派生类成员
};
```

### 公有继承（public）

```cpp
class Animal {
protected:
    string name;

public:
    Animal(string n) : name(n) {}
    
    void eat() {
        cout << name << " is eating" << endl;
    }
};

class Dog : public Animal {
private:
    string breed;

public:
    Dog(string n, string b) : Animal(n), breed(b) {}
    
    void bark() {
        cout << name << " is barking" << endl;
    }
};

int main() {
    Dog dog("Buddy", "Golden Retriever");
    dog.eat();   // 继承自 Animal
    dog.bark();  // Dog 自己的方法
}
```

### 三种继承方式

| 基类成员 | public 继承 | protected 继承 | private 继承 |
|----------|-------------|----------------|--------------|
| public | public | protected | private |
| protected | protected | protected | private |
| private | 不可访问 | 不可访问 | 不可访问 |

```cpp
class Base {
public:
    int pub;
protected:
    int pro;
private:
    int pri;
};

class Derived1 : public Base {
    // pub → public
    // pro → protected
    // pri → 不可访问
};

class Derived2 : protected Base {
    // pub → protected
    // pro → protected
    // pri → 不可访问
};

class Derived3 : private Base {
    // pub → private
    // pro → private
    // pri → 不可访问
};
```

### 构造函数和析构函数的调用顺序

```cpp
class Base {
public:
    Base() {
        cout << "Base constructor" << endl;
    }
    
    ~Base() {
        cout << "Base destructor" << endl;
    }
};

class Derived : public Base {
public:
    Derived() {
        cout << "Derived constructor" << endl;
    }
    
    ~Derived() {
        cout << "Derived destructor" << endl;
    }
};

int main() {
    Derived d;
    // 输出顺序：
    // Base constructor
    // Derived constructor
    // Derived destructor
    // Base destructor
}
```

### 多重继承

```cpp
class A {
public:
    void funcA() { cout << "A" << endl; }
};

class B {
public:
    void funcB() { cout << "B" << endl; }
};

class C : public A, public B {
public:
    void funcC() { cout << "C" << endl; }
};

int main() {
    C obj;
    obj.funcA();  // 来自 A
    obj.funcB();  // 来自 B
    obj.funcC();  // 自己的
}
```

---

## 多态

### 什么是多态？
- **多态**：同一个接口，不同的实现
- **虚函数**：用 `virtual` 关键字声明的函数
- **动态绑定**：运行时决定调用哪个函数

### 虚函数

```cpp
class Animal {
public:
    // 虚函数
    virtual void makeSound() {
        cout << "Animal sound" << endl;
    }
};

class Dog : public Animal {
public:
    // 重写虚函数
    void makeSound() override {
        cout << "Woof!" << endl;
    }
};

class Cat : public Animal {
public:
    void makeSound() override {
        cout << "Meow!" << endl;
    }
};

int main() {
    Animal* animals[3];
    animals[0] = new Animal();
    animals[1] = new Dog();
    animals[2] = new Cat();
    
    for (int i = 0; i < 3; i++) {
        animals[i]->makeSound();  // 动态绑定
    }
    
    // 输出：
    // Animal sound
    // Woof!
    // Meow!
    
    // 释放内存
    for (int i = 0; i < 3; i++) {
        delete animals[i];
    }
}
```

### override 关键字（C++11）

```cpp
class Base {
public:
    virtual void func() {}
};

class Derived : public Base {
public:
    // override 确保正确重写
    void func() override {}
    
    // void func(int) override;  // ❌ 错误：Base 中没有这个函数
};
```

### final 关键字（C++11）

```cpp
class Base {
public:
    virtual void func() {}
};

class Derived : public Base {
public:
    // final 禁止进一步重写
    void func() override final {}
};

class MoreDerived : public Derived {
public:
    // void func() override {}  // ❌ 错误：func 是 final
};

// 也可以用于类
class FinalClass final {
    // ...
};

// class Derived : public FinalClass {};  // ❌ 错误：不能继承 final 类
```

### 纯虚函数和抽象类

```cpp
class Shape {
public:
    // 纯虚函数
    virtual double area() = 0;
    virtual void draw() = 0;
    
    virtual ~Shape() {}  // 虚析构函数
};

class Circle : public Shape {
private:
    double radius;

public:
    Circle(double r) : radius(r) {}
    
    // 必须实现纯虚函数
    double area() override {
        return 3.14159 * radius * radius;
    }
    
    void draw() override {
        cout << "Drawing circle" << endl;
    }
};

int main() {
    // Shape s;  // ❌ 错误：不能实例化抽象类
    
    Shape* shape = new Circle(5.0);
    cout << "Area: " << shape->area() << endl;
    shape->draw();
    
    delete shape;
}
```

### 虚析构函数

```cpp
class Base {
public:
    Base() {
        cout << "Base constructor" << endl;
    }
    
    // 虚析构函数（重要！）
    virtual ~Base() {
        cout << "Base destructor" << endl;
    }
};

class Derived : public Base {
public:
    Derived() {
        cout << "Derived constructor" << endl;
    }
    
    ~Derived() {
        cout << "Derived destructor" << endl;
    }
};

int main() {
    Base* ptr = new Derived();
    delete ptr;  // 正确调用 Derived 和 Base 的析构函数
    
    // 如果 Base 的析构函数不是虚函数，
    // 只会调用 Base 的析构函数，导致内存泄漏
}
```

---

## 常用技巧

### 单例模式

```cpp
class Singleton {
private:
    static Singleton* instance;
    
    // 私有构造函数
    Singleton() {}

public:
    // 禁止拷贝
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;
    
    static Singleton* getInstance() {
        if (instance == nullptr) {
            instance = new Singleton();
        }
        return instance;
    }
};

Singleton* Singleton::instance = nullptr;

int main() {
    Singleton* s1 = Singleton::getInstance();
    Singleton* s2 = Singleton::getInstance();
    // s1 和 s2 指向同一个对象
}
```

### 对象计数器

```cpp
class Counter {
private:
    static int count;

public:
    Counter() {
        count++;
    }
    
    Counter(const Counter&) {
        count++;
    }
    
    ~Counter() {
        count--;
    }
    
    static int getCount() {
        return count;
    }
};

int Counter::count = 0;

int main() {
    Counter c1;
    {
        Counter c2;
        Counter c3 = c1;
        cout << Counter::getCount() << endl;  // 3
    }
    cout << Counter::getCount() << endl;  // 1
}
```

### 链式调用

```cpp
class Calculator {
private:
    int value;

public:
    Calculator(int v = 0) : value(v) {}
    
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
    
    int result() const {
        return value;
    }
};

int main() {
    Calculator calc;
    int res = calc.add(10).subtract(3).multiply(2).result();
    cout << res << endl;  // 14
}
```

### 深拷贝 vs 浅拷贝

```cpp
class DeepCopy {
private:
    int* data;

public:
    DeepCopy(int val) {
        data = new int(val);
    }
    
    // 深拷贝构造函数
    DeepCopy(const DeepCopy& other) {
        data = new int(*other.data);  // 分配新内存
    }
    
    // 深拷贝赋值运算符
    DeepCopy& operator=(const DeepCopy& other) {
        if (this != &other) {
            delete data;
            data = new int(*other.data);
        }
        return *this;
    }
    
    ~DeepCopy() {
        delete data;
    }
};
```

### 移动语义（C++11）

```cpp
class MoveExample {
private:
    int* data;

public:
    MoveExample(int val) {
        data = new int(val);
    }
    
    // 移动构造函数
    MoveExample(MoveExample&& other) noexcept {
        data = other.data;
        other.data = nullptr;
    }
    
    // 移动赋值运算符
    MoveExample& operator=(MoveExample&& other) noexcept {
        if (this != &other) {
            delete data;
            data = other.data;
            other.data = nullptr;
        }
        return *this;
    }
    
    ~MoveExample() {
        delete data;
    }
};
```

---

## 总结

### 核心概念
1. **封装**：将数据和方法封装在类中
2. **继承**：代码复用和扩展
3. **多态**：接口统一，实现多样

### 最佳实践
- ✅ 使用初始化列表初始化成员
- ✅ 虚析构函数用于基类
- ✅ const 修饰不改变对象的函数
- ✅ 使用 override 和 final 关键字
- ✅ 遵循"三五法则"（拷贝、赋值、析构）
- ✅ 私有数据，公有接口
- ⚠️ 谨慎使用友元
- ⚠️ 避免多重继承的复杂性

### 常见错误
- ❌ 忘记初始化成员变量
- ❌ 浅拷贝导致的内存问题
- ❌ 基类析构函数不是虚函数
- ❌ 在构造/析构函数中调用虚函数
- ❌ 返回局部对象的