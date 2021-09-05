### 右值引用

**右值引用的目的：**右值引用弥补了C++在移动语义上的缺失，它支持资源的转移。

---

在 C++11 之前，通过符合 & 和 const 的两种组合，可以覆盖三种场景：

1. non-const lvalue reference

* 比如： Foo foo(10); Foo& ref = foo;

2. const lvalue reference

* 比如： Foo foo(10); const Foo& ref = foo;

3. const rvalue reference

* 比如： const Foo& ref = Foo(10);

但对于 non-const rvalue reference 无法表达。

右值引用的实现：就相当于一个左值引用指向它的临时变量。

### move语义

对于 move 语意的急迫需求，到了 C++11 终于被引入。其直接的驱动力很简单：在构造或者赋值时， 如果等号右侧是一个中间临时对象，应直接将其占用的资源直接 move 过来（对方就没有了）。

C++11引入了右值引用的概念，以表示一个本应没有名称的临时对象。右值引用的声明与左值引用类似，但是它使用的是 2 个 `&` 符号（&&)，不能取地址的就是右值。

传统的引用是将表达式关联到左值，右值引用`&&`可以关联的右值，将右值与特定的地址关联就可以。

左值指既能够出现在等号左边，也能出现在等号右边的变量；右值则是只能出现在等号右边的变量。

* 左值是可寻址的变量，有持久性，在内存中有确定位置。
* 右值一般是不可寻址的临时变量

左值：指表达式结束后依然存在的持久对象。

右值：表达式结束就不再存在的临时对象。

左值和右值的区别：左值持久，右值短暂

右值引用和左值引用的区别：

左值引用不能绑定到要转换的表达式、字面常量或返回右值的表达式。右值引用恰好相反，可以绑定到这类表达式，但不能绑定到一个左值上。
右值引用必须绑定到右值的引用，通过 && 获得。右值引用只能绑定到一个将要销毁的对象上，因此可以自由地移动其资源。

主要区别之一是左值可修改，右值不能。

```cpp
int x = 6; // x是左值，6是右值
int &y = x; // 左值引用，y引用x

int &z1 = x * 6; // 错误，x*6是一个右值
const int &z2 =  x * 6; // 正确，可以将一个const引用绑定到一个右值

int &&z3 = x * 6; // 正确，右值引用
int &&z4 = x; // 错误，x是一个左值
```

以下代码使用了右值引用打印了两次 5 的平方：

```cpp
int square(int a){
    return a * a;
}
int main(){
    int x = 0; // 1
    x = 12; // 2
    cout << x << endl; // 3
    x = square(5); // 4 square(5)是一个右值，可以被赋值给左值
    int && rRef = square(5);
    cout << rRef << endl;

    return 0;
}
```

声明一个右值引用，给一个临时内存位置分配一个名称，这使得程序的其他部分访问该内存位置成为了可能，并且可以将这个临时位置变成一个左值。

和前面将square(5)赋值给x相比，使用右值引用减少了一次拷贝构造函数的消耗。

一个右值引用本质上是左值，【出现在等号左边】

```cpp
void foo(int &)  { std::cout << "lvalue" << std::endl; }
void foo(int &&) { std::cout << "rvalue" << std::endl; }

int main() {
  int &&rref = 1;
  foo(rref);    // foo(int &)
}
```

变量 rref 其实是一个左值，它的类型是 int 的右值引用 (int&&)，它绑定到一个右值（字面量1），但它本身是一个左值。

右值引用和相关的移动语义是C++11标准中引入的最强大的特性之一，通过std::move()可以避免无谓的复制，提高程序性能。

`&&` 只能引用右值。

`std::move`的作用是将左值转换为右值

### 移动语义

早期如果想用其它对象初始化一个同类的新对象，只能借助类中的复制（拷贝）构造函数。当类中拥有指针类型的成员变量时拷贝该指针成员本身的同时，还要拷贝指针指向的内存资源。否则一旦多个对象中的指针成员指向同一块堆空间，这些对象析构时就会对该空间释放多次，这是不允许的。

