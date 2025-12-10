定义函数示例：

```cpp
#include <iostream>
using namespace std;

int floor(int A, int B) {
    return A / B;
}

int ceil(int A, int B) {
    if (A / B * B < A) {
        return A / B + 1;
    }
    return A / B;
}

int round(int A, int B) {
    return A * 1.0 / B + 0.5;
}

int main() {
    int A, B;
    cin >> A >> B;
    printf("floor %d / %d = %d\n", A, B, floor(A, B));
    printf("ceil %d / %d = %d\n", A, B, ceil(A, B));
    printf("round %d / %d = %d\n", A, B, round(A, B));

    return 0;
}
```