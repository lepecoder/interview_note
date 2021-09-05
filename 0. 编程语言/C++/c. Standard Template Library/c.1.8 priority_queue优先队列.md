## priority_queue优先队列

每个 priority_queue 容器适配器在创建时，都制定了一种排序规则。根据此规则，该容器适配器中存储的元素就有了优先级高低之分，所以优先队列中也只有值的概念，根据值的大小排序。

priority_queue 容器适配器为了保证每次从队头移除的都是当前优先级最高的元素，每当有新元素进入，它都会根据既定的排序规则找到优先级最高的元素，并将其移动到队列的队头；同样，当 priority_queue 从队头移除出一个元素之后，它也会再找到当前优先级最高的元素，并将其移动到队头。

priority_queue底层默认采用vector容器，排序方式是std::less\<T>的方法.

可以为priority_queue指定底层容器和排序规则

```cpp
int values[]{ 4,1,2,3 };
std::priority_queue<int, std::deque<int>, std::greater<int> >copy_values(values, values+4);//{1,3,2,4}
```

自定义排序规则：

```
//函数对象类
template <typename T>
class cmp
{
public:
    //重载 () 运算符
    bool operator()(T a, T b)
    {
        return a > b;
    }
};
int main()
{
    int a[] = { 4,2,3,5,6 };
    priority_queue<int,vector<int>,cmp<int> > pq(a,a+5);
    while (!pq.empty())
    {
        cout << pq.top() << " ";
        pq.pop();
    }
    return 0;
}
//
        auto cmp = [](const pair<int, int>& a, const pair<int, int>& b) -> bool { return a.second < b.second; };
        priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(cmp)> que(cmp);
```