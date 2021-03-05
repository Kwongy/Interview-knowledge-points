## 队列

队列是一种特殊的线性表，入队元素只能插入队尾，出队元素只能从对头出队。进行插入操作的端称为队尾，进行删除操作的端称为队头。


队列分为一般队列，循环队列和优先队列

## 实现

Python和C++有各自的API和库支持队列这种数据结构。

1. Python

同栈一样，Python可以基于列表实现队列。Python还有额外的包queue来实现队列。

```
    import queue
```

queue包中主要有三种队列，Queue（队列）, PriorityQueue (优先队列) 和 LifoQueue （栈， 不解释）。

普通队列使用方法：

```
    q = queue.Queue()
    q.qsize() 返回队列的大小
    q.empty() 如果队列为空，返回True,反之False
    q.full() 如果队列满了，返回True,反之False
    q.get(block=True, timeout=None]) 从队列中返回并删除一个元素，timeout等待时间
    q.get_nowait() 相当q.get(False)
    q.put(item, block=True, timeout=None）非阻塞 q.put(item) 写入队列，timeout等待时间
    q.put_nowait(item) 相当q.put(item, False)
    q.task_done() 在完成一项工作之后，q.task_done() 函数向任务已经完成的队列发送一个信号
    q.join() 实际上意味着等到队列为空，再执行别的操作
```
    
优先队列的使用方法：

``` 
    q = queue.PriorityQueue()
    q.put(3)
    q.put(78)
    q.put(100)
    print(q.queue)  #查看优先级队列中的所有元素
    q.get() #返回并删除优先级最高的元素
```

由于优先队列每次插入和删除元素时都会调用__lt__方法自动排序，因此，可以自定义类，并重写__lt__函数来实现自定义优先级。

eg:

```
class Skill(object):
    def __init__(self, priority, description):
        self.priority = priority
        self.description = description

    def __lt__(self, other):
        return self.priority < other.priority

que = queue.PriorityQueue()
que.put(Skill(7, 'proficient7'))
que.put(Skill(5, 'proficient5'))
que.put(Skill(6, 'proficient6'))
que.put(Skill(10, 'expert'))
que.put(Skill(1, 'novice'))
print('end')
while not que.empty():
    print(que.get())
```

2. C++

C++中除了可以利用数组来实现队列外，也有专门的库来实现队列。

```
    #include<queue>
```

普通队列使用方法：

```
    queue<int> q;
    q.back()返回最后一个元素
    q.empty()如果队列空则返回真
    q.front()返回第一个元素
    q.pop()删除第一个元素
    q.push()在末尾加入一个元素
    q.size()返回队列中元素的个数
```

优先队列

```
    priority_queue<Type, Container, Functional>
```

其中Type为优先队列所存储的数据类型。Container为容器类型，默认为Vector(可不写)。Functional为优先级的比较方式，可以使用仿函数`greater<xxx>`或`less<xxx>`，或者自定实现比较方式，该参数可省略，默认为`less<xxx>`，即从大到小排序。其余操作与普通队列相同。

### 一般队列

1. 数组实现：

```
#include<iostream>
using namespace std;

struct Queue {
	int maxSize;
	int front;
	int rear;
	int size;
	int* arr;
};
bool IsFull(Queue* q) {
	return q->size == q->maxSize;
}
bool IsEmpty(Queue* q) {
	return q->size == 0;
}

Queue* CreateQueue(int maxsize) {
	Queue* q = (Queue*)malloc(sizeof(Queue));
	q->maxSize = maxsize;
	q->arr = (int*)malloc(sizeof(int) * maxsize);
	q->size = 1;
	q->front = 1;
	q->rear = 0;
	return q;
}
void EnQueue(Queue* q, int num) {
	if (IsFull(q))
	{
		cout << "Queue is full" << endl;
	}
	else {
		q->arr[++q->rear] = num;
	}
}
int DeQueue(Queue* q) {
	if (IsEmpty(q)){
		cout << "Queue is empty" << endl;
		exit(ENOEXEC);
	}
	else {
		int ans = q->arr[q->front];
		q->front++;
		return ans;
	}
}
int main() {
	Queue* q = CreateQueue(3);
	EnQueue(q, 5);
	EnQueue(q, 7);
	EnQueue(q, 9);
	cout << DeQueue(q) << endl;
	cout << DeQueue(q) << endl;
	cout << DeQueue(q) << endl;
	return 0;
}
```


