# XOR (异或) 运算完整笔记

## 基础概念

### 什么是 XOR？

**XOR（异或）** 是一种位运算，符号为 `^`。

**定义：** 两个位相同为 0，不同为 1。

```
0 ^ 0 = 0
0 ^ 1 = 1
1 ^ 0 = 1
1 ^ 1 = 0
```

**口诀：** "相同为假，不同为真"

### 符号说明

| 语言/环境  | XOR 符号 | 次方符号   |
| ---------- | -------- | ---------- |
| C/C++/Java | `^`      | `pow(a,b)` |
| Python     | `^`      | `**`       |
| 数学公式   | ⊕ 或 XOR | `^` 或上标 |

⚠️ **注意：** C/C++ 中 `^` 是 XOR，不是次方！

------

## 核心性质

### 1. 交换律

```
a ^ b = b ^ a
```

### 2. 结合律

```
(a ^ b) ^ c = a ^ (b ^ c)
```

### 3. 恒等律

```
a ^ 0 = a
```

**解释：** 任何数与 0 异或都等于它自己。

### 4. 自反律（归零律）

```
a ^ a = 0
```

**解释：** 任何数与自己异或都等于 0。

### 5. ⭐ 自逆性（最重要！）

```
a ^ b ^ b = a
```

**推论：** 如果 `a ^ b = c`，则：

- `a = b ^ c`
- `b = a ^ c`
- `c = a ^ b`

**含义：** XOR 是自己的逆运算！

------

## 真值表

### 单位运算

| a    | b    | a ^ b |
| ---- | ---- | ----- |
| 0    | 0    | 0     |
| 0    | 1    | 1     |
| 1    | 0    | 1     |
| 1    | 1    | 0     |

### 多位运算示例

```
  5 = 0101
  3 = 0011
  ─────────
5^3 = 0110 = 6
```

逐位计算：

- 第 0 位：1 ^ 1 = 0
- 第 1 位：0 ^ 1 = 1
- 第 2 位：1 ^ 0 = 1
- 第 3 位：0 ^ 0 = 0

------

## 逆运算性质（深入理解）

### 什么是逆运算？

**定义：** 如果运算 ⊕ 有逆运算 ⊖，则：`a ⊕ b ⊖ b = a`

**例子：**

- 加法的逆运算是减法：`a + b - b = a`
- 乘法的逆运算是除法：`a × b ÷ b = a`
- XOR 的逆运算是**自己**：`a ^ b ^ b = a`

### XOR 的特殊性：自逆运算

**核心概念：** XOR 是自己的逆运算！

```
如果：a ^ b = c
则：
  a ^ b ^ b = c ^ b  （两边同时 XOR b）
  a ^ 0 = c ^ b      （因为 b ^ b = 0）
  a = c ^ b          （因为 a ^ 0 = a）
```

### 逆运算对比表

| 运算    | 符号  | 逆运算      | 是否自逆 | 示例                |
| ------- | ----- | ----------- | -------- | ------------------- |
| 加法    | +     | 减法 (-)    | ✗        | `a + b - b = a`     |
| 减法    | -     | 加法 (+)    | ✗        | `a - b + b = a`     |
| 乘法    | ×     | 除法 (÷)    | ✗        | `a × b ÷ b = a`     |
| 除法    | ÷     | 乘法 (×)    | ✗        | `a ÷ b × b = a`     |
| **XOR** | **^** | **XOR (^)** | **✓**    | **`a ^ b ^ b = a`** |
| AND     | &     | 无          | ✗        | 不可逆              |
| OR      | \|    | 无          | ✗        | 不可逆              |
| NOT     | ~     | NOT (~)     | ✓        | `~~a = a`           |

### 自逆性的数学证明

**方法1：利用基本性质**

```
证明: a ^ b ^ b = a

步骤1: a ^ b ^ b
     = a ^ (b ^ b)    [结合律]
     = a ^ 0          [自反律: b ^ b = 0]
     = a              [恒等律: a ^ 0 = a]
```

**方法2：真值表验证**

```
a | b | a^b | (a^b)^b | 验证
--|---|-----|---------|-----
0 | 0 |  0  |    0    | ✓
0 | 1 |  1  |    0    | ✓
1 | 0 |  1  |    1    | ✓
1 | 1 |  0  |    1    | ✓
```

### 逆运算的推论

