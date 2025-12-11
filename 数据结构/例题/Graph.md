https://leetcode.com/problems/minimum-number-of-vertices-to-reach-all-nodes/description/























































































## 解题思路

1. edges 的数量固定为 n - 1
2. DAG 是有效结构
3. 所有节点必须能被“覆盖”

三者结合推出：

#### ✔ 不可能有 indegree = 0 且 outdegree = 0 的点

#### ✔ 所有 indegree = 0 的点，至少会有一条出边

#### ✔ 所有 outdegree = 0 的点，至少会有一条入边

#### ✔ 只有“孤岛”同时 0/0，但题目不允许出现这种点

这是题目隐含的数学结构，不需要题目直接写出来，它自然从约束中推导而来。

**所以只需要求indegree = 0的vertex**



## 代码实现

```cpp
 class Solution {
 public:
     vector<int> findSmallestSetOfVertices(int n, vector<vector<int>>& edges) {
         //所有indegree为0的vertices
         vector<int> indegree(n);
        
         //统计indegree(入度)
         for (auto edge : edges) {
             indegree[edge[1]]++;
         }

         vector<int> ans;
         for (int i = 0; i < n; i++) {
             if (indegree[i] == 0) ans.push_back(i);
         }

         return ans;
     }
 };
```