2. 双栈实现

```
    # Leetcode 232
    class MyQueue:

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.stack1 = []
        self.stack2 = []

    def push(self, x: int) -> None:
        """
        Push element x to the back of queue.
        """
        self.stack1.append(x)


    def pop(self) -> int:
        """
        Removes the element from in front of queue and returns that element.
        """
        if len(self.stack2) < 1:
            while len(self.stack1) > 0:
                self.stack2.append(self.stack1.pop(-1))
        ans = self.stack2.pop()
        return ans


    def peek(self) -> int:
        """
        Get the front element.
        """
        ans = self.pop()
        self.stack2.append(ans)
        return ans


    def empty(self) -> bool:
        """
        Returns whether the queue is empty.
        """
        return len(self.stack1) == 0 and len(self.stack2) == 0
```

3. 链表实现

```
#include<iostream>
using namespace std;
struct Node {
	Node* next;
	int data;
};
struct Queue {
	Node* front;
	Node* rear;
};

void CreateQueue(Queue &q) {
	Node* n;
	n = new Node;
	n->next = nullptr;
	q.front = n;
	q.rear = n;
}

int DeQueue(Queue& q) {
	Node* p = q.front;
	int data = p->next->data;
	q.front = p->next;
	return data;
}
bool IsEmpty(Queue& q) {
	return q.front == q.rear;
}

void EnQueue(Queue& q, int data) {
	Node* n = new Node;
	n->next = nullptr;
	n->data = data;
	q.rear->next = n;
	q.rear = n;
}

int main() {
	Queue* q = new Queue;
	CreateQueue(*q);
	cout << IsEmpty(*q) << endl;
	EnQueue(*q, 5);
	EnQueue(*q, 10);
	cout << IsEmpty(*q) << endl;
	cout << DeQueue(*q)<< endl; 
	cout << DeQueue(*q) << endl;
	cout << IsEmpty(*q) << endl;
	return 0;
}
```


### 循环队列

循环队列中有两种方法判断，第一是在数组中少用一个元素空间来判断队列满或者空，第二是采用额外的一个标记，例如当flag=0 and rear == front时为空， flag=1 and rear==front时为满。

```
// 循环队列
#include<iostream>
using namespace std;
const int Maxn = 3;
struct CQueue {
	int front;
	int rear;
	int arr[Maxn];
};
void CreateQueue(CQueue& q) {
	q.front = 0;
	q.rear = 1;
}
bool IsFull(CQueue& q) {
	return q.rear == q.front;
}
void EnQueue(CQueue& q, int n) {
	if (IsFull(q)) {
		cout << "Queue is full" << endl;
		exit(EXIT_FAILURE);
	}else {
		q.arr[q.rear] = n;
		q.rear = (q.rear+1) % Maxn;
	}
}
bool IsEmpty(CQueue& q) {
	return (q.front+1) == q.rear;
}
int DeQueue(CQueue& q) {
	if (IsEmpty(q)) {
		cout << "Queue is empty" << endl;
		exit(EXIT_FAILURE);
	}else {
		q.front = (q.front + 1) % Maxn;
		int ans = q.arr[q.front];
		return ans;
	}
}
int main() {
	CQueue* q = new CQueue();
	CreateQueue(*q);
	EnQueue(*q, 3);
	EnQueue(*q, 5);
	cout << DeQueue(*q) << endl;
	EnQueue(*q, 7);
	EnQueue(*q, 9);
	return 0;
}
```

### 优先队列

优先队列实现基于**堆**， 目前暂时省略。