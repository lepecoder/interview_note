## string类和标准模板库

## 标准模板库

标准模板库提供了一系列的表示容器、迭代器、函数对象和算法的模板。

### 容器

与数组类型，可以存储一些同质的单元

#### vector

矢量容器类，动态内存分配

用法查手册就行

#### array

#### 迭代器

遍历容器对象

比如.begin()返回容器头部的迭代器，迭代器的类型是`iterator`，比如可以做如下声明

```cpp
vector<double>::iterator pd;  
```

#### 函数对象

可以是类对象或函数指针

### 智能指针

在智能指针过期时，自动销毁占用的内存空间。要创建智能指针，需要包含头文件\<memtory>，