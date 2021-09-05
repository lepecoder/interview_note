## 线程库thread

`std::thread` 对象也可能处于不表示任何线程的状态（默认构造、被移动、 [detach](https://www.apiref.com/cpp-zh/cpp/thread/thread/detach.html "cpp/thread/thread/detach") 或 [join](https://www.apiref.com/cpp-zh/cpp/thread/thread/join.html "cpp/thread/thread/join") 后），并且执行线程可能与任何 `thread` 对象无关（ [detach](https://www.apiref.com/cpp-zh/cpp/thread/thread/detach.html "cpp/thread/thread/detach") 后）。

没有两个 `std::thread` 对象会表示同一执行线程。

`joinable`检查thread是否活跃的执行线程，但是当前线程不能和自己结合，join()之后也是不可结合的。

`join`是阻塞的等待线程结束，而`detach`是从thread对象中分离线程，允许线程独立的执行结束。

## 互斥锁

`mutex`基本互斥锁，

## 通用互斥管理

## 单次调用

## 条件变量

## 信号量

## Future