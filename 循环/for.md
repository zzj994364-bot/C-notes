```cpp
//基本for循环
for (int i = 0; i < v.size(); i++) {
    cout << v[i] << " ";
}


//基于范围的for循环
//auto， 拷贝元素，可以修改拷贝，不影响原容器
for (auto x : v) {
    cout << x << " ";
}
//auto &x， 引用元素，可以修改原容器
for (auto &x : v) {
    cin >> x;
}
//const auto &x， 只读引用，避免拷贝，不能修改元素
for (const auto &x : v) {
    cout << x << " ";
}


//结构化绑定遍历复杂元素
//适合遍历包含多个成员的元素，如 pair、tuple
vector<pair<int,int>> vp = {{1,2},{3,4}};
for (auto &[a,b] : vp) {
    cout << a << "," << b << " ";
}
```