从 `a ^ b = c` 可以推导出**三个等价形式**：

```
a ^ b = c  ⟺  a = b ^ c  ⟺  b = a ^ c
```

**证明过程：**

```
已知: a ^ b = c

推导 a = b ^ c:
  a ^ b = c
  a ^ b ^ b = c ^ b    [两边同时 XOR b]
  a = c ^ b            [简化]
  a = b ^ c            [交换律]

推导 b = a ^ c:
  a ^ b = c
  a ^ a ^ b = a ^ c    [两边同时 XOR a]
  b = a ^ c            [简化]
```

### 逆运算的实际应用

#### 应用1：加密/解密

```cpp
// 加密和解密使用相同的操作
int key = 12345;
int message = 9876;

int encrypted = message ^ key;  // 加密
int decrypted = encrypted ^ key; // 解密

// 验证: decrypted == message
// 原理: (message ^ key) ^ key = message
```

#### 应用2：数据恢复

```cpp
// RAID 5 式的数据恢复
int disk1 = 0b1010;
int disk2 = 0b1100;
int parity = disk1 ^ disk2;  // 校验盘: 0b0110

// 如果 disk1 损坏，可以恢复:
int recovered_disk1 = disk2 ^ parity;  // 0b1010 ✓

// 如果 disk2 损坏，可以恢复:
int recovered_disk2 = disk1 ^ parity;  // 0b1100 ✓
```

#### 应用3：交换变量

```cpp
void swap(int& a, int& b) {
    a = a ^ b;  // a' = a ^ b
    b = a ^ b;  // b' = (a ^ b) ^ b = a
    a = a ^ b;  // a'' = (a ^ b) ^ a = b
}

// 原理: XOR 自逆性
// 第2步: b' = a' ^ b = (a ^ b) ^ b = a ^ (b ^ b) = a ^ 0 = a
// 第3步: a'' = a' ^ b' = (a ^ b) ^ a = (a ^ a) ^ b = 0 ^ b = b
```

#### 应用4：链式恢复

```cpp
// 知道 a ^ b ^ c ^ d = result
// 如果知道 a, b, c，可以求 d
int a = 5, b = 7, c = 3;
int result = 12;
int d = a ^ b ^ c ^ result;

// 验证
assert((a ^ b ^ c ^ d) == result);  // ✓
```

### 为什么 AND 和 OR 不是自逆的？

#### AND 的问题

```
a & b = c  ⟹  无法唯一确定 a

例如: ? & 1 = 0
可能是: 0 & 1 = 0
也可能: 2 & 1 = 0
还可能: 4 & 1 = 0
... (无穷多解)

信息丢失: AND 会永久"抹掉"某些位
```

#### OR 的问题

```
a | b = c  ⟹  无法唯一确定 a

例如: ? | 3 = 7
可能是: 4 | 3 = 7
也可能: 5 | 3 = 7
还可能: 7 | 3 = 7
... (无穷多解)

信息丢失: OR 会永久"设置"某些位
```

#### XOR 的优势

```
a ^ b = c  ⟹  唯一确定 a = b ^ c

例如: ? ^ 3 = 7
只能是: 4 ^ 3 = 7

无信息丢失: XOR 可以完全恢复原始数据
```

### 数学本质

XOR 可以看作**模 2 加法**（不进位的加法）：

```
二进制加法表:
  0 + 0 = 0
  0 + 1 = 1
  1 + 0 = 1
  1 + 1 = 10 (进位)

XOR 运算表（模2加法）:
  0 ^ 0 = 0
  0 ^ 1 = 1
  1 ^ 0 = 1
  1 ^ 1 = 0 (不进位，相当于 1+1 mod 2)
```

在模 2 算术中：

- 加法 = XOR
- 减法 = XOR（加法就是减法）
- 所以 XOR 是自逆的！

### 记忆技巧

**口诀：** "XOR 两次见，回到初始点"

```
a ^ b ^ b = a
   ↓   ↓
  出去 回来
```

就像：

- 向前走 5 步，再向后走 5 步，回到原地
- XOR b，再 XOR b，回到原值

------

## 常用技巧

### 1. 判断奇偶性

```cpp
bool isOdd(int n) {
    return n & 1;  // 用 AND，不是 XOR
}
```

### 2. 交换两个数（无需临时变量）

