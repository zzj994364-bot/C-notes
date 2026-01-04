https://codeforces.com/group/MWSDmqGsZm/contest/219158/problem/Z

## Z. Hard Compare

time limit per test

1 second

memory limit per test

256 megabytes

Given **4** numbers A,B,C and D. If AB > CD print "**YES**" otherwise, print "**NO**".

Input

Only one line containing **4** numbers A,B,C and D (1≤A,C≤107) , (1≤B,D≤1012)

Output

Print "**YES**" or "**NO**" according to the problem above.

Examples

Input

Copy

```
3 2 5 4
```

Output

Copy

```
NO
```

Input

Copy

```
5 2 4 2
```

Output

Copy

```
YES
```

Input

Copy

```
5 2 5 2
```

Output

Copy

```
NO
```

Note

**First Example** :

32 = 9 and 54 = 625 then **9 < 625** so the answer is **NO**.

**Second Example** :

52 = 25 and 42 = 16 then **25 > 16** so the answer is **YES**.

**Third Example** :

52 = 25 and 52 = 25 then **25 = 25** so the answer is **NO**.

























## 解题思路

**思考**

- 题目给出的数据范围进行模拟数值呈指数级复合增长，**远超 `long long` 的存储限制**

- 利用对数函数的单调性，将幂运算转化为乘法运算：

  

  $$a^b > c^d \iff \ln(a^b) > \ln(c^d) \iff b \cdot \ln(a) > d \cdot \ln(c)$$

**实现难点**

- 计算机存储 `double` 或 `long double` 时存在舍入误差。比较两个浮点数大小时，不能直接使用 `>` 或 `==`
  - **精度阈值（Epsilon）**：定义一个极小值（如 `1e-15`）
  - **判断逻辑**：若 `res1 - res2 > eps`，则判定 `res1` 大于 `res2`

## 代码实现

```cpp
#include <iostream>
#include <cmath>
using namespace std;

int main() {
    long long a, b, c, d;
    cin >> a >> b >> c >> d;
    double res1 = b * log(a);
    double res2 = d * log(c);
    if (res1 - res2 > 1e-9) cout << "YES" << endl;
    else cout << "NO" << endl;

    return 0;
}
```

