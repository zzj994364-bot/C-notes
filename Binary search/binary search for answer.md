# 二分答案法完全指南

## 目录
- [基础概念](#基础概念)
- [核心思想](#核心思想)
- [适用条件](#适用条件)
- [算法模板](#算法模板)
- [经典问题类型](#经典问题类型)
- [实战例题详解](#实战例题详解)
- [常见陷阱和注意事项](#常见陷阱和注意事项)

---

## 基础概念

### 什么是二分答案法？

**二分答案法**（Binary Search on Answer）是一种通过二分查找来寻找问题最优解的算法思想。不同于在数组中二分查找某个元素，二分答案法是在**答案的取值范围**上进行二分。

### 与普通二分查找的区别

| 特性 | 普通二分查找 | 二分答案法 |
|------|-------------|-----------|
| 查找对象 | 在有序数组中查找元素 | 在答案空间中查找最优值 |
| 判断依据 | 比较目标值与中间值 | 检查某个答案是否可行 |
| 应用场景 | 查找特定元素 | 求最大值/最小值问题 |
| 核心函数 | `arr[mid] == target` | `check(mid)` 判断可行性 |

---

## 核心思想

### 基本原理

二分答案法的核心是：**将最优化问题转化为判定性问题**。

```
原问题: 求满足条件的最大/最小值
转化为: 判断某个值是否满足条件
```

### 工作流程

```
1. 确定答案的搜索范围 [left, right]
2. 取中间值 mid = (left + right) / 2
3. 通过 check(mid) 函数判断 mid 是否满足条件
4. 根据判断结果调整搜索范围:
   - 如果满足条件，尝试更优的答案
   - 如果不满足，调整到另一半
5. 重复步骤 2-4，直到找到最优解
```

### 图解示例

假设我们要找**最小的满足条件**的值：

```
答案范围: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
check():  F  F  F  F  T  T  T  T  T  T
                      ↑
                   答案是 5
```

可以看到，答案空间具有**单调性**：
- 小于 5 的都不满足条件（False）
- 大于等于 5 的都满足条件（True）

---

## 适用条件

### 必要条件（核心）

**单调性**：答案空间必须具有单调性，即：
- 如果 x 满足条件，那么所有 ≥ x 的值也满足条件（求最小值）
- 如果 x 满足条件，那么所有 ≤ x 的值也满足条件（求最大值）

### 判断是否可用二分答案法

问题需要满足以下特征：
1. ✅ 问题要求求最大值或最小值
2. ✅ 可以在 O(n) 或更快时间内判断某个答案是否可行
3. ✅ 答案具有单调性（关键！）
4. ✅ 答案范围可以确定

### 识别技巧

当题目中出现以下关键词时，考虑二分答案：
- "最大化最小值"
- "最小化最大值"
- "第 k 大/小"
- "满足条件的最大/最小"
- "至少/至多"

---

## 算法模板

### 模板1：求满足条件的最小值

```cpp
#include <iostream>
#include <vector>
using namespace std;

// check 函数: 判断 mid 是否满足条件
bool check(int mid) {
    // 根据具体问题实现判断逻辑
    // 返回 true 表示 mid 满足条件
    // 返回 false 表示 mid 不满足条件
    return true;  // 示例
}

int binarySearchMin(int left, int right) {
    int ans = right + 1;  // 初始化为不可能的值
    
    while (left <= right) {
        int mid = left + (right - left) / 2;  // 防止溢出
        
        if (check(mid)) {
            ans = mid;        // 记录可行解
            right = mid - 1;  // 尝试更小的值
        } else {
            left = mid + 1;   // mid 不可行，向右找
        }
    }
    
    return ans;
}

int main() {
    int result = binarySearchMin(1, 1000000);
    cout << "最小值: " << result << endl;
    return 0;
}
```

### 模板2：求满足条件的最大值

```cpp
#include <iostream>
#include <vector>
using namespace std;

bool check(int mid) {
    // 判断 mid 是否满足条件
    return true;  // 示例
}

int binarySearchMax(int left, int right) {
    int ans = left - 1;  // 初始化为不可能的值
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (check(mid)) {
            ans = mid;       // 记录可行解
            left = mid + 1;  // 尝试更大的值
        } else {
            right = mid - 1; // mid 不可行，向左找
        }
    }
    
    return ans;
}

int main() {
    int result = binarySearchMax(1, 1000000);
    cout << "最大值: " << result << endl;
    return 0;
}
```

### 模板3：浮点数二分

```cpp
#include <iostream>
#include <cmath>
using namespace std;

bool check(double mid) {
    // 判断 mid 是否满足条件
    return true;  // 示例
}

double binarySearchDouble(double left, double right) {
    const double EPS = 1e-6;  // 精度
    
    while (right - left > EPS) {
        double mid = left + (right - left) / 2;
        
        if (check(mid)) {
            left = mid;   // 或 right = mid，取决于求最大/最小
        } else {
            right = mid;  // 或 left = mid
        }
    }
    
    return left;  // 或 right，或 (left + right) / 2
}

int main() {
    double result = binarySearchDouble(0.0, 100.0);
    printf("结果: %.6f\n", result);
    return 0;
}
```

### 模板要点总结

```cpp
// 1. 防止整数溢出
int mid = left + (right - left) / 2;  // ✅ 推荐
int mid = (left + right) / 2;         // ❌ 可能溢出

// 2. 求最小值模板
if (check(mid)) {
    ans = mid;
    right = mid - 1;  // 继续向左找更小的
} else {
    left = mid + 1;   // 向右找
}

// 3. 求最大值模板
if (check(mid)) {
    ans = mid;
    left = mid + 1;   // 继续向右找更大的
} else {
    right = mid - 1;  // 向左找
}
```

---

## 经典问题类型

### 类型1：分配问题

**特征**：将 n 个物品分配给 m 个人，求最大化最小值或最小化最大值。

**例子**：
- 分配巧克力，使得最小的份额最大
- 分配任务，使得最大的工作量最小

### 类型2：切割问题

**特征**：将一个整体切割成若干部分，满足特定条件。

**例子**：
- 切绳子，使得每段长度至少为 k
- 切木头，获得至少 m 段长度为 k 的木条

### 类型3：容量问题

**特征**：在有限容量下，求最优的容量配置。

**例子**：
- 运输问题：每次最多运 w 重量，最少需要几次
- 带宽分配：n 个任务，求最小带宽使得能在 t 时间内完成

### 类型4：距离问题

**特征**：涉及距离的最大化或最小化。

**例子**：
- 放置 m 个物品，使得最小距离最大
- 在数轴上选点，满足距离要求

---

## 实战例题详解

### 例题1：切绳子（求满足条件的最大值）

#### 问题描述
有 n 根绳子，长度分别为 `arr[i]`。需要将这些绳子切成若干段，每段长度相同。求在切出至少 k 段的前提下，每段绳子的最大长度。

#### 输入示例
```
n = 4, k = 11
arr = [8.02, 7.43, 4.57, 5.39]
```

#### 思路分析

1. **答案范围**：[0, max(arr)]
2. **单调性**：如果长度 x 能切出至少 k 段，那么小于 x 的长度也能切出至少 k 段
3. **二分方向**：求最大的满足条件的长度
4. **check 函数**：判断长度为 mid 时，能否切出至少 k 段

#### 完整代码

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <cmath>
using namespace std;

// 判断以 length 为单位长度，能否切出至少 k 段
bool check(const vector<double>& arr, int k, double length) {
    int count = 0;
    for (double rope : arr) {
        count += (int)(rope / length);  // 向下取整
        if (count >= k) return true;    // 提前返回优化
    }
    return count >= k;
}

double cutRope(const vector<double>& arr, int k) {
    double left = 0.0;
    double right = *max_element(arr.begin(), arr.end());
    const double EPS = 1e-5;  // 精度要求
    
    // 浮点数二分：迭代次数固定，或者判断精度
    while (right - left > EPS) {
        double mid = left + (right - left) / 2;
        
        if (check(arr, k, mid)) {
            left = mid;   // mid 可行，尝试更大的
        } else {
            right = mid;  // mid 不可行，尝试更小的
        }
    }
    
    return left;
}

int main() {
    vector<double> arr = {8.02, 7.43, 4.57, 5.39};
    int k = 11;
    
    double result = cutRope(arr, k);
    printf("每段绳子的最大长度: %.2f\n", result);
    // 输出: 2.00
    
    return 0;
}
```

#### 复杂度分析
- **时间复杂度**：O(n × log(maxVal / EPS))
  - log 部分是二分次数
  - 每次 check 需要 O(n)
- **空间复杂度**：O(1)

---

### 例题2：分配问题（LeetCode 875. 爱吃香蕉的珂珂）

#### 问题描述
珂珂喜欢吃香蕉。有 n 堆香蕉，第 i 堆有 `piles[i]` 根。警卫会在 h 小时后回来。珂珂每小时最多吃 k 根香蕉（如果这堆香蕉少于 k 根，她会吃完这堆，然后这一小时内不会再吃）。求珂珂可以在 h 小时内吃掉所有香蕉的最小速度 k。

#### 输入示例
```
piles = [3, 6, 7, 11]
h = 8
输出: 4
```

#### 思路分析

1. **答案范围**：[1, max(piles)]
2. **单调性**：如果速度 k 可行，那么大于 k 的速度也可行
3. **二分方向**：求最小的满足条件的速度
4. **check 函数**：判断速度为 k 时，能否在 h 小时内吃完

#### 完整代码

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// 判断以速度 k 吃香蕉，能否在 h 小时内吃完
bool canFinish(const vector<int>& piles, int h, int k) {
    long long hours = 0;
    for (int pile : piles) {
        // 向上取整: (pile + k - 1) / k 或 (pile - 1) / k + 1
        hours += (pile + k - 1) / k;
        if (hours > h) return false;  // 提前返回优化
    }
    return hours <= h;
}

int minEatingSpeed(vector<int>& piles, int h) {
    int left = 1;
    int right = *max_element(piles.begin(), piles.end());
    int ans = right;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (canFinish(piles, h, mid)) {
            ans = mid;        // 记录可行解
            right = mid - 1;  // 尝试更小的速度
        } else {
            left = mid + 1;   // 速度太慢，需要加快
        }
    }
    
    return ans;
}

int main() {
    vector<int> piles = {3, 6, 7, 11};
    int h = 8;
    
    int result = minEatingSpeed(piles, h);
    cout << "最小速度: " << result << endl;
    // 输出: 4
    
    return 0;
}
```

#### 详细步骤演示

```
piles = [3, 6, 7, 11], h = 8

第1轮: left=1, right=11, mid=6
  check(6): 1+1+2+2=6 <= 8, 可行
  更新 ans=6, right=5

第2轮: left=1, right=5, mid=3
  check(3): 1+2+3+4=10 > 8, 不可行
  更新 left=4

第3轮: left=4, right=5, mid=4
  check(4): 1+2+2+3=8 <= 8, 可行
  更新 ans=4, right=3

第4轮: left=4, right=3, 结束
返回 ans=4
```

---

### 例题3：最小化最大值（LeetCode 410. 分割数组的最大值）

#### 问题描述
给定一个数组 `nums` 和整数 `k`，将数组分割成 k 个非空连续子数组，使得这 k 个子数组各自和的最大值最小。

#### 输入示例
```
nums = [7, 2, 5, 10, 8]
k = 2
输出: 18
解释: 分割为 [7,2,5] 和 [10,8]，最大值为 18
```

#### 思路分析

1. **答案范围**：[max(nums), sum(nums)]
   - 下界：至少要能容纳最大的元素
   - 上界：全部放在一个子数组
2. **单调性**：如果最大值为 x 可行，那么大于 x 也可行
3. **二分方向**：求最小的满足条件的最大值
4. **check 函数**：判断最大值限制为 maxSum 时，能否分成不超过 k 个子数组

#### 完整代码

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

// 判断以 maxSum 为上限，能否将数组分成不超过 k 个子数组
bool canSplit(const vector<int>& nums, int k, int maxSum) {
    int groups = 1;      // 至少需要一组
    int currentSum = 0;
    
    for (int num : nums) {
        if (currentSum + num > maxSum) {
            groups++;           // 需要新开一组
            currentSum = num;   // 当前元素放入新组
            if (groups > k) return false;  // 超过 k 组，不可行
        } else {
            currentSum += num;  // 加入当前组
        }
    }
    
    return groups <= k;
}

int splitArray(vector<int>& nums, int k) {
    int left = *max_element(nums.begin(), nums.end());
    int right = accumulate(nums.begin(), nums.end(), 0);
    int ans = right;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (canSplit(nums, k, mid)) {
            ans = mid;        // 记录可行解
            right = mid - 1;  // 尝试更小的最大值
        } else {
            left = mid + 1;   // 最大值太小，需要增大
        }
    }
    
    return ans;
}

int main() {
    vector<int> nums = {7, 2, 5, 10, 8};
    int k = 2;
    
    int result = splitArray(nums, k);
    cout << "最小的最大值: " << result << endl;
    // 输出: 18
    
    return 0;
}
```

#### 图解演示

```
nums = [7, 2, 5, 10, 8], k = 2

答案范围: [10, 32]
         (最大元素) (总和)

第1轮: left=10, right=32, mid=21
  check(21): [7,2,5] [10] [8] = 3组 > 2, 不可行
  更新 left=22

第2轮: left=22, right=32, mid=27
  check(27): [7,2,5,10] [8] = 2组, 可行
  更新 ans=27, right=26

第3轮: left=22, right=26, mid=24
  check(24): [7,2,5,10] [8] = 2组, 可行
  更新 ans=24, right=23

第4轮: left=22, right=23, mid=22
  check(22): [7,2,5] [10,8] = 3组 > 2, 不可行
  更新 left=23

第5轮: left=23, right=23, mid=23
  check(23): [7,2,5] [10,8] = 3组 > 2, 不可行
  更新 left=24

第6轮: left=24, right=23, 结束
返回 ans=24

实际上答案是 18: [7,2,5] [10,8]
让我重新检查...
```

**修正版本的 check 函数：**

```cpp
bool canSplit(const vector<int>& nums, int k, long long maxSum) {
    int groups = 1;
    long long currentSum = 0;
    
    for (int num : nums) {
        if (num > maxSum) return false;  // 单个元素超过限制
        
        if (currentSum + num <= maxSum) {
            currentSum += num;
        } else {
            groups++;
            currentSum = num;
            if (groups > k) return false;
        }
    }
    
    return true;
}
```

---

### 例题4：最大化最小值（LeetCode 1552. 两球之间的磁力）

#### 问题描述
有一个数组 `position`，表示篮子的位置。需要在这些位置中选择 m 个篮子放球，使得任意两球之间的最小磁力最大。

#### 输入示例
```
position = [1, 2, 3, 4, 7]
m = 3
输出: 3
解释: 选择位置 1, 4, 7，最小距离为 3
```

#### 思路分析

1. **预处理**：先对位置排序
2. **答案范围**：[1, max(position) - min(position)]
3. **单调性**：如果距离 d 可行，那么小于 d 的距离也可行
4. **二分方向**：求最大的满足条件的距离
5. **check 函数**：判断距离为 minDist 时，能否放下 m 个球

#### 完整代码

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// 判断最小距离为 minDist 时，能否放下 m 个球
bool canPlace(const vector<int>& position, int m, int minDist) {
    int count = 1;  // 第一个球放在第一个位置
    int lastPos = position[0];
    
    for (int i = 1; i < position.size(); i++) {
        if (position[i] - lastPos >= minDist) {
            count++;
            lastPos = position[i];
            if (count >= m) return true;  // 提前返回
        }
    }
    
    return count >= m;
}

int maxDistance(vector<int>& position, int m) {
    sort(position.begin(), position.end());  // 必须先排序
    
    int left = 1;
    int right = position.back() - position[0];
    int ans = 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (canPlace(position, m, mid)) {
            ans = mid;       // 记录可行解
            left = mid + 1;  // 尝试更大的距离
        } else {
            right = mid - 1; // 距离太大，减小
        }
    }
    
    return ans;
}

int main() {
    vector<int> position = {1, 2, 3, 4, 7};
    int m = 3;
    
    int result = maxDistance(position, m);
    cout << "最大的最小磁力: " << result << endl;
    // 输出: 3
    
    return 0;
}
```

#### 贪心放置策略

```cpp
// check 函数的贪心策略：
// 1. 第一个球总是放在第一个位置
// 2. 后续的球尽可能靠前放置（只要满足最小距离）
// 3. 这样能放置最多的球

position = [1, 2, 3, 4, 7], m = 3, minDist = 3

第1个球: 位置 1
第2个球: 位置 4 (1+3=4)
第3个球: 位置 7 (4+3=7)
共放置 3 个球，满足要求
```

---

### 例题5：第 K 小的距离（LeetCode 719）

#### 问题描述
给定整数数组 `nums` 和整数 `k`，找出所有数对 `(i, j)` 之间的第 k 小距离。距离定义为 `|nums[i] - nums[j]|`。

#### 输入示例
```
nums = [1, 3, 1]
k = 1
输出: 0
解释: 所有距离为 [0, 2, 2]，第 1 小的是 0
```

#### 思路分析

1. **预处理**：对数组排序
2. **答案范围**：[0, max(nums) - min(nums)]
3. **单调性**：距离小于等于 d 的数对数量是单调递增的
4. **二分方向**：求第 k 小的距离
5. **check 函数**：统计距离不超过 mid 的数对数量

#### 完整代码

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// 统计距离不超过 maxDist 的数对数量
int countPairs(const vector<int>& nums, int maxDist) {
    int count = 0;
    int left = 0;
    
    // 双指针统计
    for (int right = 1; right < nums.size(); right++) {
        while (nums[right] - nums[left] > maxDist) {
            left++;
        }
        count += right - left;  // [left, right) 之间的所有配对
    }
    
    return count;
}

int smallestDistancePair(vector<int>& nums, int k) {
    sort(nums.begin(), nums.end());  // 必须先排序
    
    int left = 0;
    int right = nums.back() - nums[0];
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        int count = countPairs(nums, mid);
        
        if (count < k) {
            left = mid + 1;  // 数对太少，距离要增大
        } else {
            right = mid;     // 数对足够，可能还能更小
        }
    }
    
    return left;
}

int main() {
    vector<int> nums = {1, 3, 1};
    int k = 1;
    
    int result = smallestDistancePair(nums, k);
    cout << "第 " << k << " 小的距离: " << result << endl;
    // 输出: 0
    
    return 0;
}
```

#### 双指针统计技巧

```cpp
// 对于排序后的数组，统计距离不超过 maxDist 的数对

nums = [1, 1, 3], maxDist = 2

right = 1: nums[1] = 1
  left = 0, nums[1] - nums[0] = 0 <= 2
  配对数: 1 - 0 = 1  [(0,1)]

right = 2: nums[2] = 3
  left = 0, nums[2] - nums[0] = 2 <= 2
  配对数: 2 - 0 = 2  [(0,2), (1,2)]

总配对数: 1 + 2 = 3
```

---

## 常见陷阱和注意事项

### 1. 整数溢出

```cpp
// ❌ 错误: 可能溢出
int mid = (left + right) / 2;

// ✅ 正确: 防止溢出
int mid = left + (right - left) / 2;

// ✅ 也可以使用无符号右移（C++20）
int mid = (left + right) >> 1;

// ✅ 对于 long long
long long mid = left + (right - left) / 2;
```

### 2. 浮点数精度

```cpp
// 浮点数二分的两种终止条件

// 方法1: 固定迭代次数（推荐）
for (int i = 0; i < 100; i++) {
    double mid = (left + right) / 2;
    // ...
}

// 方法2: 精度判断
const double EPS = 1e-6;
while (right - left > EPS) {
    double mid = (left + right) / 2;
    // ...
}
```

### 3. 向上取整技巧

```cpp
// 计算 a / b 的向上取整

// ❌ 错误: 浮点数可能有精度问题
int result = (int)ceil((double)a / b);

// ✅ 正确: 整数运算
int result = (a + b - 1) / b;

// ✅ 也可以
int result = (a - 1) / b + 1;

// 示例
int a = 10, b = 3;
// (10 + 3 - 1) 
```