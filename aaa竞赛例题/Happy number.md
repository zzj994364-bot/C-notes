https://atcoder.jp/contests/abc439/tasks/abc439_b

## **B - Happy Number** ![img](https://img.atcoder.jp/assets/top/img/flag-lang/ja.png) / ![img](https://img.atcoder.jp/assets/top/img/flag-lang/en.png)

------

Time Limit: 2 sec / Memory Limit: 1024 MiB

Score : 200 points

### Problem Statement

You are given a positive integer *N*. Determine whether *N* is a happy number.

A happy number is a non-negative integer that becomes 1 after repeating the following operation a finite number of times:

- Replace it with the integer obtained by taking the sum of the squares of the digits in its decimal representation.
  - For example, performing this operation once on 2026 replaces it with 22+02+22+62=4+0+4+36=44.

For examples of happy numbers, refer to the explanations of sample inputs and outputs.

### Constraints

- *N* is an integer between 1 and 2026, inclusive.

------

### Input

The input is given from Standard Input in the following format:

```
N
```

### Output

If *N* is a happy number, output `Yes`; otherwise, output `No`.

------

### Sample Input 1

Copy

```
2026
```

### Sample Output 1

Copy

```
Yes
```

2026 is a happy number.

- The digits of 2026 in decimal representation are 2,0,2,6, and taking the sum of their squares gives 22+02+22+62=4+0+4+36=44.
- The digits of 44 in decimal representation are 4,4, and taking the sum of their squares gives 42+42=16+16=32.
- The digits of 32 in decimal representation are 3,2, and taking the sum of their squares gives 32+22=9+4=13.
- The digits of 13 in decimal representation are 1,3, and taking the sum of their squares gives 12+32=1+9=10.
- The digits of 10 in decimal representation are 1,0, and taking the sum of their squares gives 12+02=1+0=1.

2026 is a happy number because it became 1 after repeating the operation of replacing itself with the integer obtained by taking the sum of the squares of the digits in its decimal representation five times.

------

### Sample Input 2

Copy

```
439
```

### Sample Output 2

Copy

```
No
```

439 is not a happy number.

Repeating the operation makes it 439→106→37→58→89→145→42→20→4→16→37→⋯, and it can be proved that no matter how many times the operation is repeated from here, it will not become 1.

------

### Sample Input 3

Copy

```
440
```

### Sample Output 3

Copy

```
Yes
```

440 is a happy number.



































## 解题思路

**思考**

- 第一眼无规律，进行模拟
- 数据范围小且计算结果有重复
- 不断重复步骤直至结果为 **1** 或 有重复

## 代码实现

```cpp
#include <bits/stdc++.h>
using namespace std;

int op(int& n) {
    int temp = 0;
    while (n > 0) {
        temp += (n % 10) * (n % 10);
        n /= 10;
    }
    n = temp;
    return temp;
}

int main() {
    set<int> s;
    int n;
    cin >> n;

    while (op(n) != 1) {
        if (s.count(op(n))) {
            cout << "No" << endl;
            return 0;
        }
        s.insert(op(n));
    }
    cout << "Yes" << endl;

    return 0;
}
```

