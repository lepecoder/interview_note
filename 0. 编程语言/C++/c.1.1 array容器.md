## array容器

> array 容器是 [C++](http://c.biancheng.net/cplus/) 11 标准中新增的序列容器，简单地理解，它就是在 C++ 普通数组的基础上，添加了一些成员函数和全局函数。在使用上，它比普通数组更安全（原因后续会讲），且效率并没有因此变差。
>

array定义在命名空间std里

```cpp
namespace std{
    template <typename T, size_t N>
    class array;
}
```

和普通数组一样，array声明大小必须使用常量

```cpp
std::array<double,10> arr;
```

除了常规成员函数，array还有一些特殊的成员函数

| 成员函数        | 功能                                   |
| --------------- | -------------------------------------- |
| at(n)           | 返回数组n位置的引用，有越界检查        |
| front()         | 返回第一个元素的引用，不能用于空容器   |
| back()          | 返回最后一个元素的引用，不能用于空     |
| data()          | 返回指向第一个元素的指针               |
| fill(val)       | 将val赋值给容器的每个元素              |
| arr1.swap(arr2) | 交换两个数组，前提是他们类型和长度一样 |

使用data获取array首个元素的指针

```
#include <iostream>
#include <array>
using namespace std;
int main()
{
    array<int, 5> words{1,2,3,4,5};
    cout << *( words.data()+1);
    return 0;
}
```