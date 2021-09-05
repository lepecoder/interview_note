## forward_list

forward_list 是 [C++](http://c.biancheng.net/cplus/) 11 新添加的一类容器，其底层实现和 list 容器一样，采用的也是链表结构，只不过 forward_list 使用的是单链表，而 list 使用的是双向链表，所以说forward_list是高效的链表。

![单链表和双向链表](http://c.biancheng.net/uploads/allimg/191219/2-191219135239561.gif)

### 特殊的成员函数

| 成员函数       | 功能                                                   |
| -------------- | ------------------------------------------------------ |
| front()        |                                                        |
| assign()       |                                                        |
| insert_after() | 在指定位置之后插入一个新元素，并返回指向新元素的迭代器 |
| erase_after()  | 删除容器中某个指定位置或区域的元素                     |
| splice_after() | 将某个forward_list()中的一个区域插入到另一个容器       |