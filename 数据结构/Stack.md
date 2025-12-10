# stack

如何理解：

```cpp
stack<int> s;
s.push(1); // 栈: [1]
s.push(2); // 栈: [1, 2]
s.push(3); // 栈: [1, 2, 3]
cout << s.top(); // 输出 3（栈顶元素）
s.pop();         // 弹出 3，栈变成 [1, 2]
cout << s.top(); // 输出 2
```

常见操作：

```cpp
#include <stack>
using namespace std;

stack<int> s; // 创建一个空的 stack

// 访问栈顶元素
int topVal = s.top();  // 获取栈顶元素（不删除），若栈为空，调用 top() 会导致未定义行为

// 栈操作
s.push(10);        // 压栈：将元素10压入栈顶

// 删除栈顶元素
s.pop();          // 弹栈：删除栈顶元素（不返回值），调用前应确认栈不为空

// 判断是否为空
bool empty = s.empty();  // 若栈为空返回 true，否则返回 false

// 栈大小
int n = s.size();        // 当前栈中元素的数量

// 清除栈的所有元素
while (!s.empty()) {
    s.pop();
}
```

括号对称：

```cpp
bool isbalenced(string s) {
    stack<char> st;
    for (char c : s) {
        if (c == '(' || c == '{' || c == '[') {
            st.push(c);
        } else {
            if (st.empty()) return false;
            char top = st.top();
            if ((c == ')' && top != '(') ||
                (c == '}' && top != '{') ||
                (c == ']' && top != '[')) {
                return false;
            }
            st.pop();
        }
    }
    return st.empty();
}
```
