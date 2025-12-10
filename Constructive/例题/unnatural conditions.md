https://codeforces.com/contest/1028/problem/B

## Unnatural Conditions

设 $s(x)$ 为正整数 $x$ 的十进制表示下各位数字之和。给定两个整数 $n$ 和 $m$，找到 **两个正整数 $a$ 和 $b$**，使得：
$$
s(a) \ge n, \quad s(b) \ge n, \quad s(a+b) \le m
$$

------

### 输入

- 输入包含一行，两个整数 $n$ 和 $m$ ($1 \le n, m \le 1129$)

------

### 输出

- 输出两行：
  - 第一行：正整数 $a$ 的十进制表示
  - 第二行：正整数 $b$ 的十进制表示
- 要求：
  - 两个数 **不能有前导零**
  - 数字长度 **不超过 2230**







## 代码实现

```cpp
#include <iostream>
using namespace std;

int main () {
      //在题目允许的范围内构造最大的s(a)和s(b)，同时控制s(a + b)一直等于1
      int len = 2230;
      cout << string(len, '5') << "\n";
      cout << (string(len - 1, '4') + '5') << "\n";
      return 0;
}
```

