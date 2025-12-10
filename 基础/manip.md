```c
#include <iomanip>
```

manip全称：manipulator

1. setfill setw

setw（int n）是定义后面一个数值输出的宽度

setfill(‘char c')补足空白的位置,setfill只能填'字符'，不能填"字符串"

```c
//二者通常一起使用 
cout << setfill(' ') << setw( )
```

```c
int main() {
cout << setfill('0') << setw(5) << 42 << endl;    // 输出：00042 （数字右对齐，左边补0）
cout << setfill('*') << setw(5) << "hi" << endl;  // 输出：hi*** （字符串左对齐，右边补*）
return 0;
}
```