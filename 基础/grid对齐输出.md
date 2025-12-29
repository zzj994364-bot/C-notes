```cpp
for (auto const& y : gird) { // 建议使用 const& 提高效率
    for (auto x : y) {
        // 2. 使用 setw(宽度) 设置每一列的大小
        // 3. 使用 left 或 right 设置对齐方向（数字通常用 right）
            cout << right << setw(3) << x;
    }
    cout << '\n';
}
```

