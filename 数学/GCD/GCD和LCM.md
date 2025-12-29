# 最大公约数与最小公倍数 (GCD and LCM)

## 基础

最大公约数（Greatest Common Divisor, GCD）指两个或多个整数共有约数中最大的一个。最小公倍数（Least Common Multiple, LCM）指两个或多个整数公有的倍数中最小的一个。它们是数论中非常基础和重要的概念。

核心关系式：

  * 对于两个正整数 `a` 和 `b`，它们的最大公约数和最小公倍数之间存在一个重要的关系：
    $a \times b = \text{gcd}(a, b) \times \text{lcm}(a, b)$
  * 这个关系式表明，只要我们求出了 `gcd(a, b)`，就可以通过 $O(1)$ 的计算得到 `lcm(a, b)`。因此，核心问题在于如何高效地求解 GCD。

常见应用：

  * **分数化简**: 将分数 $\frac{a}{b}$ 化为最简分数，需要分子分母同时除以 `gcd(a, b)`。
  * **模运算**: 求解模逆元、解线性同余方程等问题常依赖于扩展欧几里得算法，而该算法是 GCD 算法的延伸。
  * **周期性问题**: 求解多个具有不同周期的事件何时会再次同时发生，通常需要计算它们周期的 LCM。
  * **其他数论问题的基础**: GCD 是许多数论算法（如中国剩余定理）的基础构建模块。

时间/空间复杂度：

  * **欧几里得算法 (Euclidean Algorithm)**:
  * **时间复杂度**: $O(\log(\min(a, b)))$。算法的收敛速度非常快。
  * **空间复杂度**: 递归版本为 $O(\log(\min(a, b)))$ （取决于递归栈深度），迭代版本为 $O(1)$。

## 算法的流程

计算最大公约数最高效的算法是**欧几里得算法**，也称辗转相除法。

1.  **输入**: 两个非负整数 `a` 和 `b`。
2.  **基准情况 (Base Case)**: 如果 `b` 等于 0，算法终止，`a` 就是最大公约数。
3.  **递归步骤 (Recursive Step)**: 如果 `b` 不为 0，则利用 `gcd(a, b) = gcd(b, a % b)` 这个核心性质，将问题规模缩小。用 `b` 替换 `a`，用 `a % b` 替换 `b`，然后重复此过程。

## 算法模板

### 模板一：最大公约数 (GCD) - 欧几里得算法

这是求解 GCD 的标准算法。迭代版本在实践中可能因避免函数调用开销而稍快。
`C++17` 的 `<numeric>` 头文件中已包含 `gcd` 函数。

```cpp
/**
 * @brief 欧几里得算法 (递归版本)
 * @param a 第一个非负整数
 * @param b 第二个非负整数
 * @return a 和 b 的最大公约数
 */
long long gcd_recursive(long long a, long long b) {
    // 基准情况：b 为 0 时，a 即为最大公约数
    if (b == 0) {
        return a;
    }
    // 递归步骤：gcd(a, b) = gcd(b, a % b)
    return gcd_recursive(b, a % b);
}

/**
 * @brief 欧几里得算法 (迭代版本)
 * @param a 第一个非负整数
 * @param b 第二个非负整数
 * @return a 和 b 的最大公约数
 */
long long gcd_iterative(long long a, long long b) {
    while (b != 0) {
        long long temp = a % b;
        a = b;
        b = temp;
    }
    return a;
}
```

### 模板二：最小公倍数 (LCM)

利用核心关系式 `a * b = gcd(a, b) * lcm(a, b)` 来求解。

```cpp
/**
 * @brief 求解 a 和 b 的最小公倍数
 * @param a 第一个正整数
 * @param b 第二个正整数
 * @return a 和 b 的最小公倍数
 */
long long lcm(long long a, long long b) {
    // 为了防止 a * b 在计算过程中发生整数溢出，
    // 采用 (a / gcd(a, b)) * b 的形式
    if (a == 0 || b == 0) return 0;
    return (a / gcd_iterative(a, b)) * b;
}
```

### 模板三：扩展欧几里得算法 (Extended Euclidean Algorithm)

该算法用于求解方程 `a*x + b*y = gcd(a, b)` 的一组整数解 `(x, y)`。

```cpp
/**
 * @brief 扩展欧几里得算法
 * @param a 方程的第一个系数
 * @param b 方程的第二个系数
 * @param x 方程解x的引用
 * @param y 方程解y的引用
 * @return a 和 b 的最大公约数
 */
long long extended_gcd(long long a, long long b, long long& x, long long& y) {
    // 基准情况
    if (b == 0) {
        x = 1;
        y = 0;
        return a;
    }
    
    // 递归求解子问题
    long long g = extended_gcd(b, a % b, x, y);
    
    // 利用子问题的解 (x, y) 更新当前问题的解
    long long temp_x = x;
    x = y;
    y = temp_x - (a / b) * y;
    
    return g;
}
```
**裴蜀定理，又称贝祖定理 (Bézout's Identity)**
>设 a, b 是不全为零的整数。那么，对任意整数 x, y，都有 gcd(a, b) | ax + by ；而且，存在整数 x, y, 使得 ax + by = gcd(a, b)成立，其中 x 和 y可以为负或零。

对任意数字 z 进行 +a, -a, +b, -b 的操作，可以构造成ax + by，即等价于对 z 加上或减去 k * gcd(a, b)，其中 k 是任意整数，gcd(a, b) 是 a 和 b 的最大公约数。

### 模板四：自带函数

时间复杂度是 $O(\log N)$

```cpp
#include<iostream>
#include<numeric>

int main() {
    int a = 12;
    int b = 18;
    
    int res = gcd(a, b);
    int res_two = lcm(a, b);
    
    return 0;
}
```
