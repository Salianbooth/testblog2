---
title: 多线程和线程同步-C/C++
date: 2024-08-08 18:15:01
tags:
  - C
  - Cpp
  - Markdown
  - 多线程
categories:
  - self-learning
---

# Before

>在进行相应的学习之前，我们先搞清楚到底什么是线程，它和进程有什么关系，学习了它之后能有什么作用
### 线程概念

线程是轻量级的进程（LWP：light weight process），在Linux环境下线程的本质仍是进程。在计算机上运行的程序是一组指令及指令参数的组合，指令按照既定的逻辑控制计算机运行。操作系统会以进程为单位，分配系统资源，可以这样理解，***==进程是资源分配的最小单位，线程是操作系统调度执行的最小单位。==***

### 线程和进程的区别
当我们启动一个程序时，会自动的生成一个虚拟的地址空间
<img src="https://gitee.com/salianbooth/picture/raw/master/202408290940711.png" alt="image-20210130093015907.png" style="zoom:50%;" /> 

- 进程有自己独立的地址空间, 多个线程共用同一个地址空间
	- 线程更加节省系统资源, 效率不仅可以保持的, 而且能够更高
	- 在一个地址空间中多个线程独享: 每个线程都有属于自己的栈区, 寄存器(内核中管理的)
	- 在一个地址空间中多个线程共享: 代码段, 堆区, 全局数据区, 打开的文件(文件描述符表)都是线程共享的
- 线程是程序的最小执行单位, 进程是操作系统中最小的资源分配单位
	- 每个进程对应一个虚拟地址空间，一个进程只能抢一个CPU时间片
	- 一个地址空间中可以划分出多个线程, 在有效的资源基础上, 能够抢更多的CPU时间片


# 多线程
## 1.创建线程

每一个线程都有一个唯一的线程ID，ID类型为pthread_t，这个ID是一个无符号长整形数，如果想要得到当前线程的线程ID，可以调用如下函数：

```c
pthread_t pthread_self(void);	// 返回当前线程的线程ID
```

在一个进程中调用线程创建函数，就可得到一个子线程，和进程不同，需要给每一个创建出的线程指定一个处理函数，否则这个线程无法工作。

```c
#include <pthread.h>
int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
                   void *(*start_routine) (void *), void *arg);
// Compile and link with -pthread, 线程库的名字叫pthread, 全名: libpthread.so libptread.a
```


**参数:**

- thread: 传出参数，是无符号长整形数，线程创建成功, 会将线程ID写入到这个指针指向的内存中

- attr: 线程的属性, 一般情况下使用默认属性即可, 写NULL

- start_routine: 函数指针，创建出的子线程的处理动作，也就是该函数在子线程中执行。

- arg: 作为实参传递到 start_routine 指针指向的函数内部

返回值：线程创建成功返回0，创建失败返回对应的错误号

### 函数原型

```C
int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine)(void *), void *arg);
```

### 参数解释

1. **`pthread_t \*thread`**:

   - 这是一个指向 `pthread_t` 类型变量的指针，用于存储新创建的线程的标识符（线程ID）。
   - 在成功创建线程后，`pthread_create` 会通过这个指针返回新线程的ID。你可以通过这个ID在以后使用 `pthread_join` 或其他线程管理函数来控制该线程。

   **示例**:

   ```c
   pthread_t tid;
   pthread_create(&tid, NULL, working, NULL);
   ```

2. **`const pthread_attr_t \*attr`**:

   - 这是一个指向 `pthread_attr_t` 结构体的指针，用于指定新线程的属性。
   - 如果你想创建一个具有特定属性的线程（例如栈大小、优先级、分离状态等），可以在调用 `pthread_create` 之前初始化一个 `pthread_attr_t` 结构体并将其传递给此参数。
   - 如果你不需要指定任何特殊的线程属性，可以将此参数设置为 `NULL`，此时会使用默认的线程属性。

   **示例**:

   ```c
   pthread_attr_t attr;
   pthread_attr_init(&attr);           // 初始化属性
   pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED); // 设置线程为分离状态
   pthread_create(&tid, &attr, working, NULL);
   pthread_attr_destroy(&attr);        // 销毁属性对象
   ```

3. **`void \*(\*start_routine)(void \*)`**:

   - 这是一个函数指针，指向新线程要执行的函数。该函数必须符合以下签名：

     ```c
     void *function_name(void *arg);
     ```

   - 当线程被创建后，它会立即开始执行 `start_routine` 指向的函数。线程执行完成时，返回值将作为线程的退出状态传递给 `pthread_exit`，并且可以通过 `pthread_join` 获取。

   **示例**:

   ```c
   void *working(void *arg) {
       // 线程执行的任务
       return NULL;
   }
   pthread_create(&tid, NULL, working, NULL);
   ```

4. **`void \*arg`**:

   - 这是一个指向任意类型数据的指针，用作传递给 `start_routine` 函数的参数。在新线程开始执行时，`arg` 将作为参数传递给 `start_routine`。
   - 如果你的线程函数不需要参数，可以将此参数设置为 `NULL`。

   **示例**:

   ```c
   int data = 42;
   pthread_create(&tid, NULL, working, (void *)&data);
   ```

### 返回值

- **成功时返回 `0`**: 表示线程创建成功。
- **失败时返回错误码**: 表示线程创建失败。例如，`EAGAIN` 表示资源不足导致创建线程失败，`EINVAL` 表示传递的属性无效，`EPERM` 表示没有足够的权限。

