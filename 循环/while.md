```cpp
while(n) //当n = 0时停止
while(n--)//当n减1到0时停止，且会对n的值造成影响
while(cin >> n)//直到不能输入n的值时停止
while(a % b)//当 a 除以 b 的余数为 0 时停止，循环继续执行

while(n > 0) {
    ----
    n /= 10;
} //一个循环模型
```

continue在while中的作用时跳过剩下的代码，直接进入下一轮判断循环,return 0同理