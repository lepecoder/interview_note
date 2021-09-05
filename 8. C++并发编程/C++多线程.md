### 概述

线程是轻量级的进程，在Linux环境下线程的本质仍然是进程，操作系统以进程为单位分配系统资源，线程共享进程下的资源。

**进程和线程的区别：**

* 进程有独立的地址空间，多个线程共用一个地址空间。
  * 线程独享自己的栈和寄存器
  * 线程共享代码段、堆、全局数据区、文件描述符等
* 线程是程序的最小执行单位，进程是操作系统中最小的资源分配单位
  * 每个进程对应一个虚拟地址空间，一个进程只能抢一个 CPU 时间片
  * 一个地址空间中可以划分出多个线程，在有效的资源基础上，能够抢更多的 CPU 时间片
* 切换同一进程下不同的线程对CPU的开销小

进程同步方法：管道、IO重定向、套接字、<u>共享内存</u>、消息队列。

线程同步方法：互斥锁、信号量、条件变量。

### 线程创建

```cpp
#include <thread>
// 构造函数
explicit thread( Function&& f, Args&&... args );
// 得到线程ID
thread t1(func1);
t1.get_id();
// 可以使用this_thread命名空间得到当前线程的ID
this_thread::get_id();
// 等待线程结束
t1.join();
// 分离线程，自行释放资源
t1.detach();
```

### 线程结束

**线程终止并不代表释放线****程占用的资源**
线程可以自动运行结束退出，也可以调用`pthread_exit(void *value_ptr)`退出
参数`value_ptr`是向父进程返回的参数，父进程可以通过`pthread_join(pthread_t thread_id, void ** value_ptr)`接收。

线程的资源不会随着调用`pthread_exit()`释放，

*那什么时候释放资源呢??*

**注意**

如果用pthread_exit()返回的数据在子线程的栈区，那么在`join`接收时栈区的资源会被回收。

位于同一虚拟地址空间中的线程，虽然不能共享栈区数据，但是可以共享全局数据区和堆区数据，因此在子线程退出的时候可以将传出数据存储到全局变量、静态变量或者堆内存中。

但是存储到**全局变量**可能导致多个子线程同时写的问题。实际上只会获取到之后一个线程的数据。

使用**主线程的栈空间**，由于主线程是最后退出的，所以可以将需要返回的数据存储在传入的参数中。

**线程正常终止的方法：**

1、return从线程函数返回。

2、通过调用函数pthread_exit使线程退出

3、线程可以被同一进程中的其他线程取消

**主线程、子线程调用exit， pthread_exit，互相产生的影响。**

1、在主线程中，在main函数中return了或是调用了exit函数，则主线程退出，且整个进程也会终止，此时进程中的所有线程也将终止。因此要避免main函数过早结束。

2、在主线程中调用pthread_exit,   则仅仅是主线程结束，进程不会结束，进程内的其他线程也不会结束，直到所有线程结束，进程才会终止。

3、在任何一个线程中调用exit函数都会导致进程结束。进程一旦结束，那么进程中的所有线程都将结束。

终止的线程所占用的资源不会随着线程的结束而归还系统，而是仍为线程所在的进程持有。在Linux中，默认情况下是在一个线程被创建后，必须使用此函数对创建的线程进行资源回收，但是可以设置Threads attributes来设置当一个线程结束时，直接回收此线程所占用的系统资源，详细资料查看Threads attributes。比如可以用detach();

### 线程取消

1. 在线程 A 中调用线程取消函数 `pthread_cancel`，指定杀死线程 B，这时候线程 B 是死不了的
2. 在线程 B 中进程一次系统调用（从用户区切换到内核区），否则线程 B 可以一直运行。

### 线程同步

多个线程访问同一个共享资源时，为了防止自己的修改被覆盖，或是读到的数据以过时，需要进程线程同步。

#### 互斥锁

被互斥锁锁住的代码块，所有线程只能按顺序执行，Linux中互斥锁的类型是`pthread_mutex_t mutxt`

在创建的锁对象中保存了当前这把锁的状态信息：锁定还是打开，如果是锁定状态还记录了给这把锁加锁的线程信息（线程 ID）。一个互斥锁变量只能被一个线程锁定，被锁定之后其他线程再对互斥锁变量加锁就会被阻塞，直到这把互斥锁被解锁，被阻塞的线程才能被解除阻塞。`一般情况下，每一个共享资源对应一个把互斥锁，锁的个数和线程的个数无关。`

```cpp
// 初始化互斥锁
// restrict: 是一个关键字, 用来修饰指针, 只有这个关键字修饰的指针可以访问指向的内存地址, 其他指针是不行的
int pthread_mutex_init(pthread_mutex_t *restrict mutex,
           const pthread_mutexattr_t *restrict attr);
// attr是互斥锁的属性，一般为空即可
// 释放互斥锁资源          
int pthread_mutex_destroy(pthread_mutex_t *mutex);
// 修改互斥锁的状态, 将其设定为锁定状态, 这个状态被写入到参数 mutex 中
// 锁定失败的线程会被阻塞，直到其它线程解锁
int pthread_mutex_lock(pthread_mutex_t *mutex);
// 尝试加锁，如果mutex已经被锁住了，这个线程不会被阻塞，直接返回错误号
int pthread_mutex_trylock(pthread_mutex_t *mutex);

```

C++风格的互斥锁

