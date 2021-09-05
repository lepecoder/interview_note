## 概述

友元提供了一种权限，使得普通函数可以访问类中的私有或保护成员。也就是说有两种形式的友元：

（1）友元函数：普通函数对一个访问某个类中的私有或保护成员。

（2）友元类：类A中的成员函数访问类B中的私有或保护成员

优点：提高了程序的运行效率。

缺点：破坏了类的封装性和数据的透明性。

总结：

* 能访问私有成员
* 破坏封装性
* 友元关系不可继承
* 友元关系的单向性
* 友元声明的形式及数量不受限制

需要在`tv`类中声明友元类`friend class Remote;`此时`remote`类里可以访问tv类，由于remote类里提到了tv类，必须将tv类的声明放到remote类之前。

我们也可以将类中的某个函数声明为友元成员函数，此时tv里需要定义remote的友元函数，remote里又提到了tv的对象，就会造成`循环依赖`的问题，为了解决这个问题，我们需要前向声明`forward declaration`，

```cpp
class A{
public:
    A(int _a):a(_a){};
    friend int geta(A &ca);  ///< 友元函数
private:
    int a;
};

int geta(A &ca) {  // 如果不是友元，则geta没有权限访问成员变量a
    return ca.a;
}

int main(){
    A a(3);  
    cout<<geta(a)<<endl;
    return 0;
}

```

友元类

```cpp
class A{
public:
    A(int _a):a(_a){};
    friend class B;
private:
    int a;
};

class B{
public:
    int getb(A ca) {
        return  ca.a; 
    };
};

int main() {
    A a(3);
    B b;
    cout<<b.getb(a)<<endl;
    return 0;
}
```