```cpp
class demo{
public:
   demo():num(new int(0)){
      cout<<"construct!"<<endl;
   }
   //拷贝构造函数
   demo(const demo &d):num(new int(*d.num)){
      cout<<"copy construct!"<<endl;
   }
   //添加移动构造函数，参数指向变为NULL
   demo(demo &&d):num(d.num){
        d.num = NULL;
        cout<<"move construct!"<<endl;
    }
   ~demo(){
      cout<<"class destruct!"<<endl;
   }
private:
   int *num;
};
```

常规复制构造函数使用const 左值引用作为参数，可以实现深拷贝。

避免了移动原始数据，而只是修改记录。比如一个函数返回了函数内的临时对象，此时在用返回值初始化对象是可以使用移动构造函数。

移动构造函数使用右值引用作为参数，只调整记录，不需要重新分配内存，右值引用不能是const的，通常将其他对象（通常是临时对象）拥有的内存资源“移为已用”。

### 完美转发

什么是完美转发问题？当我们想要包装一个函数调用时，就会遇到完美转发问题。例如，我们有一个函数 `bar`，它什么也不干，就是包装一下 `foo` 的调用：

```cpp
void foo(int &)  { std::cout << "lvalue" << std::endl; }
void foo(int &&) { std::cout << "rvalue" << std::endl; }
void bar(int &i)  { foo(i); }
void bar(int &&i) { foo(std::move(i)); }  // 注意这里的move是必须的，不然会匹配 `foo(int&)`，i是一个左值还记得吗？
```

这里，我们将参数`i`“完美” 地转发给了函数 foo，
也就是当实参是左值时，我们将左值转发给foo；
当实参是右值时，我们将右值转发给foo。

所以准确一点地说，完美转发就是将函数实参以其原本的值类别转发出去。当我们将一个右值引用传入函数时，他在实参中有了命名，所以继续往下传或者调用其他函数时，根据C++ 标准的定义，这个参数变成了一个左值。那么他永远不会调用接下来函数的右值版本，

在代码第4行，如果没有`move`，bar就会把右值参数`i`按照左值转发出去。

`std::forward`可以保留原来参数引用类型，只有当参数绑定到右值时才转换它的参数到右值

```cpp
void foo(const int &)  { std::cout << "lvalue" << std::endl; }
void foo(const int &&) { std::cout << "rvalue" << std::endl; }

int main() {
  int i = 1;
  foo(std::forward<int>(i));     // output: rvalue; forward lvalue -> rvalue
  foo(std::forward<int&>(i));    // output: lvalue; forward lvalue -> lvalue
  foo(std::forward<int&&>(i));   // output: rvalue; forward lvalue -> rvalue
  foo(std::forward<int>(1));     // output: rvalue; forward rvalue -> rvalue
  foo(std::forward<int&>(1));    // error: static_assert failed due to requirement '!is_lvalue_reference<int &>::value' "can not forward an rvalue as an lvalue"
  foo(std::forward<int&&>(1));   // output: rvalue; forward rvalue -> rvalue
}
```

## 速亡值

如果我们想把一个左值使用移动构造函数该怎么办?

可以使用类型转换 `static_cast<T&&>(expr)`，比如

```cpp
Foo   foo{10};
Foo&& ref = Foo{10};

Foo obj1 = static_cast<Foo&&>(foo); // move 构造
Foo obj2 = static_cast<Foo&&>(ref); // move 构造
```

这个表达式`static_cast<Foo&&>(foo)`是右值，但他的返回类型是一个引用，而引用不是右值。

对于这种既有左值特征，又和右值临时对象一样，可以用来初始化右值引用类型的变量的表达式，只能将其归为新的类别。C++11 给这个新类别 命名为 **速亡值** (eXpiring value，简称 xvalue)。 而将原来的 **右值** ，重新命名为 **纯右值** 。 而 **速亡值** 和 **纯右值** 合在一起，称为 **右值** ，其代表的含义是，所有可以直接用来初始化 **右值引用类型变量** 的表达式。