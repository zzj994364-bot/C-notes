# queue

- **先进先出（FIFO）**：最先进入队列的元素最先被取出。

- **只允许在队尾插入（push）**，在队首删除（pop）。

- **不能随机访问元素**，只能访问队首（front）和队尾（back）。

```cpp
#include <queue>
using namespace std;

int main() {
    queue<int> q;           // 创建一个整数队列

    q.push(10);             // 入队：10
    q.pop();                // 出队：删除10

    q.front()               //队首元素
    q.back()                //队尾元素
    q.size()                //队列长度

    while(!q.empty()) {
        q.pop();    
    } //清空所有元素。tip：queue不能用clear清空

    cout << (q.empty() ? "empty" : "not empty") << endl;  // 判断是否为空
}
```

# priority_queue

### purpose

- get the largest or smallest element

### properties

- By default, STL priority_queue gives **the largest element the highest priority(max-heap priority queue)**.

```cpp
#include<queue>

priority_queue <int> pq;  //create a priority queue

pq.push(); //inserts a element;
pq.pop(); //removes the element with the highest priority

pq.top(); //returns the element with the highest priority
pq.size(); //returns the numbers of elements
pq.empty(); //returns true if the priority_queue is empty

while(!pq.empty()) {
    cout << pq.top() << endl;
    pq.pop();
}   //prints all elements
while(!pq.empty()) {
    pq.pop();
}   //clear

priority_queue <type, vecotr<type>, greater<type>> pq; // create a min-heap priority queue

int n;
cin >> n;
priority_queue <int> pq[n]; // create n individual priority queues
```

