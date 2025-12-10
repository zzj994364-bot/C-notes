https://cses.fi/problemset/task/1619/

## Restaurant Customers

你被给出了 **n 位顾客**在餐厅的**到达时间**和**离开时间**。

你的任务是：
 **在任意时刻，餐厅中同时存在的顾客数量最多是多少？**

### 输入格式

第一行给出一个整数 **n**：顾客数量。

接下来有 **n 行**，每行包含两个整数 **a, b**：

- **a** = 该顾客进入餐厅的时间
- **b** = 该顾客离开餐厅的时间

你可以假设：**所有到达和离开时间都互不相同**（不会出现两个顾客同时在同一毫秒到达或离开）。

### 输出格式

输出一个整数：
 **任意时间点在餐厅中的最大顾客数。**

### 数据范围

- $1 \le n \le 2 \cdot 10^5$
- $1 \le a < b \le 10^9$

### 示例

输入：

```
3
5 8
2 4
3 9
```

输出：

```
2
```

解释：

- 2~4 时刻只有第二个顾客：1 人
- 3~4 时刻第 2 和第 3 个顾客重叠：2 人
- 5~8 时刻第 1 和第 3 个顾客重叠：2 人
   最高同时人数是 **2**















## 解题思路

**事件排序 + 扫描线(sweep line)**

- 只需存所有事件点（最多 2n = 4e5 个）

- 按时间排序

- 扫描一遍统计最大同时在线人数
## 代码实现

  ```cpp
  #include <iostream>
  #include <vector>
  #include <algorithm>
  using namespace std;
  
  int main () {
      int n;
      cin >> n;
      vector<pair<long long, int>> v;
      v.reserve(n * 2); // 预留空间，存每个区间的“事件”：开始和结束各一个
  
      for (int i = 0; i < n; i++) {
          long long a, b;
          cin >> a >> b;
          v.push_back({a, +1}); // 区间开始事件，到达时 +1
          v.push_back({b, -1}); // 区间结束事件，离开时 -1
      }
  
      sort(v.begin(), v.end());// 按时间从小到大排序，保证事件按顺序处理
      
      long long cur = 0, best = 0; // cur = 当前在场数量，best = 最大同时在场数量
      for (int i = 0; i < n * 2; i++) {
          cur+= v[i].second;   // 遇到到达 +1，离开 -1，累加当前在场数量
          best = max(best, cur);
      }
  
      cout << best << endl;
  
      return 0;
  }
  ```