## 2.线程退出

在编写多线程程序的时候，如果想要让线程退出，但是不会导致虚拟地址空间的释放（针对于主线程），我们就可以调用线程库中的线程退出函数，只要调用该函数当前线程就马上退出了，并且不会影响到其他线程的正常运行，不管是在子线程或者主线程中都可以使用。

```c
#include <pthread.h>
void pthread_exit(void *retval);

```


参数: 线程退出的时候携带的数据，当前子线程的主线程会得到该数据。如果不需要使用，指定为NULL
那么退出时传出的参数要如何使用呢？这就需要和我们接下来的线程回收函数配合使用

## 3.线程回收



线程和进程一样，**子线程退出的时候其内核资源主要由主线程回收**，线程库中提供的线程回收函叫做pthread_join()，这个函数是一个阻塞函数，如果还有子线程在运行，调用该函数就会阻塞，子线程退出函数解除阻塞进行资源的回收，函数被调用一次，只能回收一个子线程，如果有多个子线程则需要循环进行回收。

另外通过线程回收函数还可以获取到子线程退出时传递出来的数据，函数原型如下：

```c
#include <pthread.h>
// 这是一个阻塞函数, 子线程在运行这个函数就阻塞
// 子线程退出, 函数解除阻塞, 回收对应的子线程资源, 类似于回收进程使用的函数 wait()
int pthread_join(pthread_t thread, void **retval);

```

**参数:**

- thread: 要被回收的子线程的线程ID

- retval: 二级指针, 指向一级指针的地址, 是一个传出参数, 这个地址中存储了pthread_exit() 传递出的数据，如果不需要这个参数，可以指定为NULL

返回值：线程回收成功返回0，回收失败返回错误号。

### 回收子线程时数据缺失
>当我们想回收子线程时接受它传出的数据，但是数据存放在栈空间上，在子线程结束后会被回收，这里我们将用到2种解决方法
#### 方法一
将要传出的数据设为全局变量

```C
// pthread_create.c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <pthread.h>

struct Test
{
    int num;
    int age;
};

struct Test t;

void *working(void *arg)
{
    printf("我是子线程，线程ID：%ld\n", pthread_self());
    for (int i = 0; i < 9; ++i)
    {
        // if (i == 6)
        // {
        //     pthread_exit(NULL); // 直接退出子线程
        // }
        printf("child == i: = %d\n", i);
    }

    // 我们在这定义的t，此时此刻是在子线程所占用的堆内存上，这一块是线程共享的，当子线程退出时，会将占用的归还
    //  struct Test t;
    t.num = 100;
    t.age = 6;

    pthread_exit(&t);
    return NULL;
}

int main()
{
    pthread_t tid;
    // 创建了一个线程，为tid，属性为空，执行的函数为working，传入的参数为NULL
    pthread_create(&tid, NULL, working, NULL);

    printf("子线程创建成功，线程ID：%ld\n", tid);

    printf("我是主线程，线程ID：%ld\n", pthread_self());
    // for (int i = 0; i < 3; ++i)
    // {
    //     printf("i = %d\n", i);
    // }

    printf("===========主线程回收子线程========\n");
    void *ptr;
    // 由于exit传出的数据为一个二级指针，二级指针->指向一级指针的地址，所以我们这里用 &指针的地址来接收
    pthread_join(tid, &ptr);
    struct Test *pt = (struct Test *)ptr;
    printf("num: %d, age = %d\n", pt->num, pt->age);

    // 主线程调用退出函数退出, 地址空间不会被释放
    pthread_exit(NULL);

    // sleep(2);
    return 0;
}
```


#### 为什么需要分配在堆上？

1. **生命周期**：如果你希望 `t` 在函数返回后仍然有效，你就需要将其分配在堆上，因为栈上的内存会在函数返回时自动释放。
   
2. **内存大小**：栈的大小有限，如果你的结构体非常大或者需要动态分配大量结构体，栈可能无法满足需求。这时，使用堆可以避免栈溢出。
   
3. **动态分配**：如果需要在运行时动态决定分配多少个 `Test` 结构体（例如通过用户输入或配置文件），就需要使用堆分配。

### 方法二
将主线程上的变量传给子线程使用

```c
// pthread_create.c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <pthread.h>

struct Test
{
    int num;
    int age;
};
// struct Test t;

void *working(void *arg)
{
    printf("我是子线程，线程ID：%ld\n", pthread_self());
    for (int i = 0; i < 9; ++i)
    {
        // if (i == 6)
        // {
        //     pthread_exit(NULL); // 直接退出子线程
        // }
        printf("child == i: = %d\n", i);
    }

    // 我们在这定义的t，此时此刻是在子线程所占用的堆内存上，这一块是线程共享的，当子线程退出时，会将占用的归还
    //  struct Test t;
    // 同样，这里尽量别用static解决，这样会导致这一块内存收回不了
    // static struct Test t;

    // 这样我们通过主线程传入的 结构体 t，来进行相应的操作
    struct Test *t = (struct Test *)arg;
    t->num = 100;
    t->age = 6;

    pthread_exit(t);
    return NULL;
}

int main()
{
    // ---------------方法二----------
    struct Test t;
    pthread_t tid;
    // 创建了一个线程，为tid，属性为空，执行的函数为working，传入的参数为NULL
    pthread_create(&tid, NULL, working, &t);

    printf("子线程创建成功，线程ID：%ld\n", tid);

    printf("我是主线程，线程ID：%ld\n", pthread_self());
    // for (int i = 0; i < 3; ++i)
    // {
    //     printf("i = %d\n", i);
    // }

    // ----------方法一---------------
    printf("===========主线程回收子线程========\n");
    void *ptr;
    // 由于exit传出的数据为一个二级指针，二级指针->指向一级指针的地址，所以我们这里用 &指针的地址来接收
    pthread_join(tid, &ptr);
    struct Test *pt = (struct Test *)ptr;
    printf("num: %d, age = %d\n", pt->num, pt->age);

    // 主线程调用退出函数退出, 地址空间不会被释放
    pthread_exit(NULL);

    // sleep(2);
    return 0;
}
```

