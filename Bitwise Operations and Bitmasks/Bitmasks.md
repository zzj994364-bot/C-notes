# 位掩码

**一个整数变量可以表示一个包含多个“开/关”状态的集合，其中每一个二进制位对应一个独立的状态。这是一种应用位运算思想的具体技巧。比喻成灯泡。**



unsigned int 最多32位

unsigned long long 最多64位

### 集合操作 (Set Operations)

##### 位掩码来模拟基本的集合操作。

```cpp
// 假设我们处理的集合是 {0, 1, ..., n-1}
// 一个整数 mask 代表这个集合的一个子集

// 检查元素 k 是否在集合中
bool has(int mask, int k) {
    return (mask >> k) & 1;
}

// 向集合中添加元素 k
int add(int mask, int k) {
    return mask | (1 << k);
}

// 从集合中移除元素 k
int remove(int mask, int k) {
    return mask & (~(1 << k));
}

// 翻转集合中元素 k 的状态 (在/不在)
int flip(int mask, int k) {
    return mask ^ (1 << k);
}

// 两个集合的并集
int union_set(int mask1, int mask2) {
    return mask1 | mask2;
}

// 两个集合的交集
int intersection_set(int mask1, int mask2) {
    return mask1 & mask2;
}

// 集合的大小 (元素个数)
int size(int mask) {
    // 在竞赛中通常使用内置函数以提高效率
    return __builtin_popcount(mask);
}

// 空集
const int EMPTY_SET = 0;

// 包含 n 个元素的全集 {0, 1, ..., n-1}
int full_set(int n) {
    return (1 << n) - 1;
}
```

##### 遍历所有子集

遍历一个大小为 `n` 的集合的所有 2n2*n* 个子集。

```cpp
// 遍历集合 {0, 1, ..., n-1} 的所有子集
void iterate_all_subsets(int n) {
    // 每个 i 从 0 到 (1<<n)-1 都唯一对应一个子集
    for (int mask = 0; mask < (1 << n); ++mask) {
        // 在这里处理由 mask 代表的子集
        // 例如，可以遍历 mask 中的元素:
        // for (int k = 0; k < n; ++k) {
        //     if (has(mask, k)) {
        //         // 元素 k 在当前子集中
        //     }
        // }
    }
}
```

##### 遍历一个掩码的子集

给定一个掩码 `mask`，遍历它所代表集合的所有非空子集。

```cpp
// 遍历 mask 的所有非空子集
void iterate_submasks(int mask) {
    // sub 从 mask 开始，每次迭代都变成 mask 的下一个(字典序更小的)子集
    // 这个技巧非常高效，复杂度与 mask 的子集数量成正比
    for (int sub = mask; sub > 0; sub = (sub - 1) & mask) {
        // 在这里处理子集 sub
    }
    // 如果需要处理空集，可以在循环外单独处理
}
```