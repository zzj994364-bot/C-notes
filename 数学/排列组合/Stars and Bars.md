# 隔板法 (Stars and Bars)

## 基础

隔板法（Stars and Bars）是一种用于解决特定类型组合计数问题的技巧。它主要用于求解将 $n$ 个**不可区分**（相同）的物品放入 $k$ 个**可区分**（不同）的盒子中的方案数问题。

这个问题等价于求解一个线性方程的整数解个数。

**核心应用与解决的问题**:
隔板法主要解决以下两种核心模型：

1.  **非负整数解模型 (盒子可为空)**

      * **问题**: 求解方程 $x_1 + x_2 + \dots + x_k = n$ 的非负整数解（即 $x_i \ge 0$）的个数。
      * **物理解释**: 将 $n$ 个相同的球放入 $k$ 个不同的盒子，允许盒子为空。
      * **模型思想**: 想象有 $n$ 个星星（`*`）代表物品，我们需要用 $k-1$ 个隔板（`|`）将它们分成 $k$ 组。问题就转换为在 $n+k-1$ 个总位置中，选择 $k-1$ 个位置放置隔板  **C(stars + bars, bars)**。
      * **公式**:
        $$\binom{n+k-1}{k-1} \quad \text{或} \quad \binom{n+k-1}{n}$$

2.  **正整数解模型 (盒子不能为空)**

      * **问题**: 求解方程 $x_1 + x_2 + \dots + x_k = n$ 的正整数解（即 $x_i \ge 1$）的个数。
      * **物理解释**: 将 $n$ 个相同的球放入 $k$ 个不同的盒子，每个盒子至少放一个。
      * **模型思想**: 想象 $n$ 个星星排成一排，它们之间形成了 $n-1$ 个空隙。问题转换为在这 $n-1$ 个空隙中选择 $k-1$ 个位置放置隔板。
      * **公式**:
        $$\binom{n-1}{k-1}$$

**一般化**: 对于求解 $x_1 + \dots + x_k = n$ 且 $x_i \ge c_i$ 的解，可以通过变量代换 $y_i = x_i - c_i + 1$ 转换成正整数解模型，或 $y_i = x_i - c_i$ 转换成非负整数解模型。

$$y_1 + c_1 + y_2 + c_2 + \dots + y_k + c_k = n$$

$$y_1 + y_2 + \dots + y_k = n - c_1 - c_2 - \dots - c_k$$

$$\binom{n-c_1-c_2-\dots-c_k+k-1}{k-1} \quad \text{或} \quad \binom{n-c_1-c_2-\dots-c_k+k-1}{n-c_1-c_2-\dots-c_k}$$

注：每个 $x_i$ 都有**不同**下限 $c_i$。

时间/空间复杂度：

  * 隔板法本身是一个数学模型，没有时间复杂度。其计算依赖于组合数。
  * 使用预计算阶乘和逆元的方法计算组合数：
      * **预计算**: 时间 $O(N)$，空间 $O(N)$。
      * **单次查询**: 时间 $O(1)$。

## 算法的流程

应用隔板法解决问题的思维流程如下：

1.  **识别模型**: 判断问题是否可以转化为“将 n 个相同物品放入 k 个不同盒子”的模型。明确什么是“物品”（ $n$ ）和“盒子”（ $k$ ）。
2.  **确定约束**: 分析每个盒子的放入限制。是允许为空（ $x_i \ge 0$ ），还是必须非空（ $x_i \ge 1$ ），或者有其他下限（ $x_i \ge c$ ）。
3.  **问题转化**: 如果约束不是最基本的“非负整数解”模型，则先进行转化。
      * 对于“正整数解”（ $x_i \ge 1$ ）模型，可以想象先给每个盒子放一个物品，剩下 $n-k$ 个物品可以随意分配。问题转化为将 $n-k$ 个物品放入 $k$ 个盒子，允许为空。
4.  **套用公式**: 对转化后的问题套用相应的隔板法公式。
      * “ $n'$ 个物品放入 $k$ 个盒子，允许为空” 的方案数为 $\binom{n'+k-1}{k-1}$。
5.  **计算组合数**: 使用预处理好的阶乘和模逆元，计算出最终的组合数值。

## 算法模板

隔板法的代码实现，本质上是调用一个计算组合数的函数。因此，一个完整的模板应包含组合数计算的框架。

```cpp
#include <vector>

// 假设已存在一个用于计算组合数的框架
// const int MAX_N, const int MOD
// long long fact[], invFact[]
// void precompute_factorials()
// long long combinations(int n, int k)
// (为保持模板独立性，在此处重新提供)

const int MAX_SIZE = 200000; // 根据题目 n+k 的最大值调整
const int MOD = 1e9 + 7;

long long fact[MAX_SIZE + 1];
long long invFact[MAX_SIZE + 1];

long long power_mod(long long base, long long exp) {
    long long res = 1;
    base %= MOD;
    while (exp > 0) {
        if (exp & 1) res = (res * base) % MOD;
        base = (base * base) % MOD;
        exp >>= 1;
    }
    return res;
}

long long mod_inverse(long long n) {
    return power_mod(n, MOD - 2);
}

void precompute_factorials() {
    fact[0] = 1;
    for (int i = 1; i <= MAX_SIZE; ++i) {
        fact[i] = (fact[i - 1] * i) % MOD;
    }
    invFact[MAX_SIZE] = mod_inverse(fact[MAX_SIZE]);
    for (int i = MAX_SIZE - 1; i >= 0; --i) {
        invFact[i] = (invFact[i + 1] * (i + 1)) % MOD;
    }
}

long long combinations(int n, int k) {
    if (k < 0 || k > n) {
        return 0;
    }
    return (((fact[n] * invFact[k]) % MOD) * invFact[n - k]) % MOD;
}


/**
 * @brief 隔板法：非负整数解模型 (盒子可为空)
 * 求解 x1 + x2 + ... + xk = n 的非负整数解个数
 * @param n 物品数量 (stars)
 * @param k 盒子数量 (bins)
 * @return 方案数
 */
long long stars_and_bars_non_negative(int n, int k) {
    if (k == 0) return (n == 0) ? 1 : 0;
    // 公式为 C(n + k - 1, k - 1)
    return combinations(n + k - 1, k - 1);
}

/**
 * @brief 隔板法：正整数解模型 (盒子不能为空)
 * 求解 x1 + x2 + ... + xk = n 的正整数解个数
 * @param n 物品数量 (stars)
 * @param k 盒子数量 (bins)
 * @return 方案数
 */
long long stars_and_bars_positive(int n, int k) {
    if (n < k || k <= 0) {
        return 0;
    }
    // 公式为 C(n - 1, k - 1)
    return combinations(n - 1, k - 1);
}
```