```cpp
void swap(int& a, int& b) {
    a ^= b;  // a = a ^ b
    b ^= a;  // b = b ^ (a ^ b) = a
    a ^= b;  // a = (a ^ b) ^ a = b
}
```

**注意：** 实际开发中不推荐，可读性差且无性能优势。

### 3. 翻转特定位

```cpp
// 翻转第 k 位（0-indexed）
int flipBit(int n, int k) {
    return n ^ (1 << k);
}

// 例子：翻转第 2 位
// n = 5 (0101) -> 5 ^ 4 (0100) = 1 (0001)
```

### 4. 检查两个数是否不同

```cpp
bool isDifferent(int a, int b) {
    return (a ^ b) != 0;
}
```

### 5. 统计不同的位数

```cpp
int countDifferentBits(int a, int b) {
    int xor_result = a ^ b;
    int count = 0;
    while (xor_result) {
        count += xor_result & 1;
        xor_result >>= 1;
    }
    return count;
}

// 或使用内置函数
int countDifferentBits(int a, int b) {
    return __builtin_popcount(a ^ b);
}
```

------

## 经典应用

### 1. 找出唯一出现一次的数

**问题：** 数组中所有数都出现两次，只有一个出现一次，找出它。

```cpp
int findSingle(vector<int>& nums) {
    int result = 0;
    for (int num : nums) {
        result ^= num;
    }
    return result;
}
```

**原理：** `a ^ a = 0`，相同的数会互相抵消。

**例子：**

```
[2, 3, 2, 4, 4]
2 ^ 3 ^ 2 ^ 4 ^ 4
= (2 ^ 2) ^ (4 ^ 4) ^ 3
= 0 ^ 0 ^ 3
= 3 ✓
```

### 2. 找出两个唯一出现一次的数

**问题：** 数组中只有两个数出现一次，其余都出现两次。

```cpp
vector<int> findTwoSingles(vector<int>& nums) {
    // 步骤1：得到两个唯一数的异或结果
    int xor_all = 0;
    for (int num : nums) {
        xor_all ^= num;
    }
    
    // 步骤2：找到任意一个为 1 的位
    int rightmost_bit = xor_all & (-xor_all);
    
    // 步骤3：根据这一位分组
    int num1 = 0, num2 = 0;
    for (int num : nums) {
        if (num & rightmost_bit) {
            num1 ^= num;
        } else {
            num2 ^= num;
        }
    }
    
    return {num1, num2};
}
```

### 3. 简单加密/解密

```cpp
// 加密
int encrypt(int data, int key) {
    return data ^ key;
}

// 解密（使用相同的 key）
int decrypt(int encrypted, int key) {
    return encrypted ^ key;
}

// 例子
int message = 42;
int key = 123;
int encrypted = encrypt(message, key);  // 42 ^ 123 = 81
int decrypted = decrypt(encrypted, key); // 81 ^ 123 = 42 ✓
```

**原理：** `(a ^ key) ^ key = a`

### 4. 判断两个数符号是否相同

```cpp
bool sameSign(int a, int b) {
    return (a ^ b) >= 0;
}
```

**原理：** 符号位相同时，XOR 结果的符号位为 0（正数）。

### 5. 寻找缺失的数字

**问题：** 数组包含 0 到 n 中除了一个数以外的所有数。

```cpp
int missingNumber(vector<int>& nums) {
    int n = nums.size();
    int result = n;
    for (int i = 0; i < n; i++) {
        result ^= i ^ nums[i];
    }
    return result;
}
```

**原理：** 完整序列与实际数组异或，相同的数抵消，剩下缺失的数。

------

## 算法题模板

### 模板 1：单个特殊数

```cpp
// 所有数出现 k 次，只有一个出现 1 次（k 为偶数）
int findSpecial(vector<int>& nums) {
    int result = 0;
    for (int num : nums) {
        result ^= num;
    }
    return result;
}
```

### 模板 2：XOR 前缀和

```cpp
// 计算区间 [l, r] 的异或和
class XORPrefix {
    vector<int> prefix;
public:
    XORPrefix(vector<int>& arr) {
        int n = arr.size();
        prefix.resize(n + 1);
        for (int i = 0; i < n; i++) {
            prefix[i + 1] = prefix[i] ^ arr[i];
        }
    }
    
    int query(int l, int r) {
        return prefix[r + 1] ^ prefix[l];
    }
};
```

### 模板 3：最大 XOR

