# list

### purpose

参考deque

### properties

![](C:\Users\24940\Desktop\C++笔记\数据结构\images\WEBRESOURCEebae7b6cd3141781dec3d9059ab133a3615DF1610E43DEEDC43B6C4030979CEB.png)

```cpp
#include <list>
#include <algorithm> //for std::find

int main() {
    list <type> l; //创建一个list
    list <int> nums = {1, 2, 3, 4, 5}; //初始化一个list

    front()
    back()

    .push_front()
    .push_back()
    .pop_front()
    .pop_back()

    remove(x) //删除所有值为x的元素

    //只删除第一个值为x的元素
    auto it = find(l.begin(), l.end(), x); // 找到第一个等于 x 的位置（若无则返回 end）
    if (it != l.end()) l.erase(it);        // 只删除第一个

    reverse()
    Reverses the order of the elements.
    sort()
    Sorts the list elements in a particular order.
    unique()
    Removes consecutive duplicate elements.
    empty()
    Checks whether the list is empty.
    size()
    Returns the number of elements in the list.
    clear()
    Clears all the values from the list
    merge()
    Merges two sorted lists.

}
```