## 4.线程分离

>在某些情况下，程序中的主线程有属于自己的业务处理流程，如果让主线程负责子线程的资源回收，调用pthread_join()只要子线程不退出主线程就会一直被阻塞，主要线程的任务也就不能被执行了。

在线程库函数中为我们提供了线程分离函数pthread_detach()，调用这个函数之后指定的子线程就可以和主线程分离，当子线程退出的时候，其占用的内核资源就被系统的其他进程接管并回收了。线程分离之后在主线程中使用pthread_join()就回收不到子线程资源了。

```C
#include <pthread.h>
// 参数就子线程的线程ID, 主线程就可以和这个子线程分离了
int pthread_detach(pthread_t thread);
```

下面的代码中，在主线程中创建子线程，并调用线程分离函数，实现了主线程和子线程的分离：
```C++
// pthread_create.c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <pthread.h>

struct Test
{
    int num;
    int age;
};

void *working(void *arg)
{
    printf("我是子线程，线程ID：%ld\n", pthread_self());
    for (int i = 0; i < 9; ++i)
    {

        printf("child == i: = %d\n", i);
    }

    return NULL;
}

int main()
{
    // ---------------方法二----------
    struct Test t;
    pthread_t tid;
    // 创建了一个线程，为tid，属性为空，执行的函数为working，传入的参数为NULL
    pthread_create(&tid, NULL, working, &t);

    printf("子线程创建成功，线程ID：%ld\n", tid);

    printf("我是主线程，线程ID：%ld\n", pthread_self());
    // for (int i = 0; i < 3; ++i)
    // {
    //     printf("i = %d\n", i);
    // }
    pthread_detach(tid);

    // 主线程调用退出函数退出, 地址空间不会被释放
    pthread_exit(NULL);

    // sleep(2);
    return 0;
}
```

## 小剧场--线程分离和线程回收
在多线程编程中，“线程分离”和“线程回收”是两种管理线程生命周期的方式，它们处理线程结束后的资源管理问题。

### 线程分离（Detached Thread）
线程分离指的是将线程的资源自动释放，不需要主线程显式地等待和回收。

#### 主要特点：
1. **自动资源释放**：一旦线程执行完毕，系统会自动释放与该线程相关的资源，无需其他线程显式地调用 `pthread_join` 进行回收。
   
2. **无需等待**：主线程或其他线程不必等待分离线程结束，这样可以避免阻塞。

3. **不可回收**：一旦线程被分离（调用 `pthread_detach`），其他线程无法再通过 `pthread_join` 获取它的返回值或等待它的结束。

#### 使用场景：
- 适用于那些不需要与其他线程进行同步、不需要获取返回值、并且可以自行终止的线程。

#### 代码示例：
```c
pthread_t tid;
pthread_create(&tid, NULL, thread_function, NULL);
pthread_detach(tid);  // 将线程设为分离状态
```

### 线程回收（Joinable Thread）
线程回收是指显式等待一个线程结束，并获取它的返回值。

#### 主要特点：
1. **显式等待**：主线程或其他线程会调用 `pthread_join` 来等待目标线程结束。这会导致调用 `pthread_join` 的线程阻塞，直到目标线程完成。

2. **获取返回值**：可以通过 `pthread_join` 函数获取线程的退出状态或返回值。

3. **手动释放资源**：回收线程后，系统会释放与该线程相关的资源。

#### 使用场景：
- 当主线程需要等待子线程完成任务，或者需要从子线程获取返回值时，应该使用线程回收。

#### 代码示例：
```c
pthread_t tid;
pthread_create(&tid, NULL, thread_function, NULL);
void *retval;
pthread_join(tid, &retval);  // 等待线程结束并获取返回值
```

### 线程分离与回收的区别总结
- **控制权**：分离线程一旦设置为分离状态，就不再受其他线程的控制，结束后自动释放资源。回收线程则需要显式地通过 `pthread_join` 进行回收。
  
- **阻塞与非阻塞**：回收线程需要等待子线程结束，会导致主线程或调用线程阻塞。分离线程则无需等待，可以让主线程继续执行其他任务。

- **资源管理**：分离线程的资源由系统自动管理，回收线程则由程序员通过 `pthread_join` 进行管理。

### 什么时候选择线程分离？
- 当子线程不需要与主线程同步，并且不需要返回值时，使用分离线程可以减少系统资源的占用和管理复杂性。

### 什么时候选择线程回收？
- 当子线程完成的任务对主线程很重要，或者需要从子线程获取结果时，应该选择线程回收。

