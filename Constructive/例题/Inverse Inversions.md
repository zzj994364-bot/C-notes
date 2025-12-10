https://cses.fi/problemset/result/15382007/

## 逆向逆序对（Inverse Inversions）

**任务**

你的任务是构造一个由数字 1, 2, …, n 组成的排列，使其恰好有 k 个逆序对。

一个逆序对定义为一对 (a, b)，其中 a < b 且 pₐ > p_b，其中 p_i 表示排列中位置 i 的数字。

**输入**

输入只有一行，包含两个整数 n 和 k。

**输出**

输出一行，包含一个满足条件的排列。任意一个合法解均可。

**限制条件**

- 1 ≤ n ≤ 10⁶
- 0 ≤ k ≤ n(n−1)/2

**示例**

输入：

```
5 4
```

输出：

```
1 5 2 4 3
```











## 解题思路

### **贪心 Greedy：每次尽量让当前最大数字贡献最大逆序对数**

你现在有区间 `[l … r]` 的数字还没放。

如果你把 **r 放在最左边**，它会压住所有比它小的数：
 贡献的逆序对数量 = `r - l`

于是：

- **如果 (r - l) ≤ k**
   → 那就用 r，一次性贡献最大可能逆序对
   → 最优，因为越大的数越适合放前面
- **否则 (r - l) > k**
   → r 贡献太多了，k 不够
   → 那就只能放 l，不贡献逆序对

这就是贪心。

------

### 双指针 Two Pointers

我们用两个指针：

- **l** 指向目前最小的数字
- **r** 指向目前最大的数字

构造的排列从左到右放数字：

- 放 r → r--
- 放 l → l++

整个操作 O(n)。



## 代码实现

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    long long n, k;
    cin >> n >> k;

    vector<long long> ans;
    ans.reserve(n);

    long long l = 1, r = n;

    while (l <= r) {
        long long maxInv = r - l;   // 把 r 放在最左边能贡献的逆序对数

        if (maxInv <= k) {
            // 放 r，贡献 maxInv 个逆序对
            ans.push_back(r);
            k -= maxInv;
            r--;
        } else {
            // 放 l，不贡献逆序对
            ans.push_back(l);
            l++;
        }
    }

    // 输出结果
    for (long long x : ans)
        cout << x << " ";
}
```

