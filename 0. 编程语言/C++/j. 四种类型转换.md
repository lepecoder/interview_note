| 关键字           | 说明                                                                                                                          |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| static_cast      | 用于良性转换，一般不会导致意外发生，风险很低。                                                                                |
| const_cast       | 用于 const 与非 const、volatile 与非 volatile 之间的转换。                                                                    |
| reinterpret_cast | 高度危险的转换，这种转换仅仅是对二进制位的重新解释，不会借助已有的转换规则对数据进行调整，但是可以实现最灵活的 C++ 类型转换。 |
| dynamic_cast     | 借助 RTTI，用于类型安全的向下转型（Downcasting）。                                                                            |

这四个关键字的语法格式都是一样的，具体为：

```cpp
xxx_cast<newType>(data)
```

## static_cast 关键字

static_cast 只能用于良性转换，这样的转换风险较低，一般不会发生什么意外，例如：* 原有的自动类型转换，例如 short 转 int、int 转 double、const 转非 const、向上转型等；

* void [指针](http://c.biancheng.net/c/80/)和具体类型指针之间的转换，例如`void *`转`int *`、`char *`转`void *`等；
* 有转换构造函数或者类型转换函数的类与其它类型之间的转换，例如 double 转 Complex（调用转换构造函数）、Complex 转 double（调用类型转换函数）。

## const_cast

const_cast 比较好理解，它用来去掉表达式的 const 修饰或 volatile 修饰。换句话说，const_cast 就是用来将 const/volatile 类型转换为非 const/volatile 类型。

## reinterpret_cast

reinterpret 是“重新解释”的意思，顾名思义，reinterpret_cast 这种转换仅仅是对二进制位的重新解释，不会借助已有的转换规则对数据进行调整，非常简单粗暴，所以风险很高。

只是*二进制位*的重新解释，虽然可以实现各种转换，但可能出现奇怪的问题。、

## dynamic_cast

Run-Time Type Identification

通过运行时类型信息程序能够使用[基类](https://baike.baidu.com/item/%E5%9F%BA%E7%B1%BB/9589663)的[指针](https://baike.baidu.com/item/%E6%8C%87%E9%92%88/2878304)或引用来检查这些指针或引用所指的对象的实际[派生类](https://baike.baidu.com/item/%E6%B4%BE%E7%94%9F%E7%B1%BB)型。

相比C风格的强制类型转换和C++ reinterpret_cast，dynamic_cast提供了类型安全检查，是一种基于能力查询(Capability Query)的转换，所以在多态类型间进行转换更提倡采用dynamic_cast。

dynamic_cast运算符的主要用途：将基类的指针或引用安全地转换成派生类的指针或引用，

并用派生类的指针或引用调用非虚函数。如果是基类指针或引用调用的是虚函数无需转换就能在运行时调用派生类的虚函数。

前提条件：当我们将dynamic_cast用于某种类型的指针或引用时，只有该类型至少含有虚函数时(最简单是基类析构函数为虚函数)，才能进行这种转换。否则，编译器会报错。