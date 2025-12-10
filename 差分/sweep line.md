# Sweep Line（扫描线）

## 适用场景

- 处理 **区间重叠**、**同时发生事件**、**矩形/区间覆盖问题**
- 求：
  - 同时发生的最大事件数（如客户、会议、飞机、火车）
  - 区间总覆盖长度 / 面积
  - 事件动态变化统计（最大值 / 累加值）

------

## 核心思路

1. **将每个区间/事件拆成两类事件：**

   对区间 `[l, r]`：

   ```
   (l, +1)   // 开始事件
   (r, -1)   // 结束事件
   ```

   - `+1` 表示“新增/进入”
   - `-1` 表示“减少/离开”

2. **把所有事件存入数组，并按时间排序**

3. **线性扫描事件数组**

   - 维护一个累加量 `current`：

     ```
     current += delta
     max_value = max(max_value, current)
     ```

   - 扫描结束后 `max_value` 就是答案。

4. **注意：**

   - 相同时间的事件顺序可能影响统计：
     - 先减后加：保证离开先于到达
     - 或先加后减：保证到达先于离开
   - 对整数区间通常不会出错，但涉及面积/长度时要注意。

------

## 基本 C++ 模板

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n;
    cin >> n;
    vector<pair<long long, int>> events; // {time, delta}

    for (int i = 0; i < n; i++) {
        long long a, b;
        cin >> a >> b;
        events.push_back({a, +1});   // 到达
        events.push_back({b, -1});   // 离开
    }

    // 按时间排序
    sort(events.begin(), events.end());

    int current = 0;
    int max_count = 0;

    for (auto &[time, delta] : events) {
        current += delta;
        max_count = max(max_count, current);
    }

    cout << max_count << "\n";
    return 0;
}
```

------

## 拓展用法

### 1 求区间总覆盖长度

- 用类似方法，但记录 `prev_time`：

```cpp
long long covered = 0;
long long prev_time = events[0].first;
int current = 0;

for (auto &[time, delta] : events) {
    if (current > 0) {
        covered += (time - prev_time);
    }
    current += delta;
    prev_time = time;
}
```

### 2 二维矩形覆盖

- 把矩形事件拆成 x 轴事件：
  - `(x_left, y_bottom, y_top, +1)`
  - `(x_right, y_bottom, y_top, -1)`
- 用线段树或 BIT 维护 y 方向覆盖数
- 扫描 x 从小到大，计算面积

------

## 注意事项

1. **事件类型**
   - +1 / -1 或 start/end 标志
2. **排序顺序**
   - 对整数问题，时间排序即可
   - 对面积问题，需要先处理结束事件
3. **变量类型**
   - `current` / `max_count` 根据题目范围选择 `int` / `long long`
4. **复杂度**
   - 排序 O(n log n)
   - 扫描 O(n)
   - 总体 O(n log n) 或 O(events log events)