```cpp
#include <thread>
#include <mutex>
// 初始化互斥锁
std::mutex mtx;
void fun(){
    mtx.lock();  // 加锁
    mtx.unlock();  // 去锁
}
std::thread t1(fun), t2(fun);
t1.join();
t2.join();
```

#### 读写锁

读写锁是一种类型，读操作是并行的，写操作是互斥的；总的来说，多个读操作可以同时进行，写操作只能独占资源。写锁比读锁的优先级更高。

```cpp
// 创建读写锁
pthread_rwlock_t rwlock;
// 初始化读写锁
int pthread_rwlock_init(pthread_rwlock_t *restrict rwlock,
           const pthread_rwlockattr_t *restrict attr);
// 释放读写锁占用的系统资源
int pthread_rwlock_destroy(pthread_rwlock_t *rwlock);
// 在程序中对读写锁加读锁, 锁定的是读操作
int pthread_rwlock_rdlock(pthread_rwlock_t *rwlock);
// 这个函数可以有效的避免死锁
// 如果加读锁失败, 不会阻塞当前线程, 直接返回错误号
int pthread_rwlock_tryrdlock(pthread_rwlock_t *rwlock);
// 在程序中对读写锁加写锁, 锁定的是写操作
int pthread_rwlock_wrlock(pthread_rwlock_t *rwlock);
// 这个函数可以有效的避免死锁
// 如果加写锁失败, 不会阻塞当前线程, 直接返回错误号
int pthread_rwlock_trywrlock(pthread_rwlock_t *rwlock);
// 解锁, 不管锁定了读还是写都可用解锁
int pthread_rwlock_unlock(pthread_rwlock_t *rwlock);
```

### 条件变量

与互斥锁不同，条件变量是用来等待而不是用来上锁的。条件变量用来自动阻塞一个线程，直到某特殊情况发生为止。通常条件变量和互斥锁同时使用。条件的检测是在互斥锁的保护下进行的。如果一个条件为假，一个线程自动阻塞，并释放等待状态改变的互斥锁。如果另一个线程改变了条件，它发信号给关联的条件变量，唤醒一个或多个等待它的线程，重新获得互斥锁，重新评价条件。

一般情况下条件变量用于处理生产者和消费者模型，比如消费者在没有产品时阻塞，生产者在产生产品时用条件变量通知消费者。条件变量类型对应的类型为 `pthread_cond_t`，严格意义上来说，条件变量的主要作用不是处理线程同步，`而是进行线程的阻塞。`如果在多线程程序中只使用条件变量无法实现线程的同步，必须要配合互斥锁来使用。

```cpp
// 创建条件变量
pthread_cond_t cond;
// 初始化
int pthread_cond_init(pthread_cond_t *restrict cond,
      const pthread_condattr_t *restrict attr);
// 销毁释放资源      
int pthread_cond_destroy(pthread_cond_t *cond);
// 线程阻塞函数, 哪个线程调用这个函数, 哪个线程就会被阻塞
int pthread_cond_wait(pthread_cond_t *restrict cond, pthread_mutex_t *restrict mutex);
```

`pthread_cond_wait`在阻塞线程时需要mutex参数，这个互斥锁的功能是进行线程同步，让线程顺序进入临界区，避免出现数共享资源的数据混乱。该函数会对这个互斥锁做以下几件事情：

1. 在阻塞线程后，如果`mutex`上锁，那么为了避免死锁，会把这个锁打开。
2. 当线程解除阻塞的时候，函数内部会帮助这个线程再次将这个 `mutex` 互斥锁锁上，继续向下访问临界区。

```cpp
// 唤醒阻塞在条件变量上的线程, 至少有一个被解除阻塞
int pthread_cond_signal(pthread_cond_t *cond);
// 唤醒阻塞在条件变量上的线程, 被阻塞的线程全部解除阻塞
int pthread_cond_broadcast(pthread_cond_t *cond);
```

### 信号量

信号量用在多线程多任务同步的，一个线程完成了某一个动作就通过信号量告诉别的线程，别的线程再进行某些动作。信号量不一定是锁定某一个资源，而是流程上的概念，比如：有 A，B 两个线程，B 线程要等 A 线程完成某一任务以后再进行自己下面的步骤，这个任务并不一定是锁定某一资源，还可以是进行一些计算或者数据处理之类。

`信号量（信号灯）`与互斥锁和条件变量的主要不同在于” 灯” 的概念，灯亮则意味着资源可用，灯灭则意味着不可用。信号量主要阻塞线程，不能完全保证线程安全，如果要保证线程安全，需要信号量和互斥锁一起使用。

```cpp
#include <semaphore.h>
// 初始化信号量/信号灯
int sem_init(sem_t *sem, int pshared, unsigned int value);
// 资源释放, 线程销毁之后调用这个函数即可
// 参数 sem 就是 sem_init() 的第一个参数            
int sem_destroy(sem_t *sem);
// 参数 sem 就是 sem_init() 的第一个参数  
// 函数被调用sem中的资源就会被消耗1个, 资源数-1
int sem_wait(sem_t *sem);
// 调用该函数给sem中的资源数+1
int sem_post(sem_t *sem);
```

当线程调用这个函数，并且 `sem` 中的资源数 `>0`，线程不会阻塞，线程会占用 `sem` 中的一个资源，因此资源数 - 1，直到 `sem` 中的资源数减为 `0` 时，资源被耗尽，因此线程也就被阻塞了。