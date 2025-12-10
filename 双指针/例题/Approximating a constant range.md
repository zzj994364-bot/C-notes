https://codeforces.com/contest/602/problem/B

## **Approximating a Constant Range**

在大学的一门实践课中，Xellos 曾经需要测量一种效应的强度，它会缓慢接近平衡状态。一个判断平衡强度的好方法，是从序列中选择一段足够长、看起来尽量恒定的连续数据点，然后取它们的平均值。当然，在实际数据规模下，这并不是什么难题——但既然如此，何不把它做成一个编程竞赛题呢？

给定一个包含 n 个数据点的序列 a₁, ..., aₙ。相邻的数据之间不会出现很大的跳变——对每个 1 ≤ i < n，都保证 |aᵢ₊₁ − aᵢ| ≤ 1。

如果某一段区间 [l, r] 中的最大值与最小值之差不超过 1，则称该区间为“几乎恒定”的。形式化地说，令该区间内数据的最大值为 M，最小值为 m；若 M − m ≤ 1，则 [l, r] 是一个几乎恒定区间。

请找出最长的几乎恒定区间的长度。

------

### **输入**

第一行包含一个整数 n (2 ≤ n ≤ 100000) —— 数据点个数。

第二行包含 n 个整数 a₁, a₂, ..., aₙ (1 ≤ aᵢ ≤ 100000)。

### **输出**

输出一个整数 —— 序列中最长的几乎恒定区间的长度。

------

### **例子**

**输入**

```
5
1 2 3 3 2
```

**输出**

```
4
```

**输入**

```
11
5 4 5 5 6 7 8 8 8 7 6
```

**输出**

```
5
```

**说明**

在第一个样例中，最长的几乎恒定区间是 [2, 5]；其长度为 4。

在第二个样例中，有三个长度为 4 的几乎恒定区间：[1, 4], [6, 9], [7, 10]；唯一长度为 5 的几乎恒定区间是 [6, 10]。

























## 解题思路

**使用map<int, int>**：自动按键排序，方便获取最大最小值

- `count.begin()->first`：获取最小值
- `count.rbegin()->first`：获取最大值

**滑动窗口逻辑**：

- 右指针扩展窗口，将元素加入map

- 当max-min > 1时，左指针收缩窗口

- 删除计数为0的键，保持map只包含窗口内的值

  

## 代码实现

```cpp
#include <iostream>
#include <map>
#include <algorithm>
using namespace std;

int main() {
    int n;
    cin >> n;
    
    int a[100005];
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }
    
    int left = 0;
    int max_length = 0;
    map<int, int> count; // 记录当前窗口内每个值出现的次数
    
    for (int right = 0; right < n; right++) {
        // 将右端点的值加入窗口
        count[a[right]]++;
        
        // 当窗口内最大值-最小值 > 1时，收缩左边界
        while (count.rbegin()->first - count.begin()->first > 1) {
            count[a[left]]--;
            if (count[a[left]] == 0) {
                count.erase(a[left]);
            }
            left++;
        }
        
        // 更新最大长度
        max_length = max(max_length, right - left + 1);
    }
    
    cout << max_length << endl;
    
    return 0;
}
```

