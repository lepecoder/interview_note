## Standard Template Library

STL六大部件

- 容器Containers
- 分配器Allocators
- 算法Algorithms
- 迭代器Iterators
- 适配器Adapters
- 仿函数Functors

STL提供3类标准容器，分别是序列容器(顺序容器)，关联容器和排序关联容器。其中无序关联容器使用hash方式查找键，可以做到O(1)的时间复杂度。

### 顺序容器

- array    静态连续数组
- vector  动态连续数组
- deque  双端队列
- forward_list  单链表
- list       双链表

### 关联容器-按照键排序

- set       集合，集合中的元素不重复
- map     映射
- multiset  集合，集合中的元素允许相同
- multimap 映射，允许键重复

### 无序关联容器

- unordered_set  按照键生成散列
- unordered_map
- unordered_multiset
- unordered_multimap

### 容器适配器

同样是顺序容器，但是提供特殊的接口

- stack   栈，提供先进后出的特性
- queue  队列，提供先进先出特性
- priority_queue  优先队列，

### 迭代器

每种类型都有自己对应的迭代器，通常可以按功能分为输入迭代器、输出迭代器、前向迭代器、双向迭代器、随机访问迭代器。

输入输出迭代器不是把容器当做操作对象，而是把输入输出流作为操作对象。

- 前向迭代器：支持++操作，*取值操作，==运算符，两个同类迭代器可以相互赋值
- 双向迭代器：支持++,--
- 随机访问迭代器：可以每次移动多个元素`p+=i`或是`p[i]`，支持比较运算符`<`,`>`。

| 容器                               | 对应的迭代器类型 |
| ---------------------------------- | ---------------- |
| array                              | 随机访问迭代器   |
| vector                             | 随机访问迭代器   |
| deque                              | 随机访问迭代器   |
| list                               | 双向迭代器       |
| set / multiset                     | 双向迭代器       |
| map / multimap                     | 双向迭代器       |
| forward_list                       | 前向迭代器       |
| unordered_map / unordered_multimap | 前向迭代器       |
| unordered_set / unordered_multiset | 前向迭代器       |
| stack                              | 不支持迭代器     |
| queue                              | 不支持迭代器     |

#### 迭代器定义方式

| 迭代器定义方式 | 具体格式                                     |
| -------------- | -------------------------------------------- |
| 正向迭代器     | 容器类名::iterator  迭代器名;                |
| 常量正向迭代器 | 容器类名::const_iterator  迭代器名;          |
| 反向迭代器     | 容器类名::reverse_iterator  迭代器名;        |
| 常量反向迭代器 | 容器类名::const_reverse_iterator  迭代器名； |

对正向迭代器`++`时会指向后一个元素，对反向迭代器++会指向前一个元素，一般`rbegin()`返回反向迭代器。