```cpp
// 找数组中两个数的最大 XOR 值（使用字典树）
struct TrieNode {
    TrieNode* children[2] = {};
};

class Solution {
    TrieNode* root = new TrieNode();
    
    void insert(int num) {
        TrieNode* node = root;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (!node->children[bit]) {
                node->children[bit] = new TrieNode();
            }
            node = node->children[bit];
        }
    }
    
    int findMax(int num) {
        TrieNode* node = root;
        int maxXOR = 0;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            // 尝试走相反的路径以最大化 XOR
            int toggledBit = 1 - bit;
            if (node->children[toggledBit]) {
                maxXOR |= (1 << i);
                node = node->children[toggledBit];
            } else {
                node = node->children[bit];
            }
        }
        return maxXOR;
    }
    
public:
    int findMaximumXOR(vector<int>& nums) {
        for (int num : nums) {
            insert(num);
        }
        int result = 0;
        for (int num : nums) {
            result = max(result, findMax(num));
        }
        return result;
    }
};
```

------

## 注意事项

### 1. 符号混淆

```cpp
// ❌ 错误：C++ 中 ^ 不是次方
int power = 2 ^ 3;  // 结果是 1，不是 8！

// ✅ 正确：
int power = pow(2, 3);  // 8
int power = 1 << 3;     // 8 (2的幂专用)
```

### 2. 优先级陷阱

```cpp
// ❌ 错误：^ 的优先级低于 ==
if (a ^ b == 0)  // 等价于 a ^ (b == 0)

// ✅ 正确：
if ((a ^ b) == 0)
```

### 3. 有符号数问题

```cpp
// 对于负数，XOR 仍然按位操作
int a = -5;  // 二进制：11111111111111111111111111111011
int b = 3;   // 二进制：00000000000000000000000000000011
int c = a ^ b;  // 正常工作，但结果可能是负数
```

### 4. 交换变量的特殊情况

```cpp
// ❌ 错误：如果 a 和 b 指向同一内存
int arr[5] = {1, 2, 3, 4, 5};
// swap(arr[2], arr[2]);  // 会导致 arr[2] = 0！

// 原因：
a ^= a;  // a = 0
a ^= a;  // a = 0
a ^= a;  // a = 0
```

------

## 常见题型

### LeetCode 经典题目

1. **136. Single Number** - 找唯一出现一次的数
2. **137. Single Number II** - 找唯一出现一次的数（其他出现3次）
3. **260. Single Number III** - 找两个唯一出现一次的数
4. **268. Missing Number** - 找缺失的数
5. **371. Sum of Two Integers** - 不用 + 实现加法
6. **421. Maximum XOR of Two Numbers** - 最大 XOR 值
7. **1486. XOR Operation in an Array** - 数组 XOR 运算

### Codeforces 常见标签

- `bitmasks`
- `constructive algorithms`
- `math`

------

## 速查表

| 操作       | 表达式              | 结果          |
| ---------- | ------------------- | ------------- |
| 保持不变   | `a ^ 0`             | `a`           |
| 取反所有位 | `a ^ 1`             | `~a` (整数时) |
| 归零       | `a ^ a`             | `0`           |
| 恢复原值   | `a ^ b ^ b`         | `a`           |
| 判断不同   | `a ^ b != 0`        | true/false    |
| 交换       | `a^=b; b^=a; a^=b;` | 交换 a, b     |

------

## 总结

### XOR 的本质

- **数学角度：** 模 2 加法（不进位的加法）
- **逻辑角度：** 不同为真，相同为假
- **密码学角度：** 可逆的混淆运算

### 记忆要点

1. ⭐ **自逆性是核心**：`a ^ b ^ b = a`
2. 相同的数异或为 0：`a ^ a = 0`
3. 任何数与 0 异或是自己：`a ^ 0 = a`
4. XOR 满足交换律和结合律

### 应用场景

- 找唯一数/缺失数
- 简单加密
- 位操作优化
- 数据校验（奇偶校验）

------

## 参考资源

- [LeetCode 位运算专题](https://leetcode.com/tag/bit-manipulation/)
- [Codeforces Bitmasks Tutorial](https://codeforces.com/blog/entry/73490)
- [Bit Twiddling Hacks](https://graphics.stanford.edu/~seander/bithacks.html)

------

**最后提醒：** XOR 虽然强大，但不要过度使用！代码可读性永远比技巧更重要。✨