### 示例场景
- **线程分离**：启动一个后台日志记录线程，记录的工作与主程序无关，也不需要主线程等待其完成。
- **线程回收**：并行计算任务，主线程需要等待所有子线程计算完毕并汇总结果。

## 其他线程函数

## 1.线程取消
线程取消的意思就是在某些特定情况下在一个线程中杀死另一个线程。使用这个函数杀死一个线程需要分两步：

在线程A中调用线程取消函数pthread_cancel，指定杀死线程B，这时候线程B是死不了的
在线程B中进程一次系统调用（从用户区切换到内核区），否则线程B可以一直运行。


```C
#include <pthread.h>
// 参数是子线程的线程ID
int pthread_cancel(pthread_t thread);

```
- 参数：要杀死的线程的线程ID
- 返回值：函数调用成功返回0，调用失败返回非0错误号。

## 2.线程id比较
在Linux中线程ID本质就是一个无符号长整形，因此可以直接使用比较操作符比较两个线程的ID，但是线程库是可以跨平台使用的，在某些平台上 pthread_t可能不是一个单纯的整形，这中情况下比较两个线程的ID必须要使用比较函数，函数原型如下：

```C
#include <pthread.h>
int pthread_equal(pthread_t t1, pthread_t t2);
```

- 参数：t1 和 t2 是要比较的线程的线程ID
- 返回值：如果两个线程ID相等返回非0值，如果不相等返回0


# 线程同步

>假设有多个线程A,B，当A访问 共享资源 时，另一个线程B是不能对这块内存进行操作，直到A访问结束，B才能进行操作。线程对这种访问方式称为线程同步 。由此我们可以了解到 -> 线程同步不是指线程同时进行访问，而是依照先后顺序依次进行的。


## 1.Why ->为什么要线程同步
在此之前，我们先看这样一段代码，两个线程交替计数（各50次，总计100次）
```C
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <string.h>
#include <pthread.h>

#define MAX 50
// 全局变量
int number;

// 线程处理函数
void *funcA_num(void *arg)
{
    for (int i = 0; i < MAX; ++i)
    {
        int cur = number;
        cur++;
        usleep(10);
        number = cur;
        printf("Thread A, id = %lu, number = %d\n", pthread_self(), number);
    }

    return NULL;
}

void *funcB_num(void *arg)
{
    for (int i = 0; i < MAX; ++i)
    {
        int cur = number;
        cur++;
        number = cur;
        printf("Thread B, id = %lu, number = %d\n", pthread_self(), number);
        usleep(5);
    }

    return NULL;
}

int main(int argc, const char *argv[])
{
    pthread_t p1, p2;

    // 创建两个子线程
    pthread_create(&p1, NULL, funcA_num, NULL);
    pthread_create(&p2, NULL, funcB_num, NULL);

    // 阻塞，资源回收
    pthread_join(p1, NULL);
    pthread_join(p2, NULL);

    return 0;
}

```

根据上面的例子进行多次的测试，但得出的结果和预期（最终输出100）大不相同，那么这就是为什么呢？其原因就是没有对线程进行同步处理，导致了数据的混乱。

两个线程在数数的时候需要分时复用CPU时间片，并且测试程序中调用了sleep()导致线程的CPU时间片没用完就被迫挂起了，这样就能让CPU的上下文切换（保存当前状态, 下一次继续运行的时候需要加载保存的状态）更加频繁，更容易再现数据混乱的这个现象。

**通俗地讲，就是合作的双方没有沟通好，从而做了许多的重复工作**。

## 2.线程同步方式

对于多个线程访问共享资源出现数据混乱的问题，需要进行线程同步。常用的线程同步方式有四种：互斥锁、读写锁、条件变量、信号量。所谓的共享资源就是多个线程共同访问的变量，这些变量通常为全局数据区变量或者堆区变量，这些变量对应的共享资源也被称之为临界资源。


