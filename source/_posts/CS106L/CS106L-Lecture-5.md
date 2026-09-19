---
title: CS106L:Lecture 5
date: 2024-09-13 09:00:15
tags:
  - Cpp
categories:
  - CS106L
---
# TO-do list


1. ~~const + photo +  非const函数无法使用const变量~~
2. ~~operator overload~~
3. ~~POLA~~
4. spaceship in C++20
5. ~~copy constructor & copy assignment~~
6. ~~rvalue & lvalue~~
7. empalce_back
8. ~~move constructor~~
9. ~~include & using namespace~~
10. ~~virtual function & pure 

>这一节课主要讲了一些和面向对象有关的内容，主要包括重写操作符，构造函数，虚函数，继承等有关。这些在之前的[C++Primer](https://salianbooth.github.io/2024/08/02/C-Primer-learning/)中还是粗略的学过，感觉这次的课程主要就是为之前学习的C++进行巩固加深。
# 1.Const
对于const的话，我之前又专门写过一篇博客来区分底层const和顶层const的区别，这次的课程着重介绍了const在函数和class中应用，所以这次就让我们一起仔细地探索const的所有用途。

## 1.1 const基本用法
const最基础的用法就是声明常量，虽然在C++11中引入了一个新的关键字`constexpr`用于声明常量，但是还是`const`用途更广泛，下面是示例代码：
```cpp
const int x = 10;  // x 是常量，不能被修改
x = 20;           // 错误，x 是常量，无法修改
```

## 1.2const和指针

1. 指向const对象的指针
	这种指针指向的是不可以修改的对象，但是可以修改指针本身：
```cpp
	const int* ptr;   // 指向 const int 的指针
	int x = 10;
	ptr = &x;        // 可以指向 x
	*ptr = 20;       // 错误，不能通过 ptr 修改 x 的值

```
2. const指针
	这种指针不能修改指向的对象，但是能修改对象的值：
```cpp
	int* const ptr = &x;   // 常量指针，ptr 的地址不能修改
	*ptr = 20;             // 可以通过 ptr 修改 x 的值
	ptr = &y;              // 错误，ptr 是常量，不能指向别的地址

```
3. 指向 `const` 对象的 `const` 指针
	结合体了属于是，指针和指向的对象都不能修改：
```Cpp
	const int* const ptr = &x;  // 常量指针，且指向一个 const 对象
	*ptr = 20;                 // 错误，不能修改 x 的值
	ptr = &y;                  // 错误，不能改变 ptr 指向的地址

```
## 1.3 const函数
`const` 关键字也可以用在成员函数中，用于表示该函数不会修改对象的成员变量。
###  1.3.1**`const` 成员函数**

成员函数的末尾加上 `const` 表示该函数不会修改对象的状态。也就是说，函数内部不能改变任何成员变量的值（除非是 `mutable` 修饰的变量）。

```Cpp
class MyClass {
public:
    int x;
    
    void setValue(int val) {
        x = val;
    }
    
    int getValue() const {   // const 成员函数
        return x;            // 不能修改类的成员变量
    }
};

```


### 1.3.2 **`const` 函数和非 `const` 函数的重载**

>`const` 版本的函数只能被 `const` 对象调用，而非 `const` 版本的函数可以被普通对象调用。

在一个类中，你可以定义两个具有相同名称但不同 `const` 限定的成员函数。**`const` 版本的函数只能被 `const` 对象调用，而非 `const` 版本的函数可以被普通对象调用。**
```Cpp
class MyClass {
public:
    int getValue() {
        return 42;  // 非 const 版本
    }
    
    int getValue() const {
        return 24;  // const 版本
    }
};

int main() {
    MyClass obj1;
    const MyClass obj2;
    
    obj1.getValue();  // 调用非 const 版本
    obj2.getValue();  // 调用 const 版本
}

```

### 1.3.3 **`const` 返回值**

当函数返回一个 `const` 值时，表示返回的对象是不可修改的。


```Cpp
const int getValue() {
    return 10;
}

int main() {
    int x = getValue();
    x = 20;  // 正确，可以修改 x
    getValue() = 20;  // 错误，不能修改 const 返回值
}

```
### 1.3.4 **`const` 参数**

`const` 参数表示该参数在函数内部不能被修改，通常用于传递引用或指针以提高性能，同时保证函数不会修改参数值。

```Cpp
void printValue(const int& value) {
    // value 不能被修改
    std::cout << value;
}

```
### 1.3.5 **函数参数后的 `const`**

表示该成员函数不会修改类的成员变量。通常用于成员函数，表明该函数不会改变对象的状态。

```Cpp
class MyClass {
public:
    int getValue() const {  // const 成员函数
        return 42;
    }
};

```

# 2.Operator overloading
我们有很多的操作符，其中差不多有40多种可以被重载，就是下图中标黄的部分![Clip_2024-09-16_17-07-41.png](https://gitee.com/salianbooth/picture/raw/master/202409161707187.png)

## 2.1重载<< & >>
这一部分在[C++Primer](https://salianbooth.github.io/2024/08/02/C-Primer-learning/)中也有详细的讲过，这里就简单举一个例子，
这一部分在之前写图书管理系统有用过，但是当时还是一知半解，借助chatgpt才完成的。C++标准库提供了 `<<` 和 `>>` 运算符用于输出和输入操作，这些运算符的默认行为只适用于标准数据类型，例如整数、浮点数、字符串等。但对于自定义的类或结构体，我们可以通过重载这两个运算符来实现输入输出的功能。
***重载输出运算符***
```cpp
class A{  
    friend ostream& operator<<(ostream&,const A&);  
public:  
    int a_;  
    A() : a_(0) {}  
    A(int a) : a_(a) {}  
  
    //在类内会有一个隐式的绑定，lhs就是this的A  
    A& operator+ (const A& a){  
        cout << "in operator + " << a.a_ << endl;  
        this->a_ += a.a_;  
        return *this;  
    }  
};  
//在<<这个operator上就不能隐式绑定了，因为第一个是ostream，并且它也不能拷贝  
ostream& operator<<(ostream& os, const A& a){  
    cout << "in operator << " << endl;  
  
    os << a.a_ << endl;  
    return  os;  
  
}  
int main(int argc,char** argv) {  
    std::cout << "Hello, World!" << std::endl;  
    A a(1);  
    cout << a << endl;  
    return 0;  
}

```
***重载输入运算符***
```Cpp
#include <iostream>

class Person {
public:
    Person() : name(""), age(0) {}

    // 重载 >> 运算符
    friend std::istream& operator>>(std::istream &in, Person &p);

private:
    std::string name;
    int age;
};

// 输入运算符重载的定义
std::istream& operator>>(std::istream &in, Person &p) {
    std::cout << "Enter name: ";
    in >> p.name;
    std::cout << "Enter age: ";
    in >> p.age;
    return in;
}

int main() {
    Person person;
    std::cin >> person;
    std::cout << person << std::endl; // 输出输入的内容
    return 0;
}
```
## 2.2 POLA
在 C++ 和软件设计的背景下，**POLA** 是 "Principle of Least Astonishment"（最小惊讶原则）的缩写。这一原则也被称为**最小惊讶原则**或**最小意外原则**，它主张设计系统时，应该遵循用户或程序员的直觉，避免让他们感到意外或困惑。

### 1. **POLA 的基本概念**

POLA 的核心思想是：**代码、接口、或库的行为应该符合用户的期望**，从而避免在使用时出现惊讶或意外的结果。这一原则在编写代码时很有用，因为它能提高代码的可读性、可维护性，并减少出错的几率。

### 2. **POLA 在 C++ 编程中的应用**

在 C++ 中，POLA 可以在多个层面上应用，涵盖代码设计、库设计、函数行为等，以下是几个常见场景：

#### 2.1 **函数和运算符的预期行为**
在重载运算符或函数时，必须遵循 POLA，使得用户直观地理解它们的行为。例如：

- 如果重载 `+` 运算符，用户通常会期望它是相加操作，而不是减法或其他操作。如果行为与标准运算符不一致，会让用户感到意外。
  
- 构造函数应该初始化类的成员，并确保类的对象在构造后处于有效的状态。如果构造函数不进行适当的初始化，用户可能会感到意外。

```cpp
class MyClass {
public:
    MyClass() {
        // 按照 POLA 原则，这里应初始化所有成员变量
        // 避免未定义行为或令人意外的错误
    }
};
```

#### 2.2 **接口设计**
设计类和函数的接口时，也需要遵循 POLA。例如：
- **函数的返回值**：用户调用一个函数时，通常期待返回值与函数名相关。例如，一个名为 `getValue()` 的函数，用户会期望它返回某个值，而不是修改对象的内部状态。

- **合理的默认参数**：当函数提供默认参数时，应该保证这些默认参数不会导致意外行为。默认参数应符合常见的使用场景，而不是稀有的边界情况。

#### 2.3 **函数副作用**
函数不应该有未预期的副作用。如果一个函数名表明它是一个查询函数，那么用户通常会期望它不修改任何数据。例如：

```cpp
int getValue() const {
    // 按照 POLA 原则，这个函数名表明它是只读操作
    // 它不应该修改类的成员变量
    return value;
}
```

#### 2.4 **错误处理和异常**
在错误处理方面，程序设计应该避免让用户意外地收到异常或错误信息。例如：
- **隐式转换**：如果隐式转换可能导致模糊或不明确的行为，那么这种设计不符合 POLA。用户可能期望使用特定类型而不会产生副作用。
- **合理的异常抛出**：如果函数有可能抛出异常，用户应该能够预测到。否则，程序可能会在用户意外的地方崩溃。

#### 2.5 **资源管理**
当使用资源（如内存、文件句柄）时，用户期望资源能够自动释放，因此应该遵循 RAII（Resource Acquisition Is Initialization）原则，避免内存泄漏和资源未正确释放的情况。

例如，在 C++ 中使用智能指针可以更符合 POLA，因为它自动管理资源，避免用户意外忘记释放内存。

```cpp
std::shared_ptr<int> ptr = std::make_shared<int>(5);
// 用户不需要担心手动释放内存，符合 POLA
```

#### 2.6 **函数和类的命名**
良好的命名能够帮助遵循 POLA。函数、变量和类的名称应该清晰地表达它们的意图。用户通过名称应该能够推测出它们的功能，避免意外误用。

```cpp
class UserAccount {
    void authenticate();   // 名称清晰表明此函数用于身份验证
};
```

### 3. **POLA 的重要性**
- **提高可读性和可维护性**：遵循 POLA 的代码更容易理解和维护，因为它的行为是直观的，符合程序员的期望。
- **减少 Bug 和错误**：用户意外的行为往往会导致 Bug。如果程序的行为总是符合预期，用户就能更容易理解并正确使用代码，减少出错的机会。
- **提高用户体验**：对于开发库或 API 的开发者来说，遵循 POLA 能让使用者更愉快地使用接口，不需要反复查阅文档来确认每个函数的行为。

# 3.Member Function
## 3.1 copy constructor & copy assignment
在 C++ 中，**拷贝构造函数**（copy constructor）和**拷贝赋值运算符**（copy assignment operator）都用于复制对象，但它们在应用场景和行为上有所不同。以下是两者的主要区别和详细解释：

### 1. **拷贝构造函数（Copy Constructor）**

- **定义**：拷贝构造函数是在创建一个新的对象时，通过已有对象来初始化新对象的构造函数。
  
- **语法**：
  ```cpp
  ClassName(const ClassName& other);
  ```

- **何时调用**：拷贝构造函数在以下几种情况中调用：
  1. 当用现有对象初始化一个新的对象时：
     ```cpp
     ClassName obj1;      // 调用默认构造函数
     ClassName obj2 = obj1; // 调用拷贝构造函数
     ```
  2. 当函数通过值传递对象时：
     ```cpp
     void func(ClassName obj); // 在调用 func 时，会调用拷贝构造函数
     ```
  3. 当函数返回对象时（通过值返回）：
     ```cpp
     ClassName func() {
         ClassName obj;
         return obj; // 拷贝构造函数可能被调用，或是启用返回值优化（RVO）
     }
     ```

- **默认行为**：如果不显式定义拷贝构造函数，编译器会自动生成一个默认的拷贝构造函数，它会按**成员逐一拷贝**的方式（shallow copy）复制对象的成员。

- **自定义拷贝构造函数**：在对象有需要深拷贝的资源（如动态分配的内存、文件句柄等）时，往往需要显式定义拷贝构造函数来避免浅拷贝带来的问题。

  例子：
  ```cpp
  class MyClass {
  private:
      int* data;

  public:
      // 构造函数
      MyClass(int value) {
          data = new int(value);
      }

      // 拷贝构造函数 (深拷贝)
      MyClass(const MyClass& other) {
          data = new int(*other.data); // 深拷贝
      }

      // 析构函数
      ~MyClass() {
          delete data;
      }
  };
  ```

### 2. **拷贝赋值运算符（Copy Assignment Operator）**

- **定义**：拷贝赋值运算符用于将一个对象的内容赋值给另一个已经存在的对象。它不同于拷贝构造函数，它是修改已有对象而不是创建新对象。

- **语法**：
  ```cpp
  ClassName& operator=(const ClassName& other);
  ```

- **何时调用**：拷贝赋值运算符在对象已经被创建且需要被重新赋值时调用。
  ```cpp
  ClassName obj1; // 调用默认构造函数
  ClassName obj2; // 调用默认构造函数
  obj2 = obj1;    // 调用拷贝赋值运算符
  ```

- **默认行为**：如果不显式定义拷贝赋值运算符，编译器会自动生成一个默认的拷贝赋值运算符，按**成员逐一赋值**的方式（shallow copy）进行赋值。

- **自定义拷贝赋值运算符**：在类管理动态资源时，需要自定义拷贝赋值运算符，以避免浅拷贝问题，并且需要小心处理自赋值的情况（如 `a = a;`），以避免资源被不正确地释放。

  例子：
  ```cpp
  class MyClass {
  private:
      int* data;

  public:
      // 构造函数
      MyClass(int value) {
          data = new int(value);
      }

      // 拷贝赋值运算符
      MyClass& operator=(const MyClass& other) {
          if (this != &other) {      // 检查自赋值
              delete data;           // 释放旧内存
              data = new int(*other.data); // 深拷贝
          }
          return *this;
      }

      // 析构函数
      ~MyClass() {
          delete data;
      }
  };
  ```

### 3. **区别总结**

| 特性                         | 拷贝构造函数                             | 拷贝赋值运算符                           |
|------------------------------|----------------------------------------|----------------------------------------|
| **目的**                      | 用一个已有对象创建一个新对象              | 将一个已有对象的内容赋值给另一个已存在的对象 |
| **调用时机**                  | 当新对象被创建并需要初始化时（如传递值参数、返回值） | 当一个已有对象需要被重新赋值时           |
| **语法**                      | `ClassName(const ClassName& other)`     | `ClassName& operator=(const ClassName& other)` |
| **默认行为**                  | 默认按成员逐一拷贝                      | 默认按成员逐一赋值                      |
| **自赋值检查**（`a = a`）     | 不需要（新对象和原对象不同）              | 通常需要进行自赋值检查                   |
| **返回类型**                  | 不返回值                               | 返回当前对象的引用 (`*this`)             |

### 4. **使用示例**
```cpp
#include <iostream>

class MyClass {
public:
    int* data;

    // 构造函数
    MyClass(int value) {
        data = new int(value);
    }

    // 拷贝构造函数
    MyClass(const MyClass& other) {
        data = new int(*other.data); // 深拷贝
        std::cout << "Copy Constructor called!" << std::endl;
    }

    // 拷贝赋值运算符
    MyClass& operator=(const MyClass& other) {
        if (this != &other) { // 检查自赋值
            delete data;       // 释放旧内存
            data = new int(*other.data); // 深拷贝
            std::cout << "Copy Assignment Operator called!" << std::endl;
        }
        return *this;
    }

    // 析构函数
    ~MyClass() {
        delete data;
    }
};

int main() {
    MyClass obj1(10);         // 构造函数
    MyClass obj2 = obj1;      // 调用拷贝构造函数
    MyClass obj3(20);         // 构造函数
    obj3 = obj1;              // 调用拷贝赋值运算符
}
```

### 5. **总结**

- **拷贝构造函数**用于创建新对象，并用已有对象来初始化它。
- **拷贝赋值运算符**用于将一个对象的内容赋值给已经存在的另一个对象。
  
理解它们的区别，尤其是在涉及到管理动态资源时，至关重要。通过定义深拷贝机制，确保对象之间的复制操作不会产生意外的共享资源或内存泄漏问题。

## 3.2 move constructor & move assignment
**移动构造函数**和**移动赋值运算符（Move Assignment Operator）**都是 C++11 引入的特性，用于优化对象的移动操作，避免不必要的深拷贝。它们在语法、调用场景和使用上存在一些重要区别。

### 1. **移动构造函数（Move Constructor）**

- **定义**：移动构造函数用于创建一个新对象，并通过“移动”资源而非“复制”资源的方式来初始化新对象。
  
- **语法**：
  ```cpp
  ClassName(ClassName&& other);
  ```

- **调用时机**：移动构造函数在以下情况下调用：
  1. 当一个临时对象（右值）被用来初始化另一个对象时。
     ```cpp
     ClassName obj1 = std::move(temp_obj); // 移动构造函数被调用
     ```
  2. 当函数返回一个对象，并且启用了移动语义时。
  3. 在某些容器（如 `std::vector`）重分配内存并移动其元素时。

- **作用**：移动构造函数将源对象（通常是临时对象）的资源（如指针、文件句柄等）“移动”到新对象中，而不是复制资源。移动后，源对象处于有效但未定义的状态，通常会将其内部指针设为 `nullptr`，防止资源重复释放。

  ```cpp
  class MyClass {
  public:
      int* data;
      
      // 移动构造函数
      MyClass(MyClass&& other) noexcept {
          data = other.data;   // 直接获取资源
          other.data = nullptr; // 清空源对象的资源
      }
  };
  ```

### 2. **移动赋值运算符（Move Assignment Operator）**

- **定义**：移动赋值运算符用于将一个已有对象的资源“移动”到另一个已经存在的对象上。这与移动构造函数的区别在于，移动赋值是针对已经存在的对象，而移动构造是创建新对象。

- **语法**：
  ```cpp
  ClassName& operator=(ClassName&& other);
  ```

- **调用时机**：移动赋值运算符在以下情况下调用：
  1. 当一个临时对象（右值）被赋值给一个已经存在的对象时。
     ```cpp
     ClassName obj1;
     obj1 = std::move(temp_obj); // 移动赋值运算符被调用
     ```
  2. 当一个对象从另一个对象中通过移动语义赋值时。

- **作用**：移动赋值运算符用于释放目标对象的现有资源（如果有的话），然后“移动”源对象的资源到目标对象中。与移动构造函数不同的是，移动赋值运算符需要处理目标对象已经分配了资源的情况，通常需要先释放旧资源以避免内存泄漏。

  ```cpp
  class MyClass {
  public:
      int* data;
      
      // 移动赋值运算符
      MyClass& operator=(MyClass&& other) noexcept {
          if (this != &other) {      // 防止自赋值
              delete data;           // 释放旧资源
              data = other.data;     // 获取新资源
              other.data = nullptr;  // 清空源对象
          }
          return *this;
      }
  };
  ```

### 3. **移动构造函数和移动赋值运算符的区别**

| **特性**                     | **移动构造函数**                            | **移动赋值运算符**                            |
|------------------------------|--------------------------------------------|----------------------------------------------|
| **目标**                      | 创建一个新对象并移动资源                    | 将资源从一个对象“移动”到已经存在的对象中       |
| **调用时机**                  | 当新对象通过右值初始化时                     | 当已有对象通过右值被赋值时                     |
| **语法**                      | `ClassName(ClassName&& other)`              | `ClassName& operator=(ClassName&& other)`    |
| **需要处理的资源状态**         | 新对象刚创建，没有已有资源                   | 已有对象可能已经持有资源，需要先清理或释放     |
| **是否涉及自赋值检查**         | 不需要，因为是构造新对象                    | 需要检查自赋值，以避免错误释放资源             |
| **示例场景**                  | 用临时对象初始化新对象                       | 将一个对象通过移动语义赋值给另一个已有对象     |

### 4. **代码示例**

```cpp
#include <iostream>

class MyClass {
public:
    int* data;

    // 构造函数
    MyClass(int value) {
        data = new int(value);
        std::cout << "Constructor called\n";
    }

    // 移动构造函数
    MyClass(MyClass&& other) noexcept {
        data = other.data;         // 直接获取资源
        other.data = nullptr;      // 清空源对象
        std::cout << "Move Constructor called\n";
    }

    // 移动赋值运算符
    MyClass& operator=(MyClass&& other) noexcept {
        if (this != &other) {      // 检查自赋值
            delete data;           // 释放现有资源
            data = other.data;     // 获取新资源
            other.data = nullptr;  // 清空源对象
            std::cout << "Move Assignment Operator called\n";
        }
        return *this;
    }

    // 析构函数
    ~MyClass() {
        delete data;
        std::cout << "Destructor called\n";
    }
};

int main() {
    MyClass obj1(10);              // 构造函数
    MyClass obj2 = std::move(obj1); // 移动构造函数

    MyClass obj3(20);              // 构造函数
    obj3 = std::move(obj2);        // 移动赋值运算符
}
```

### 5. **总结**

- **移动构造函数**：用于通过“移动”资源来创建一个新对象，避免不必要的复制操作。适用于初始化新对象时。
- **移动赋值运算符**：用于将已有对象的资源“移动”到另一个已存在的对象上，适用于赋值操作。

移动构造函数与移动赋值运算符的主要区别在于它们的应用场景和作用的对象不同。前者是在创建新对象时调用，后者是在对象赋值时调用。

## 3.3 rvalue & lvalue
在 C++ 中，**左值（Lvalue）** 和 **右值（Rvalue）** 是用来描述表达式值的两类概念，主要用于区分表达式在赋值、函数调用等场景中的行为方式。理解这两者的区别对于掌握 C++ 的赋值、函数重载、移动语义等特性非常重要。

### 1. **Lvalue（左值）**

- **定义**：Lvalue 是指**有明确内存地址**的表达式，可以在程序的多个地方被引用。左值通常是可以出现在赋值表达式左边的值，因此可以被修改。
  
- **特点**：
  - Lvalue 表示持久存在的对象（具有内存地址）。
  - 可以出现在赋值运算符的左侧和右侧（即可以读取和修改）。
  - 是可以被取地址的，使用 `&` 可以获取它的地址。

- **示例**：
  ```cpp
  int x = 10;   // 变量 x 是一个左值
  x = 20;       // 可以给 x 赋值，x 是一个左值
  int* p = &x;  // 可以通过取地址符获取 x 的地址
  ```

- **总结**：Lvalue 是指向某个内存位置的对象或表达式，表示可以被引用和修改的值。

### 2. **Rvalue（右值）**

- **定义**：Rvalue 是指**不占有内存地址**或者**临时的值**，它只能出现在赋值运算符的右侧。Rvalue 通常是一些临时对象，它们的生命周期仅限于当前表达式。

- **特点**：
  - Rvalue 表示临时的值，通常是不能取地址的（因为它们没有持久的内存地址）。
  - 不能出现在赋值运算符的左侧，意味着它们不能被修改。
  - 右值通常包括字面量（如整数、浮点数等）和临时对象。

- **示例**：
  ```cpp
  int y = 5;    // 5 是一个右值（它是一个字面量）
  y = x + 10;   // x + 10 是一个右值（表达式结果是临时值）
  ```

- **总结**：Rvalue 是指一个临时值或表达式的计算结果，不会持久存在，也不能被修改或取地址。

### 3. **Lvalue 和 Rvalue 的区别**

| **特性**               | **Lvalue**                              | **Rvalue**                          |
|------------------------|------------------------------------------|-------------------------------------|
| **存储**               | 有明确的内存地址（持久的对象）            | 临时的，没有内存地址（短暂的值）     |
| **赋值**               | 可以出现在赋值操作的左侧和右侧            | 只能出现在赋值操作的右侧              |
| **取地址**             | 可以取地址 (`&`)                          | 不能取地址                           |
| **生命周期**           | 在作用域中持久存在                        | 临时的，仅在当前表达式中有效           |

### 4. **右值引用（Rvalue Reference）与左值引用（Lvalue Reference）**

C++11 引入了 **右值引用（Rvalue Reference）** 的概念，以优化对象的移动操作和减少不必要的复制。

#### **左值引用（Lvalue Reference）**：
- 左值引用是引用左值对象的引用。
- 语法：`Type& ref = lvalue;`
- 示例：
  ```cpp
  int x = 10;
  int& ref = x; // ref 是 x 的左值引用
  ```

#### **右值引用（Rvalue Reference）**：
- 右值引用是引用右值对象的引用，通常用于捕获临时对象，以便进行“移动语义”操作（如 `std::move`）。
- 语法：`Type&& ref = rvalue;`
- 示例：
  ```cpp
  int&& ref = 10; // ref 是 10 的右值引用
  ```

### 5. **Lvalue 和 Rvalue 示例详解**

#### 左值示例：
```cpp
int x = 42;      // x 是左值，有明确内存地址
int* p = &x;     // 可以获取 x 的地址
x = 100;         // 左值可以出现在赋值运算符的左侧
```

#### 右值示例：
```cpp
int y = x + 10;  // x + 10 是一个右值，表示表达式的临时结果
int* p = &(x + 10); // 错误！不能获取右值的地址
```

### 6. **进一步理解：Lvalue、Rvalue 与函数重载**

C++11 中引入的右值引用允许我们根据左值和右值进行函数重载。

```cpp
void func(int& x) {
    std::cout << "Lvalue reference called" << std::endl;
}

void func(int&& x) {
    std::cout << "Rvalue reference called" << std::endl;
}

int main() {
    int a = 5;
    func(a);           // 调用左值版本
    func(10);          // 调用右值版本
}
```

在上面的例子中，`func` 重载了接受左值引用和右值引用的版本，`a` 是左值，因此调用左值引用版本；`10` 是右值，因此调用右值引用版本。

### 7. **总结**

- **Lvalue（左值）** 是持有内存地址的对象，具有较长生命周期，可以被修改。
- **Rvalue（右值）** 是临时的、短暂的值，通常不能被修改或取地址。
- C++11 引入了 **右值引用（Rvalue Reference）**，用于捕获右值并实现优化的移动语义。
### 练习
![Clip_2024-09-15_10-25-33.png](https://gitee.com/salianbooth/picture/raw/master/202409151025446.png)
让我们逐行分析这段代码及其对应的注释。

#### 代码解释

1. **前置变量定义**：
   ```cpp
   int val = 2;
   int* ptr = 0x02248837; // 假设地址为 0x02248837
   vector<int> v1{1, 2, 3};
   ```

   - `val` 是一个整数变量，存储值 `2`。
   - `ptr` 是一个指向整数的指针，指向一个特定地址（这里用一个示例地址 `0x02248837` 表示）。
   - `v1` 是一个包含三个整数的 `vector`，分别是 `1, 2, 3`。

2. **各个语句分析**：

   ```cpp
   auto ptr2 = ptr;
   ```
   - `ptr2` 是通过 `auto` 类型推导得到的，类型是 `int*`（因为 `ptr` 是 `int*`）。
   - `ptr2` 是一个指针，是一个左值（Lvalue）。
   - **注释解释**：`ptr2 is an l-value reference`。
     - 这里的注释有点误导，实际上 `ptr2` 是一个左值变量，而不是左值引用。
     - 它是一个普通指针，指向 `ptr` 所指向的地址。

   ```cpp
   auto&& v4 = v1[1] + v2;
   ```
   - `v4` 是一个右值引用，`auto&&` 的类型推导会根据右值表达式来推导出 `int&&` 类型。
   - `v1[1] + v2` 是一个右值表达式，因为它的结果是一个临时值。
   - `v4` 绑定到一个临时右值结果，这就是为什么 `v4` 是右值引用。
   - **注释解释**：`v4 is an r-value reference`。

   ```cpp
   auto& ptr3 = &val;
   ```
   - `&val` 是一个右值表达式，它返回一个指针，这里是 `int*` 类型。
   - 由于 `&val` 是一个右值，不能绑定到一个左值引用（`auto&`）。
   - **注释解释**：`ERROR: can't bind l-val ref to r-value`
     - 试图将一个左值引用（`auto&`）绑定到一个右值（`&val`）上，这是非法的。

   ```cpp
   auto&& val2 = val;
   ```
   - `val` 是一个左值，它可以绑定到一个右值引用（`auto&&`）上。
   - 这是因为 `auto&&` 会推导出一个左值引用。
   - **注释解释**：`ERROR: can't bind r-val ref to l-value`
     - 这个注释看起来是错误的，因为 `auto&&` 可以绑定到 `val`（一个左值）上，这是 C++11 中的完美转发（forwarding references）。
     - 正确的解释应该是：`val2` 是一个右值引用，但在这种情况下它会被推导为左值引用。

   ```cpp
   const auto& ptr3 = ptr + 5;
   ```
   - `ptr + 5` 是一个右值表达式，结果是一个新的指针值。
   - `const auto&` 可以绑定到右值，这是合法的，因为它是 `const` 左值引用。
   - **注释解释**：`OKAY: CAN bind const l-val ref to r-value (WHY?)`
     - 这是因为 `const` 左值引用可以延长临时对象的生命周期，所以它可以绑定到右值表达式。

### 总结

- **左值** 是指对象的内存地址，它们在表达式中可以出现在赋值号左边，可以取地址。
- **右值** 是没有内存地址的临时对象，通常不能被修改或取地址。
- **左值引用**（`auto&`）可以绑定到左值，但不能绑定到右值。
- **右值引用**（`auto&&`）可以绑定到右值，可以实现移动语义。
- - **常量左值引用**（如 `const_Lref`）可以绑定到**左值**和**右值**，因为它不会修改绑定的对象，并且可以延长临时右值的生命周期。

**注释中的错误**:
- `auto&& val2 = val;` 的注释是错误的，因为 `auto&&` 在这里作为一个万能引用（forwarding reference），它可以绑定到左值 `val`，并推导出 `int&` 类型。
# 4.Inherent
在 C++ 中，**继承**、**虚函数**、**纯虚函数**和**抽象类**是面向对象编程的核心概念，它们用于实现**多态性**，即不同类型的对象可以通过相同的接口进行操作。下面我会详细解释这些概念：

### 1. **继承（Inheritance）**
继承是面向对象编程的一个机制，允许一个类从另一个类派生，从而获得父类的属性和方法。子类可以直接使用父类的功能，也可以根据需要进行**重写（override）**。

- **基类（Base class）**：也称为父类或超类，是被继承的类。
- **派生类（Derived class）**：也称为子类，从基类继承的类。

#### 例子：
```cpp
class Animal {
public:
    void eat() {
        std::cout << "Animal eats food." << std::endl;
    }
};

class Dog : public Animal {
public:
    void bark() {
        std::cout << "Dog barks." << std::endl;
    }
};
```
在这个例子中，`Dog` 类继承了 `Animal` 类，因此它也拥有 `eat()` 方法，并且可以定义自己的 `bark()` 方法。

### 2. **虚函数（Virtual Function）**
虚函数是指在基类中声明的函数，旨在通过**指针或引用**在运行时实现**多态性**。通过在基类中使用 `virtual` 关键字修饰函数，派生类可以重写该函数，并且在运行时会根据对象的实际类型调用合适的版本。

#### 例子：
```cpp
class Animal {
public:
    virtual void makeSound() {
        std::cout << "Animal makes sound." << std::endl;
    }
};

class Dog : public Animal {
public:
    void makeSound() override { // 重写基类的虚函数
        std::cout << "Dog barks." << std::endl;
    }
};
```

- **基类** `Animal` 定义了一个虚函数 `makeSound()`。
- **派生类** `Dog` 重写了这个虚函数。
- 通过基类指针调用时，会根据指向的对象类型调用相应的版本（即使该指针类型是基类）。

#### 多态性示例：
```cpp
Animal* animal = new Dog();
animal->makeSound();  // 输出：Dog barks.
```
- 虽然 `animal` 是 `Animal*` 类型，但它指向的是一个 `Dog` 对象，因此会调用 `Dog` 类的 `makeSound()` 函数，而不是 `Animal` 类的版本。

### 3. **纯虚函数（Pure Virtual Function）**
纯虚函数是没有实现的虚函数。一个包含纯虚函数的类是**抽象类**，不能直接实例化。纯虚函数的声明通过在函数签名后面添加 `= 0` 来实现。

#### 例子：
```cpp
class Animal {
public:
    virtual void makeSound() = 0;  // 纯虚函数
};
```

- `Animal` 类中 `makeSound()` 函数是纯虚函数，这意味着派生类必须**实现**它，否则派生类也将成为抽象类。
- 如果某个派生类没有实现所有纯虚函数，则该派生类也是抽象类，不能被实例化。

### 4. **抽象类（Abstract Class）**
抽象类是指包含一个或多个纯虚函数的类。抽象类不能直接实例化，只能通过继承并实现所有纯虚函数后才能创建派生类的对象。

#### 例子：
```cpp
class Animal {
public:
    virtual void makeSound() = 0;  // 纯虚函数
};

class Dog : public Animal {
public:
    void makeSound() override {    // 实现纯虚函数
        std::cout << "Dog barks." << std::endl;
    }
};
```

- **`Animal` 类是抽象类**，因为它包含一个纯虚函数 `makeSound()`。
- **`Dog` 类是具体类**，因为它实现了 `Animal` 类的纯虚函数。
- `Animal` 不能被实例化，而 `Dog` 可以被实例化。

```cpp
Animal* animal = new Dog(); // 可以通过多态实现
animal->makeSound();  // 输出：Dog barks.
```

### 5. **虚函数与纯虚函数的区别**
- **虚函数**：基类中有默认的实现，派生类可以选择重写，也可以使用基类的实现。
- **纯虚函数**：基类中没有实现，派生类必须提供自己的实现。

### 6. **总结**
- **继承**允许子类获得父类的属性和方法。
- **虚函数**通过指针或引用实现了**运行时多态**，派生类可以重写虚函数。
- **纯虚函数**是没有实现的虚函数，基类定义接口，派生类必须实现。
- **抽象类**包含纯虚函数，不能直接实例化，用于定义接口或基础功能。

这些概念共同构成了 C++ 中的面向对象编程，特别是在实现多态性和代码复用方面。

# 5.namespace
当然可以！关于命名空间（**namespace**）和命名冲突的概念在 C++ 中是非常重要的，尤其是在大型项目或者使用外部库时。通过命名空间，我们可以避免不同代码单元之间出现函数、变量或类名相同的情况。以下是与命名空间和命名冲突相关的知识点：

### 1. **命名空间（namespace）**
命名空间是一种**逻辑上划分代码**的方法，用于将一组代码封装在一个特定的命名空间中，防止与其他命名空间或全局作用域中的名称冲突。通过使用命名空间，可以在不同模块或库中使用相同的名称而不会引起冲突。

#### 定义和使用：
命名空间通过关键字 `namespace` 来声明，其语法如下：
```cpp
namespace MyNamespace {
    // 在命名空间中的代码
    int myFunction() {
        return 42;
    }
}
```

要使用命名空间中的元素，可以通过**作用域解析运算符** `::` 来访问：
```cpp
int result = MyNamespace::myFunction();
```

#### 示例：
```cpp
namespace Lecture {
    int count(const vector<int>& v) {
        int ctr = 0;
        for (auto i : v) {
            if (i == 1) {
                ++ctr;
            }
        }
        return ctr;
    }
}
```
在上述例子中，`Lecture::count` 函数定义在 `Lecture` 命名空间中，这样就可以和其他同名的 `count` 函数（例如标准库中的 `std::count`）区分开来。

### 2. **命名冲突**
命名冲突是指在同一个作用域内定义了多个相同名字的实体（例如函数、变量、类等）。在 C++ 中，如果没有使用命名空间进行区分，不同的库、模块或代码可能会定义相同名称的函数或变量，这会导致冲突，编译器无法知道该调用哪个函数或使用哪个变量。

#### 命名冲突的例子：
```cpp
int count(const std::vector<int>& v) {
    return std::count(v.begin(), v.end(), 1); // 调用标准库的 count
}

int count(const std::vector<int>& v) {
    int ctr = 0;
    for (auto i : v) {
        if (i == 1) {
            ++ctr;
        }
    }
    return ctr; // 自定义的 count 函数
}
```
如果我们定义了两个 `count` 函数而没有使用命名空间，编译器将不知道该选择哪个 `count` 函数来调用，导致编译错误。

### 3. **如何避免命名冲突**
C++ 提供了几种避免命名冲突的方法，最常见的就是使用**命名空间**。

#### 使用命名空间避免冲突：
将可能导致冲突的代码置于不同的命名空间中，便可以清晰地分隔不同模块的代码，防止名称冲突。例如：
```cpp
namespace MyLibrary {
    int count(const std::vector<int>& v) {
        int ctr = 0;
        for (auto i : v) {
            if (i == 1) {
                ++ctr;
            }
        }
        return ctr;
    }
}
```

这样，即使其他地方有同名的 `count` 函数，只要使用 `MyLibrary::count`，就不会有冲突。

### 4. **命名空间的嵌套**
命名空间可以嵌套，以便进一步组织代码。例如，你可以将某个库的所有组件放在一个顶级命名空间下，然后根据模块进一步细分：
```cpp
namespace Library {
    namespace Math {
        int add(int a, int b) {
            return a + b;
        }
    }
    namespace Utils {
        int multiply(int a, int b) {
            return a * b;
        }
    }
}
```

调用时，可以通过完整路径访问嵌套命名空间中的函数：
```cpp
int result = Library::Math::add(3, 5);
```

### 5. **`using` 声明**
在某些情况下，我们希望简化命名空间的使用，可以通过 `using` 关键字将命名空间引入当前作用域。例如：
```cpp
using namespace std;
```

引入后，可以直接使用标准库中的函数和类型，而无需每次使用 `std::` 前缀。例如：
```cpp
vector<int> v;  // 不需要 std::vector
cout << "Hello, World!";  // 不需要 std::cout
```

然而，**不推荐**在全局作用域中使用 `using namespace`，尤其是在大型项目中，因为这会增加命名冲突的风险。较好的做法是仅在**局部作用域**中使用，或者直接为特定的类型或函数使用 `using` 声明：
```cpp
using std::cout;
using std::vector;
```

### 6. **标准库中的命名空间：`std`**
C++ 标准库的所有内容都被封装在 `std` 命名空间中，例如 `std::cout`、`std::vector`、`std::string` 等。这是为了防止标准库的名称与用户自定义的名称发生冲突。如果 C++ 标准库没有使用命名空间，则像 `cout`、`string` 这样的常见名称会很容易与用户代码中的名字冲突。

例如，以下是标准库的命名空间使用：
```cpp
std::cout << "Hello, World!" << std::endl;
std::vector<int> v = {1, 2, 3};
```

### 7. **匿名命名空间**
在某些情况下，我们希望某个命名空间中的内容只在特定的翻译单元内可见。匿名命名空间（`namespace {}`）可以实现这种目的，其作用相当于在 C 中使用 `static` 关键字来限制函数或变量的可见性。
```cpp
namespace {
    int helperFunction() {
        return 42;
    }
}
```
在这个例子中，`helperFunction` 只能在其定义所在的源文件中使用，其他文件不能访问它。

### 总结
- **命名空间（namespace）**用于组织代码，避免名称冲突。
- **命名冲突**发生在两个相同名称的实体在同一个作用域中被定义，导致编译错误。
- 通过**命名空间**可以将代码划分成不同的逻辑单元，避免冲突，增强代码的可读性和可维护性。
- **`using` 声明**可以简化命名空间的使用，但需谨慎，特别是在全局作用域中。
- C++ 标准库使用 `std` 命名空间，避免了常见名称的冲突。

命名空间是一个强大的工具，特别是在大型项目中，可以帮助你有效管理代码结构，避免复杂的命名冲突问题。

# 6.Extended Discussion
在C++20中引入了**Concepts（概念）**和**Constraints（约束）**，用于对模板参数进行约束和检查，帮助编译器更早发现模板代码中的错误，并让代码更具可读性和表达能力。

### 1. **Concepts（概念）**
Concepts 是用于定义模板参数必须满足的条件或属性。例如，要求某个模板参数必须是整数类型、浮点数类型，或支持某些操作（如加法、比较等）。

#### 例子：定义一个简单的 Concept
```cpp
#include <concepts>

// 定义一个概念，要求类型 T 是整型
template <typename T>
concept Integral = std::is_integral_v<T>;
```

这个 `Integral` 概念确保模板参数 `T` 是一个整型类型。

#### 使用 Concept
你可以用 Concepts 来约束模板参数：

```cpp
// 使用概念约束模板函数，只允许整型类型
template <Integral T>
T add(T a, T b) {
    return a + b;
}

int main() {
    int x = 5, y = 10;
    double d = 5.5;
    
    add(x, y);  // 正确，x 和 y 是整数
    // add(d, d);  // 编译错误，d 是浮点数，不满足 Integral 概念
}
```

在上面的代码中，模板函数 `add` 被 `Integral` 概念约束，意味着它只能接受整型参数。这样可以避免某些类型不支持的操作导致的编译错误。

#### 常见 Concepts
C++20 标准库中还提供了一些常用的概念，例如：
- `std::integral`：整数类型。
- `std::floating_point`：浮点类型。
- `std::default_initializable`：能够默认初始化的类型。
- `std::assignable_from`：能够赋值的类型。

### 2. **Constraints（约束）**
Constraints 是基于 Concepts 应用在模板参数上的具体限制，通常通过 `requires` 关键字来实现。Constraints 让你能够更加灵活地控制模板实例化的条件。

#### 例子：使用 Constraints
```cpp
#include <concepts>

// 需要两个类型能够相加
template <typename T>
requires std::integral<T>  // 限制 T 必须是整型
T add(T a, T b) {
    return a + b;
}
```

在这个例子中，`requires` 表达式用于对模板函数 `add` 进行约束，确保参数类型 `T` 必须是 `std::integral`（即整数类型）。

#### requires 表达式
`requires` 关键字用于声明某种约束。你可以用 `requires` 直接描述条件：

```cpp
// 要求 T 支持 + 操作符
template <typename T>
requires requires(T a, T b) {
    a + b;  // 约束：T 类型必须支持 + 操作符
}
T add(T a, T b) {
    return a + b;
}
```

在这个例子中，`requires` 表达式确保类型 `T` 支持加法操作。

### 3. **Concepts vs Constraints 的关系**
- **Concepts** 是对类型性质的抽象描述，类似于一种“类型检查”机制。
- **Constraints** 则是使用这些概念来限制模板参数的实际条件。

### 总结
- **Concepts** 是对模板参数的条件描述，例如要求类型是整型、浮点型或支持某种操作。
- **Constraints** 则是通过 `requires` 或使用 Concepts 来对模板参数进行实际约束。

它们让模板代码更安全、易于维护，并且编译时错误信息更清晰易懂。