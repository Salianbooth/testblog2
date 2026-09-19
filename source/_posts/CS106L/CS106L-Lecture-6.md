---
title: CS106L:Lecture 6
date: 2024-09-17 12:08:13
tags:
  - Cpp
categories:
  - CS106L
---
>这是本次课程的最后一次笔记了，满打满算是看了2周的时间，中间耽误了不少时间。本次的课程主要是讲了RAII和smart pointer 还有多线程的知识。

# 1.RAII
RAII 是Resource Acquisition Is Initialization的缩写，中文翻译过来就是**资源的获取就是初始化**，本质就是为了让资源的生命周期和对象生命周期对等，可以简单的理解为构造即申请，析构即释放。
RAII主要用于管理诸如**动态内存、文件句柄、锁、数据库连接**等系统资源，避免手动释放这些资源时可能出现的错误。
## RAII使用案例
如果没有RAII的话，我们执行下面的代码：
```cpp
string EvaluateSalaryAndReturnName(int idNumber) { 
    Employee* e = new Employee(idNumber);
    auto result = e->First() + " " + e->Last();
    delete e;  // 存在内存泄漏的风险
    return result;
}
```
这里使用了动态分配new，并且在结束前使用了delete将分配的内存删掉，但是在delete语句之前如果发生了错误提前退出，那么就会有内存泄漏的风险。
那么这里的话，我们可以通过使用智能指针来解决这个问题，当智能指针出了作用域后，就会自动销毁。
```cpp
#include <memory>  // 包含智能指针的头文件

string EvaluateSalaryAndReturnName(int idNumber) {
    // 使用 std::unique_ptr 来管理动态分配的 Employee 对象
    std::unique_ptr<Employee> e = std::make_unique<Employee>(idNumber); 
    
    // 使用智能指针时不需要手动删除对象
    auto result = e->First() + " " + e->Last();
    
    // 返回结果，内存自动释放，无需手动 delete
    return result;
}
```

上面的内容是和内存管理相关的内容，此外RAII还应用于多线程中的互斥锁管理。
下面是手动解锁：
```cpp
void cleanDatabase (mutex& databaseLock, map<int, int>& database) {
    databaseLock.lock();
    // 对数据库的操作
    databaseLock.unlock();  // 如果中间抛出异常，锁不会被释放
}

```
这样和上面的内存管理相似，如果中间发生错误，databaseLock这个锁就不会被打开，又可以引发死锁。在这里可以使用`lock_guard`来轻松解决：
```cpp
void cleanDatabase (mutex& databaseLock, map<int, int>& database) {
    lock_guard<mutex> lock(databaseLock);  // RAII风格，自动获取和释放锁
    // 在作用域结束时，析构函数会自动释放锁
}
```
## 智能指针

在C++中，**智能指针**（Smart Pointers）是一种用于自动管理动态内存分配的工具，它确保对象在不再使用时自动释放内存，避免手动调用 `delete` 导致的内存泄漏和其他问题。智能指针基于 RAII（资源获取即初始化）的理念管理资源的生命周期。

C++ 标准库提供了三种主要的智能指针：

1. **`std::unique_ptr`**  
2. **`std::shared_ptr`**  
3. **`std::weak_ptr`**

### 1. `std::unique_ptr`
`std::unique_ptr` 是独占型智能指针，它独占所指向的资源，即不能被复制，只能通过移动语义转移所有权。

- **特点**:
  - **独占所有权**: 每个资源只能被一个 `std::unique_ptr` 管理。
  - **内存安全**: 在指针超出作用域时，自动释放资源。
  - **移动语义**: 不能复制，但可以通过 `std::move` 转移所有权。

- **示例**:
```cpp
#include <memory>
#include <iostream>

class Employee {
public:
    Employee(int id) { std::cout << "Employee created with id: " << id << std::endl; }
    ~Employee() { std::cout << "Employee destroyed" << std::endl; }
    std::string First() const { return "John"; }
    std::string Last() const { return "Doe"; }
};

std::string EvaluateSalaryAndReturnName(int idNumber) {
    std::unique_ptr<Employee> e = std::make_unique<Employee>(idNumber); // 使用 unique_ptr
    return e->First() + " " + e->Last();
}

int main() {
    std::string name = EvaluateSalaryAndReturnName(123);
    std::cout << "Employee Name: " << name << std::endl;
    return 0;
}
```

**输出**:
```
Employee created with id: 123
Employee Name: John Doe
Employee destroyed
```

在这个例子中，`Employee` 对象在函数结束时会自动销毁，无需手动调用 `delete`，避免内存泄漏。

### 2. `std::shared_ptr`
`std::shared_ptr` 是一种共享型智能指针，可以让多个指针共享同一个对象。当最后一个指向该对象的 `shared_ptr` 被销毁时，对象才会被释放。

- **特点**:
  - **共享所有权**: 多个 `shared_ptr` 可以指向同一块内存。
  - **引用计数**: 维护一个引用计数，跟踪有多少个 `shared_ptr` 指向同一个对象。
  - **内存自动释放**: 当引用计数归零时，自动释放资源。

- **示例**:
```cpp
#include <memory>
#include <iostream>

std::string EvaluateSalaryAndReturnName(int idNumber) {
    std::shared_ptr<Employee> e = std::make_shared<Employee>(idNumber); // 使用 shared_ptr
    return e->First() + " " + e->Last();
}
```

在这个例子中，即使有多个 `shared_ptr` 指向同一个 `Employee` 对象，也会自动管理引用计数，并在最后一个指针销毁时释放内存。

### 3. `std::weak_ptr`
`std::weak_ptr` 是与 `shared_ptr` 搭配使用的弱引用指针。它不会增加对象的引用计数，常用于避免循环引用问题。

- **特点**:
  - **不影响引用计数**: `weak_ptr` 不增加对象的引用计数，因此不会影响对象的生命周期。
  - **用法**: 常用于缓存或避免循环引用，必须使用 `lock()` 将 `weak_ptr` 转换为 `shared_ptr` 才能访问对象。

- **示例**:
```cpp
#include <memory>
#include <iostream>

void UseEmployee(std::weak_ptr<Employee> weakEmp) {
    if (auto emp = weakEmp.lock()) {  // 将 weak_ptr 转换为 shared_ptr
        std::cout << emp->First() << " " << emp->Last() << std::endl;
    } else {
        std::cout << "Employee no longer exists." << std::endl;
    }
}
```

`weak_ptr` 适合用于弱引用对象的场景，避免循环依赖造成的内存泄漏。

### 总结
- **`std::unique_ptr`**: 独占内存资源，适合需要明确单一所有权的情况，最轻量、最安全。
- **`std::shared_ptr`**: 共享内存资源，适合需要多个对象共享同一资源的情况，但稍微有性能开销（引用计数管理）。
- **`std::weak_ptr`**: 用于打破 `shared_ptr` 的循环引用，它是一个弱引用，不管理资源生命周期。

这些智能指针通过 RAII 机制管理资源，确保程序的内存安全并减少手动管理内存的复杂性。

# 尾声
这基本就是这门课的主要内容了，最后的RAII笔者没有仔细去看，主要是感觉知识密度太低了，现在这门课也算是圆满结束了，也算是对C++Primer的复习了。接下来就去看effective C++和CMU15-445这门课了，笔者估计将来要走C++和数据库的方向，现在国产数据库是大头，要好好搞一下了！