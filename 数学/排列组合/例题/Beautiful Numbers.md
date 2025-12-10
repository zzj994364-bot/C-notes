https://codeforces.com/contest/300/problem/C

## **Beautiful Numbers**

Vitaly 是一个非常奇怪的人。他有两个最喜欢的数字 **a** 和 **b**。如果一个正整数的十进制表示只包含数字 **a** 和 **b**，Vitaly 就称它为 **good 数**。如果一个 good 数的**各位数字之和**也是一个 good 数，那么它就是 **excellent 数**。

例如，假设 Vitaly 最喜欢的数字是 **1** 和 **3**，那么数字 **12** 不是 good 数，而数字 **13** 和 **311** 是 good 数。同时，数字 **111** 是 excellent 数，而 **11** 不是。

现在 Vitaly 想知道，长度**恰好为 n** 的 excellent 数有多少个。因为结果可能非常大，他让你求出这个数量 **模 1000000007 (10^9 + 7)** 的余数。

一个数字的长度是其十进制表示的位数，不允许有前导零。

------

### **输入**

第一行包含三个整数：**a, b, n**
 (1 ≤ a < b ≤ 9，1 ≤ n ≤ 10^6)

------

### **输出**

输出一个整数——excellent 数的数量（取模 1000000007）。

------

### **样例**

输入：

```
1 3 3
```

输出：

```
1
```

输入：

```
2 3 10
```

输出：

```
165
```













## 解题思路

样例 2（`a=2, b=3, n=10`）：

1. 长度为 10 的数，每位只能是 2 或 3。设其中有 $k$ 个 2，$10-k$ 个 3。
2. 数字各位之和为：$S = k\cdot2 + (10-k)\cdot3 = 30 - k.$
3. 要成为 **excellent**，这个和 $S$ 的十进制表示只能含 2 和 3。因为 $k\in[0,10]$，所以 $S$ 落在 $[20,30]$。在这个区间内只有满足条件的数是 **22** 和 **23**。
   - 若 $S=22$，则 $30-k=22\Rightarrow k=8$。
   - 若 $S=23$，则 $30-k=23\Rightarrow k=7$。
4. 对应每个 $k$，长度为 10 的数有 $\binom{10}{k}$ 种（从 10 位里选出 k 位放 2，其余放 3）。所以总数是

$$
\binom{10}{8}+\binom{10}{7}.
$$

计算：
 $\binom{10}{8}=\binom{10}{2}=\dfrac{10\cdot9}{2}=45$。
 $\binom{10}{7}=\binom{10}{3}=\dfrac{10\cdot9\cdot8}{3\cdot2\cdot1}=120$。

5. 相加得 $45+120=165$。

## 代码实现

```cpp
#include <iostream>
#include <vector>
using namespace std;

const int MAXN = 1000000;
const int MOD = 1000000007;

long long fac[MAXN + 1], invfac[MAXN + 1];

long long power(long long base, long long exp, long long mod) {
    long long result = 1;
    base %= mod;
    while (exp > 0) {
        if (exp & 1) {
            result = (result * base) % mod;
        }
        exp >>= 1;
        base = (base * base) % mod;
    }
    return result;
}

int main() {
        //阶乘预处理
        fac[0] = 1;
        for (int i = 1; i <= MAXN; i++) fac[i] = fac[i - 1] * i % MOD;
        invfac[MAXN] = power(fac[MAXN], MOD - 2, MOD);
        for (int i = MAXN - 1; i >= 0; i--) invfac[i] = invfac[i + 1] * (i + 1) % MOD;

        int a, b, n;
        cin >> a >> b >> n;

        vector<long long> v;
        for (int k = 0; k <= n; k++) {
            int sum = (a - b) * k + n * b; //sum的范围
            
            //判断sum是否合法
            bool ok = true;
            while (sum > 0) {
                int temp = sum % 10;
                sum /= 10;
                if (temp != a && temp != b) {
                    ok = false;
                    break;
                }
            }
            
            if (ok) v.push_back(k); //储存合法的k
        }

        //求出C(n, k)
        long long res = 0;
        for (int i = 0; i < v.size(); i++) {
            res = (res + fac[n] * invfac[v[i]] % MOD * invfac[n - v[i]] % MOD) % MOD;
        }

        cout << res << endl;

        return 0;
}
```

