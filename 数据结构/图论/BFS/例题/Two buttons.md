https://codeforces.com/problemset/problem/520/B

**B. 两个按钮**

**时间限制**：2 秒
 **内存限制**：256 MB

瓦西里发现了一个奇怪的装置。装置的面板上有：一个红色按钮、一个蓝色按钮，以及一个显示正整数的屏幕。
 按下红色按钮后，屏幕上的数字会乘以 2。
 按下蓝色按钮后，屏幕上的数字会减去 1。
 如果在某个时刻数字不再是正数，装置就会坏掉。
 屏幕可以显示任意大的数字。最开始，屏幕上显示数字 n。

鲍勃想让屏幕上显示数字 m。为了达到这个结果，他最少需要按多少次按钮？

------

**输入**
 输入只有一行，包含两个不同的整数 n 和 m（1 ≤ n, m ≤ 10⁴），用空格分隔。

**输出**
 输出一个整数，表示从 n 变到 m 所需按按钮的最少次数。

------

**示例**

输入

```
4 6
```

输出

```
2
```

输入

```
10 1
```

输出

```
9
```

------

**说明**
 在第一个示例中，需要先按一次蓝色按钮，再按一次红色按钮。
 在第二个示例中，不需要使用翻倍操作，只需按蓝色按钮九次。



























































## 解题思路

**状态空间搜索**模板题



## 代码实现

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 20001;
int main() {
    int n, m;
    cin >> n >> m;


    //状态空间搜索
    queue<int> q;
    q.push(n);
    vector<int> dist(MAXN, -1);
    dist[n] = 0;
    while (!q.empty()) {
        int cur = q.front();
        q.pop();

        if (cur == m) {
            cout << dist[cur] << endl;
            return 0;
        }

        int next_min = cur - 1;
        if (next_min > 0 && dist[next_min] == -1) {
            q.push(next_min);
            dist[next_min] = dist[cur] + 1;
        }
        int next_mul = cur * 2;
        if (next_mul < MAXN && dist[next_mul] == -1) {
            q.push(next_mul);
            dist[next_mul] = dist[cur] + 1;
        }
    }

    cout << -1 << endl;
    return 0;
}
```