| ![Clip_2024-08-29_22-33-51.png](https://gitee.com/salianbooth/picture/raw/master/202408292233910.png) |
| :---------------------------------------------------------------------------------------------------: |
|                                               图片来源->大丙                                                |
找到临界资源之后，再找和临界资源相关的上下文代码，这样就得到了一个代码块，这个代码块可以称之为临界区。确定好临界区（临界区越小越好）之后，就可以进行线程同步了，线程同步的大致处理思路是这样的：
- 在临界区上面，添加加锁函数
	- 哪个进程调用这段代码，就会给临界区加锁，其他的进程就被阻塞到锁上
- 在临界区下面，添加解锁函数
	- 出了临界区的进程会把锁解开，这样其他函数就能进入到临界区了。

## 3.互斥锁
>互斥锁是线程同步最常用的一种方式，通过互斥锁可以锁定一个代码块, 被锁定的这个代码块, 所有的线程只能顺序执行(不能并行处理)，这样多线程访问共享资源数据混乱的问题就可以被解决了，需要付出的代价就是执行效率的降低，因为默认临界区多个线程是可以并行处理的，现在只能串行处理。

由定义我们可以得知，将共享资源块看成一个房子，当房子没有被锁的时候，有线程进去，该线程就会将房子锁住，其他的线程无法进入，当该线程出来时，它才会解锁，其他线程才可以进入。

### 1. 初始化互斥锁

```
int pthread_mutex_init(pthread_mutex_t *restrict mutex, const pthread_mutexattr_t *restrict attr);
```

- **作用**: 初始化一个互斥锁对象 `mutex`，使其准备好被使用。

- 参数

  :

  - `mutex`: 指向互斥锁变量的指针。
  - `attr`: 指向互斥锁属性的指针，如果不需要自定义属性，可以传 `NULL` 表示使用默认属性。

- **返回值**: 成功返回 `0`，失败返回错误号。

### 2. 销毁互斥锁

```
int pthread_mutex_destroy(pthread_mutex_t *mutex);
```

- **作用**: 销毁一个互斥锁对象并释放相关的资源。

- 参数

  :

  - `mutex`: 指向要销毁的互斥锁变量的指针。

- **返回值**: 成功返回 `0`，失败返回错误号。

### 3. 加锁

```
int pthread_mutex_lock(pthread_mutex_t *mutex);
```

- **作用**: 锁定互斥锁。如果互斥锁已经被其他线程锁定，调用线程将阻塞，直到互斥锁可用为止。

- 参数

  :

  - `mutex`: 指向要锁定的互斥锁变量的指针。

- **返回值**: 成功返回 `0`，失败返回错误号。

### 4. 尝试加锁

```
int pthread_mutex_trylock(pthread_mutex_t *mutex);
```

- **作用**: 尝试锁定互斥锁。如果互斥锁未被锁定，锁定成功并返回 `0`；如果互斥锁已被锁定，返回错误号但不会阻塞调用线程。

- 参数

  :

  - `mutex`: 指向要尝试锁定的互斥锁变量的指针。

- **返回值**: 成功返回 `0`，失败返回错误号。

### 5. 解锁

```
int pthread_mutex_unlock(pthread_mutex_t *mutex);
```

- **作用**: 解锁互斥锁，释放对共享资源的访问权。只有锁定互斥锁的线程才能解锁成功。

- 参数

  :

  - `mutex`: 指向要解锁的互斥锁变量的指针。

- **返回值**: 成功返回 `0`，失败返回错误号。



### restrict关键字

`restrict` 关键字是 C99 标准引入的一种限定符，用来告诉编译器指针是唯一的、独占的，指向的内存区域不会通过别的指针间接访问。这可以让编译器进行更激进的优化，从而提高程序的性能。

#### 使用场景

`restrict` 关键字主要用于指针声明，表示通过这个指针访问的内存区域不会被其他指针修改。它通常用于函数参数，特别是涉及大量数据处理的代码，如图像处理、矩阵运算等。

#### 语法示例

```
void func(int *restrict ptr1, int *restrict ptr2) {
    // 在函数中可以假设 ptr1 和 ptr2 指向的内存区域不重叠，
    // 也不会通过其他指针间接访问这些区域
    *ptr1 = *ptr2 + 10;
}
```

#### `restrict` 关键字的意义

1. 性能优化

   - 如果编译器知道 `ptr1` 和 `ptr2` 指向的内存区域是独立的，它可以优化生成的机器代码。例如，编译器可能会并行化内存操作或将某些操作放在寄存器中而不是内存中。

2. 指针别名

   - 在没有 `restrict` 关键字的情况下，编译器无法确定两个指针是否指向相同的内存区域（指针别名），因此可能会生成更保守的代码。
   - 有了 `restrict` 关键字，编译器可以确信不同的指针指向的内存区域没有重叠，从而可以放心地进行更激进的优化。

## 4.死锁

当多个线程访问共享资源, 需要加锁, 如果锁使用不当, 就会造成死锁这种现象。如果线程死锁造成的后果是：所有的线程都被阻塞，并且线程的阻塞是无法解开的（因为可以解锁的线程也被阻塞了）。

造成死锁的场景有如下几种：

- 加锁之后忘记解锁

```C
// 场景1
void func()
{
    for(int i=0; i<6; ++i)
    {
        // 当前线程A加锁成功, 当前循环完毕没有解锁, 在下一轮循环的时候自己被阻塞了
        // 其余的线程也被阻塞
    	pthread_mutex_lock(&mutex);
    	....
    	.....
        // 忘记解锁
    }
}

// 场景2
void func()
{
    for(int i=0; i<6; ++i)
    {
        // 当前线程A加锁成功
        // 其余的线程被阻塞
    	pthread_mutex_lock(&mutex);
    	....
    	.....
        if(xxx)
        {
            // 函数退出, 没有解锁（解锁函数无法被执行了）
            return ;
        }
        
        pthread_mutex_lock(&mutex);
    }
}



```

- 重复加锁, 造成死锁

```C
void func()
{
    for(int i=0; i<6; ++i)
    {
        // 当前线程A加锁成功
        // 其余的线程阻塞
    	pthread_mutex_lock(&mutex);
        // 锁被锁住了, A线程阻塞
        pthread_mutex_lock(&mutex);
    	....
    	.....
        pthread_mutex_unlock(&mutex);
    }
}

// 隐藏的比较深的情况
void funcA()
{
    for(int i=0; i<6; ++i)
    {
        // 当前线程A加锁成功
        // 其余的线程阻塞
    	pthread_mutex_lock(&mutex);
    	....
    	.....
        pthread_mutex_unlock(&mutex);
    }
}

void funcB()
{
    for(int i=0; i<6; ++i)
    {
        // 当前线程A加锁成功
        // 其余的线程阻塞
    	pthread_mutex_lock(&mutex);
        funcA();		// 重复加锁
    	....
    	.....
        pthread_mutex_unlock(&mutex);
    }
}



```

在程序中有多个共享资源, 因此有很多把锁，随意加锁，导致相互被阻塞

场景描述:
  - 有两个共享资源:X, Y，X对应锁A, Y对应锁B
     - 线程A访问资源X, 加锁A
     - 线程B访问资源Y, 加锁B
  - 线程A要访问资源Y, 线程B要访问资源X，因为资源X和Y已经被对应的锁锁住了，因此这个两个线程被阻塞
     - 线程A被锁B阻塞了, 无法打开A锁
     - 线程B被锁A阻塞了, 无法打开B锁

在使用多线程编程的时候，如何避免死锁呢？

- 避免多次锁定, 多检查

- 对共享资源访问完毕之后, 一定要解锁，或者在加锁的使用 trylock

- 如果程序中有多把锁, 可以控制对锁的访问顺序(顺序访问共享资源，但在有些情况下是做不到的)，另外也可以在对其他互斥锁做加锁操作之前，先释放当前线程拥有的互斥锁。

- 项目程序中可以引入一些专门用于死锁检测的模块




## 5.读写锁（Read-Write Lock）概述

读写锁是一种同步机制，它允许多个线程并发读取共享资源，但在写操作时会将其独占。与互斥锁（mutex）相比，读写锁更适合于以下场景：

- **读多写少**：当程序中大多数线程都是进行读操作而只有少数进行写操作时，使用读写锁可以显著提高并发性和效率。
- **共享读访问**：多个线程可以同时读取共享资源，而不会相互阻塞。

### 读写锁的工作原理

- **读锁**（`pthread_rwlock_rdlock`）: 当一个线程获取读锁时，其他线程也可以继续获取读锁。这意味着多个线程可以同时进行读操作，因为读操作不会修改数据，因此并发是安全的。
- **写锁**（`pthread_rwlock_wrlock`）: 当一个线程获取写锁时，其他任何线程都不能获取读锁或写锁。写锁是独占的，保证只有一个线程能够修改数据，从而防止数据竞争和不一致性。
- **锁优先级**: 通常，写锁的优先级高于读锁。当有一个线程请求写锁时，任何新的读锁请求都会被阻塞，直到写锁释放。

### 读写锁的使用

1. **初始化和销毁锁**:

   ```C
   #include <pthread.h>
   pthread_rwlock_t rwlock;
   
   // 初始化读写锁
   int pthread_rwlock_init(pthread_rwlock_t *restrict rwlock,
                           const pthread_rwlockattr_t *restrict attr);
   
   // 释放读写锁占用的系统资源
   int pthread_rwlock_destroy(pthread_rwlock_t *rwlock);
   ```

   - `rwlock`: 读写锁的地址。
   - `attr`: 读写锁属性，通常为`NULL`表示默认属性。

2. **加锁与解锁操作**:

   - **加读锁**:

     ```c
     int pthread_rwlock_rdlock(pthread_rwlock_t *rwlock);
     int pthread_rwlock_tryrdlock(pthread_rwlock_t *rwlock);
     ```

     - `pthread_rwlock_rdlock`: 阻塞调用，直到加锁成功。
     - `pthread_rwlock_tryrdlock`: 非阻塞调用，如果无法获取读锁立即返回错误码。

   - **加写锁**:

     ```C
     int pthread_rwlock_wrlock(pthread_rwlock_t *rwlock);
     int pthread_rwlock_trywrlock(pthread_rwlock_t *rwlock);
     ```

     - `pthread_rwlock_wrlock`: 阻塞调用，直到加锁成功。
     - `pthread_rwlock_trywrlock`: 非阻塞调用，如果无法获取写锁立即返回错误码。

   - **解锁**:

     ```C
     int pthread_rwlock_unlock(pthread_rwlock_t *rwlock);
     ```

     该函数用于解锁读锁或写锁。

### 读写锁的应用场景

1. **缓存读取**: 大量读取缓存数据的场景，例如Web服务器缓存静态文件。
2. **配置文件访问**: 配置文件的读多写少场景。
3. **数据库的读操作**: 允许多个线程同时读取数据库而不阻塞。

条件变量（Condition Variable）是一种用于多线程编程的同步机制。它允许线程在某个条件为真之前进入等待状态，避免频繁轮询条件状态，从而提高程序效率。条件变量通常与互斥锁（Mutex）结合使用，以确保线程在访问共享资源时的安全性。

## 6.条件变量
### 条件变量的工作原理

1. **等待条件变量**：
   
    - 当某个线程需要等待某个条件满足时，它可以调用 `pthread_cond_wait` 函数进入等待状态。
    - 在调用 `pthread_cond_wait` 时，线程首先会释放与条件变量相关联的互斥锁，然后进入阻塞状态等待条件变量的信号。
    - 当条件变量发出信号时，线程会被唤醒，并重新获得互斥锁，以继续执行后续的操作。
2. **发出信号**：
   
    - 另一个线程在条件满足时，可以调用 `pthread_cond_signal` 或 `pthread_cond_broadcast` 函数发出信号。
    - `pthread_cond_signal` 唤醒至少一个等待该条件的线程。
    - `pthread_cond_broadcast` 则唤醒所有等待该条件的线程。

### 条件变量的常见用法

条件变量通常用于生产者-消费者模型中。假设有一个共享队列，生产者线程向队列中添加数据，消费者线程从队列中取数据：

- **生产者线程**：在队列满时，生产者线程会等待队列有空位时再继续添加数据。如果队列非满，则将数据加入队列，并通过条件变量通知消费者线程。
  
- **消费者线程**：在队列为空时，消费者线程会等待有数据加入队列。当有生产者线程加入数据后，消费者线程被条件变量唤醒，取出数据并继续执行。

### 条件变量的主要操作函数

1. **条件变量初始化**

   ```C
   int pthread_cond_init(pthread_cond_t *restrict cond, const pthread_condattr_t *restrict attr);
   ```

   - **功能**：初始化一个条件变量 `cond`，使其可供线程使用。

   - 参数

     - `cond`：条件变量的地址。
     - `attr`：条件变量属性，通常使用默认属性，指定为 `NULL`。

   - **返回值**：成功返回 0，失败返回错误码。

   使用例子：

   ```C
   pthread_cond_t cond;
   pthread_cond_init(&cond, NULL);  // 初始化条件变量
   ```

2. **销毁条件变量**

   ```C
   int pthread_cond_destroy(pthread_cond_t *cond);
   ```

   - **功能**：销毁条件变量，释放其占用的资源。

   - 参数

     - `cond`：条件变量的地址。

   - **返回值**：成功返回 0，失败返回错误码。

   使用例子：

   ```C
   pthread_cond_destroy(&cond);  // 销毁条件变量
   ```

3. **等待条件变量**

   ```C
   int pthread_cond_wait(pthread_cond_t *restrict cond, pthread_mutex_t *restrict mutex);
   ```

   - **功能**：将调用线程阻塞，直到接收到条件变量的信号。需要与互斥锁结合使用。

   - 参数

     - `cond`：条件变量的地址。
     - `mutex`：互斥锁的地址。函数会在阻塞线程之前解锁该互斥锁，在线程被唤醒时重新锁定。

   - **返回值**：成功返回 0，失败返回错误码。

   使用场景：

   - 当一个线程希望等待某个条件时，例如等待队列中有数据可供消费时，它可以调用 `pthread_cond_wait`。

   ```C
   pthread_mutex_lock(&mutex);
   while (queue_is_empty()) {
       pthread_cond_wait(&cond, &mutex);
   }
   // 处理数据
   pthread_mutex_unlock(&mutex);
   ```

4. **定时等待条件变量**

   ```C
   int pthread_cond_timedwait(pthread_cond_t *restrict cond,
                              pthread_mutex_t *restrict mutex, 
                              const struct timespec *restrict abstime);
   ```

   - **功能**：类似 `pthread_cond_wait`，但如果在指定的时间 `abstime` 内条件未满足，线程将超时并继续执行。

   - 参数

     - `cond`：条件变量的地址。
     - `mutex`：互斥锁的地址。
     - `abstime`：表示绝对时间的 `timespec` 结构体，表示线程等待的超时时间。

   - **返回值**：成功返回 0，如果超时则返回 `ETIMEDOUT`。

   使用例子：

   ```C
   struct timespec ts;
   clock_gettime(CLOCK_REALTIME, &ts);
   ts.tv_sec += 5;  // 阻塞5秒
   
   int res = pthread_cond_timedwait(&cond, &mutex, &ts);
   if (res == ETIMEDOUT) {
       printf("Timed out waiting for condition\n");
   }
   ```

5. **唤醒等待的线程**

   - **pthread_cond_signal**

     ```C
     int pthread_cond_signal(pthread_cond_t *cond);
     ```

     - **功能**：唤醒至少一个等待该条件变量的线程。

     - 参数

       - `cond`：条件变量的地址。

     - **返回值**：成功返回 0，失败返回错误码。

     使用例子：

     ```C
     pthread_cond_signal(&cond);  // 唤醒一个线程
     ```

   - **pthread_cond_broadcast**

     ```C
     int pthread_cond_broadcast(pthread_cond_t *cond);
     ```

     - **功能**：唤醒所有等待该条件变量的线程。

     - 参数

       - `cond`：条件变量的地址。

     - **返回值**：成功返回 0，失败返回错误码。

     使用例子：

     ```C
     pthread_cond_broadcast(&cond);  // 唤醒所有线程
     ```

## 7.信号量

信号量（Semaphore）是一种用于多线程编程的同步机制，可以用来控制对共享资源的访问。信号量是一种计数器，维护着一个计数值，表示可以同时访问某个资源的线程数。信号量有两种类型：**二进制信号量**（binary semaphore）和**计数信号量**（counting semaphore）。

### 信号量的类型

1. **二进制信号量**：
   - 也称为互斥量（mutex），只有两个状态：0 和 1。
   - 类似于互斥锁，可以用来实现线程之间的互斥访问。
   - 在任意时刻最多只有一个线程可以访问共享资源。
2. **计数信号量**：
   - 计数信号量可以有多个资源的访问权限，允许多个线程同时访问共享资源。
   - 计数器的初始值表示资源的总数，信号量的值减少表示资源被占用，增加表示资源被释放。

### 信号量的操作函数

在 POSIX 线程库中，信号量使用 `sem_t` 类型进行表示，并提供了一组操作函数：

1. **信号量的初始化**

   ```C
   #include <semaphore.h>
   int sem_init(sem_t *sem, int pshared, unsigned int value);
   ```

   - **功能**：初始化一个信号量。

   - 参数

     - `sem`：指向信号量对象的指针。
     - `pshared`：指示信号量的共享类型。为 0 表示线程间共享，为非 0 表示进程间共享。
     - `value`：信号量的初始值（资源的数量）。

   - **返回值**：成功返回 0，失败返回 -1 并设置 `errno`。

   使用例子：

   ```C
   sem_t sem;
   sem_init(&sem, 0, 1);  // 初始化一个二进制信号量
   ```

2. **销毁信号量**

   ```C
   int sem_destroy(sem_t *sem);
   ```

   - **功能**：销毁信号量，释放其占用的资源。

   - 参数

     - `sem`：指向信号量对象的指针。

   - **返回值**：成功返回 0，失败返回 -1 并设置 `errno`。

   使用例子：

   ```C
   sem_destroy(&sem);
   ```

3. **等待信号量（P 操作）**

   ```C
   int sem_wait(sem_t *sem);
   ```

   - **功能**：等待信号量，使信号量的值减 1。如果信号量的值为 0，调用线程将被阻塞，直到信号量的值大于 0。

   - 参数

     - `sem`：指向信号量对象的指针。

   - **返回值**：成功返回 0，失败返回 -1 并设置 `errno`。

   使用例子：

   ```C
   sem_wait(&sem);  // 请求访问资源，可能会阻塞
   ```

4. **尝试等待信号量（非阻塞 P 操作）**

   ```C
   int sem_trywait(sem_t *sem);
   ```

   - **功能**：尝试等待信号量，使信号量的值减 1。如果信号量的值为 0，则函数立即返回，而不是阻塞。

   - 参数

     - `sem`：指向信号量对象的指针。

   - **返回值**：成功返回 0，失败返回 -1 并设置 `errno`。

   使用例子：

   ```C
   if (sem_trywait(&sem) == 0) {
       // 成功获取信号量
   } else {
       // 未成功获取信号量
   }
   ```

5. **释放信号量（V 操作）**

   ```C
   int sem_post(sem_t *sem);
   ```

   - **功能**：释放信号量，使信号量的值加 1。如果有线程正在等待信号量，则唤醒一个等待的线程。

   - 参数

     - `sem`：指向信号量对象的指针。

   - **返回值**：成功返回 0，失败返回 -1 并设置 `errno`。

   使用例子：

   ```C
   sem_post(&sem);  // 释放资源
   ```

### 信号量的典型使用场景

信号量广泛用于控制对共享资源的访问，尤其是在多线程或多进程的环境中。下面是两个典型的使用场景：

1. **生产者-消费者问题**：

   在生产者-消费者问题中，可以使用两个信号量来协调生产者和消费者之间的操作：

   - 一个信号量 `empty` 表示可用的空槽数（初始值为缓冲区的大小）。
   - 另一个信号量 `full` 表示已占用的槽数（初始值为 0）。
   - 生产者在添加项目到缓冲区之前，先执行 `sem_wait(&empty)`，然后在添加完项目后执行 `sem_post(&full)`。
   - 消费者在从缓冲区取项目之前，先执行 `sem_wait(&full)`，然后在取完项目后执行 `sem_post(&empty)`。

   这确保了生产者和消费者之间的正确同步。

2. **读者-写者问题**：

   信号量也可以用来实现读者-写者问题（Readers-Writers Problem），在这种情况下，需要确保：

   - 多个读者可以同时读取资源。
   - 只有一个写者可以写入资源，且写者与读者之间的操作是互斥的。

   信号量和互斥锁的结合使用可以解决这个问题，使得读者可以并发地读取数据，而写者在写入时能够独占访问权限。

### 示例代码

以下是一个简单的生产者-消费者问题的信号量实现示例：

```C
#include <pthread.h>
#include <semaphore.h>
#include <stdio.h>

#define BUFFER_SIZE 5

int buffer[BUFFER_SIZE];
int count = 0;
sem_t empty, full;
pthread_mutex_t mutex;

void* producer(void* arg) {
    int item;
    while (1) {
        item = rand() % 100;
        sem_wait(&empty);  // 等待有空位
        pthread_mutex_lock(&mutex);  // 加锁
        buffer[count++] = item;
        printf("Produced: %d\n", item);
        pthread_mutex_unlock(&mutex);  // 解锁
        sem_post(&full);  // 增加已用槽位
    }
}

void* consumer(void* arg) {
    int item;
    while (1) {
        sem_wait(&full);  // 等待有数据
        pthread_mutex_lock(&mutex);  // 加锁
        item = buffer[--count];
        printf("Consumed: %d\n", item);
        pthread_mutex_unlock(&mutex);  // 解锁
        sem_post(&empty);  // 增加空位
    }
}

int main() {
    pthread_t prod, cons;

    sem_init(&empty, 0, BUFFER_SIZE);  // 初始化空槽信号量
    sem_init(&full, 0, 0);  // 初始化已用槽信号量
    pthread_mutex_init(&mutex, NULL);  // 初始化互斥锁

    pthread_create(&prod, NULL, producer, NULL);
    pthread_create(&cons, NULL, consumer, NULL);

    pthread_join(prod, NULL);
    pthread_join(cons, NULL);

    sem_destroy(&empty);  // 销毁信号量
    sem_destroy(&full);
    pthread_mutex_destroy(&mutex);  // 销毁互斥锁

    return 0;
}
```
