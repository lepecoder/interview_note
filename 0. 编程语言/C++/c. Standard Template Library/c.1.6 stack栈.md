## stack栈

stack和queue称为容器适配器，它实际上是在序列容器的基础上进行了封装，实现Last-in-First-out。

因为deque可以在两端插入删除，所以能够实现stack和queue需要的功能。

| 成员函数           | 功能                                                                                                            |
| ------------------ | --------------------------------------------------------------------------------------------------------------- |
| empty()            | 当 stack 栈中没有元素时，该成员函数返回 true；反之，返回 false。                                                |
| size()             | 返回 stack 栈中存储元素的个数。                                                                                 |
| top()              | 返回一个栈顶元素的引用，类型为 T&。如果栈为空，程序会报错。                                                     |
| push(const T& val) | 先复制 val，再将 val 副本压入栈顶。这是通过调用底层容器的 push_back() 函数完成的。                              |
| push(T&& obj)      | 以移动元素的方式将其压入栈顶。这是通过调用底层容器的有右值引用参数的 push_back() 函数完成的。                   |
| pop()              | 弹出栈顶元素。                                                                                                  |
| emplace(arg...)    | arg... 可以是一个参数，也可以是多个参数，但它们都只用于构造一个对象，并在栈顶直接生成该对象，作为新的栈顶元素。 |