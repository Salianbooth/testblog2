---
title: C++ Primer learning
date: 2024-08-02 12:06:42
tags:
  - Cpp
  - Markdown
categories:
  - book
---

# C++ Primer



 # Chapter 2      Variables and Basic Types



## Table 2.1. C++: Arithmetic Types

![Clip_2024-06-22_16-18-35](https://gitee.com/salianbooth/picture/raw/master/202408021209868.png)



#### Signed and Unsigned Types

* **Except for bool and the extended character types, the integral types may be signed or unsigned. A signed type represents negative or positive numbers (including zero); an unsigned type represents only values greater than or equal to zero. The types int, short, long, and long long are all signed. We obtain the corresponding unsigned type by adding unsigned to the type, such as unsigned long. The type unsigned int may be abbreviated as unsigned.**



#### 2.1.3. Literals (字面值常量)

* A value, such as 42, is known as a literal because its value self-evident. Every literal has a type. The form and value of a literal determine its type.20 
  $$
  20 /* decimal */ 024 /* octal */ 0x14 /* hexadecimal */
  $$

##### Character and Character String Literals(字符和字符串字面值)

```c++
'a' // character literal
"Hello World!" // string literal
```



##### Escape Sequences（转义序列）

```
newline \n 
horizontal tab \t 
alert (bell) \a
vertical tab \v 
backspace \b 
double quote \"
backslash \\ 
question mark \? 
single quote \'
carriage return \r 
formfeed \f
```

## 2.2.2. Variable Declarations and Definitions



## const

顶层 `const` 是指用在指针本身上时，即指针本身是常量。它决定了指针本身是否可以被修改。例如：

```c++
int x = 10;
int *const ptr = &x;  // ptr 是一个常量指针，指向 x，不能改变 ptr 指向的地址

// 以下操作合法
*ptr = 20;  // 修改 ptr 指向的值为 20

// 以下操作不合法，会导致编译错误
// ptr = nullptr;  // 错误：ptr 是常量指针，不能改变其指向的地址
```

在这个例子中，`ptr` 是一个常量指针，意味着你可以通过 `ptr` 修改它指向的值（`*ptr = 20;`），但不能修改 `ptr` 本身指向的地址（`ptr = nullptr;` 是非法的）。



底层 `const` 则是指指针指向的对象是常量，即指针所指向的值不能被修改。

例如：

```c++
int x = 10;
const int *ptr = &x;  // ptr 是一个指向常量整数的指针，不能通过 ptr 修改 x 的值

// 以下操作合法
 int y = *ptr;  // 可以读取 ptr 指向的值，即 x 的值

// 以下操作不合法，会导致编译错误
// *ptr = 20;  // 错误：ptr 指向的是常量，不能修改其指向的值
```

### 区别总结

- **顶层 `const`**：修饰的是指针本身，表示指针本身是常量，不可修改指向的地址。
- **底层 `const`**：修饰的是指针指向的对象，表示指针指向的值是常量，不可修改该值。

理解这两个概念对于正确理解和使用 C++ 中的 `const` 修饰符非常重要，特别是在处理指针和函数参数时，它们可以帮助你确保代码的正确性和安全性。

# Chapter 3. Strings, Vectors, and Arrays

## 3.4.1. Using Iterators

### Iterator Operations

![Clip_2024-06-26_20-56-56](https://gitee.com/salianbooth/picture/raw/master/202406262056997.png)



迭代器就像是对于STL标准库中的String和Vector这些对象的指针

```c++
vector<int> v;
const vector<int> cv;
auto it1 = v.begin(); // it1 has type vector<int>::iterator
auto it2 = cv.begin(); // it2 has type vector<int>::const_iterator

(*it).empty() // dereferences it and calls the member empty on the resulting
object
*it.empty() // error: attempts to fetch the member named empty from it
 			// but it is an iterator and has no member named empty
    
// print each line in text up to the first blank line
for (auto it = text.cbegin();
 it != text.cend() && !it->empty(); ++it) cout << *it << endl;
```

end()指向的是最后一个元素的下一个指针

**注意：迭代器与迭代器之间没有加法运算**

![Clip_2024-06-26_21-01-08](https://gitee.com/salianbooth/picture/raw/master/202406262101025.png)

## 3.5. Arrays



Exercise 3.27: Assuming txt_size is a function that takes no arguments and returns an int value, which of the following definitions are illegal? Explain why.

```c++
unsigned buf_size = 1024;
(a) int ia[buf_size];
(b) int ia[4 * 7 - 14];
(c) int ia[txt_size()];
(d) char st[11] = "fundamental";
```

- **(a)** 非法，因为 `buf_size` 不是编译时常量。
- **(b)** 合法，因为 `4 * 7 - 14` 是编译时常量。
- **(c)** 非法，因为 `txt_size()` 是函数调用，不能在编译时确定其返回值。
- **(d)** 非法，因为数组 `st` 的大小不足以容纳字符串 `"fundamental"` 包括其末尾的空字符。

注意，在自己电脑的时候会显示有的合法，那是因为编译器拓展，要避免使用这样的非标准特性，因为这可能会在其他电脑上运行失效。

# Chapter 4 表达式

## 4.1 基础

**定义**

![Clip_2024-07-08_14-55-44](https://gitee.com/salianbooth/picture/raw/master/202407081455638.png)

**重载运算符**

![Clip_2024-07-08_14-58-57](https://gitee.com/salianbooth/picture/raw/master/202407081458843.png)

![Clip_2024-07-08_14-59-04](https://gitee.com/salianbooth/picture/raw/master/202407081459202.png)

# Chapter 6 函数

## 6.1 函数基础

### 形参和实参

​	**形参出现在函数定义的地方，形参列表可以包含0个、1个或多个 形参，多个形参之间以逗号分隔。形参规定了一个函数所接受数据的 类型和数量。 实参出现在函数调用的地方，实参的数量与形参一样多。实参的 主要作用是初始化形参，并且这种初始化过程是一一对应的，即第一 个实参初始化第一个形参、第二个实参初始化第二个形参，以此类 推。实参的类型必须与对应的形参类型匹配。**

**形参、局部变量以及局部静态变量**

- 形参是一种自动对象，函数开始时为形参申请内存空间，我们 用调用函数时提供的实参初始化形参对应的自动对象。
- 普通变量对应的自动对象也容易理解，我们在定义该变量的语 句处创建自动对象，如果定义语句提供了初始值，则用该值初始化； 否则，执行默认初始化。当该变量所在的块结束后，变量失效。
- 局部静态变量比较特殊，它的生命周期贯穿函数调用及之后的 时间。局部静态变量对应的对象称为局部静态对象，它的生命周期从 定义语句处开始，直到程序结束才终止。static

## 6.2 参数传递



### const 形参和实参

终于知道那些函数形参里面为什么会要有const

![Clip_2024-07-13_10-24-06](https://gitee.com/salianbooth/picture/raw/master/202407131024908.png)

错误原因：第二个fcn是第一个的重载，因为我们在调用第一个fcn函数时，能传入const int 和 int 着两种类型的参数，所以这两个函数被认为是同一个函数

![Clip_2024-07-13_10-29-43](https://gitee.com/salianbooth/picture/raw/master/202407131029738.png)

### main： 处理命令行选项

```c++
int main(int argc, char *argv[])
```

1. **`int argc`**：
   - `argc` 是一个整数参数，表示命令行参数的数量（argument count）。它至少为 1，因为第一个参数是程序的名称（即可执行文件的路径）。
2. **`char *argv[]`** 或 **`char **argv`**：
   - `argv` 是一个指向字符指针（`char*`）数组的指针。这些字符指针指向每个命令行参数的字符串。`argv[0]` 指向程序的名称，`argv[1]`、`argv[2]` 等依次指向传递给程序的其他参数

## 6.5 内联函数

​	内联函数（inline function）是 C++ 中的一种优化手段，通过将函数调用替换为函数代码，从而减少函数调用的开销。内联函数使用 inline 关键字声明，编译器会在合适的情况下将其展开到调用处。以下是内联函数的详细讲解及其使用场景。



### 什么是内联函数

内联函数是一种建议编译器在调用处展开的函数。与普通函数不同，内联函数在编译时将其代码直接插入到调用点，从而避免了函数调用的开销（如栈操作、跳转等）。

### 定义内联函数

内联函数通常在类定义中实现，也可以在类外定义时使用 `inline` 关键字。以下是两种方式的示例：

#### 类内定义

在类内部定义的成员函数，默认情况下是内联函数：

```c
class Example {
public:
    int getValue() const { return value; } // 内联函数
private:
    int value;
};
```

#### 类外定义

在类外部定义时，需要显式使用 `inline` 关键字：

```c
class Example {
public:
    int getValue() const;
private:
    int value;
};

inline int Example::getValue() const {
    return value;
}
```

### 内联函数的优点

1. 消除函数调用开销:
   - 内联函数将函数代码插入到调用处，消除了参数压栈、跳转和返回等函数调用开销。
2. 有助于编译器优化：
   - 内联展开可以使编译器更好地优化代码，例如消除不必要的变量、合并代码块等。

### 内联函数的缺点

1. **代码膨胀**：
   - 如果内联函数体较大且被多次调用，会导致生成的二进制代码膨胀，增加可执行文件的大小。
2. **内联并非强制**：
   - 内联是对编译器的建议，编译器可以选择忽略。如果函数体较大或编译器认为展开不合适，可能不会内联展开。

### 适用场景

- **简单、频繁调用的函数**：适用于函数体小且频繁调用的函数，如访问器（getter）和设置器（setter）。
- **时间关键代码**：在性能要求较高的代码中，可以使用内联函数减少函数调用开销。

### 使用注意事项

1. 避免过度使用:
   - 不要将大型函数声明为内联函数，否则会导致代码膨胀。
2. 递归函数:
   - 递归函数通常不适合作为内联函数，因为展开递归调用会导致无限膨胀。

# Chapter 7 类

## 7.2 访问控制与封装

### 友元函数

就是能使用private里面的函数

```c++
 friend SalesData add(const SalesData&,const SalesData&);
    friend ostream &print(ostream&,const SalesData&);
    friend istream &read(istream&,SalesData&);
```



## 7.3类的其他特性

### 不同的构造函数

1. **构造函数体内赋值**（你之前见过的形式）：

   ```c++
   SalesData(const std::string &s, unsigned n, double p) {
       this->bookNo = s;
       this->units_sold = n;
       this->renvenue = p * n;
   }
   ```

2. **成员初始化列表**（你当前的代码）：

```c++
SalesData(const std::string &s, unsigned n, double p) :
    bookNo(s), units_sold(n), renvenue(p * n) {}
```

#### 成员初始化列表的优势和必要性

成员初始化列表在构造函数中用来初始化成员变量，而不是在构造函数体内进行赋值。成员初始化列表有几个重要的优势和必要性：

1. **效率**：

   - 成员初始化列表直接初始化成员变量，而不是先调用默认构造函数再赋值。因此，对于复杂类型，成员初始化列表通常更高效。

2. **常量成员**：

   - 对于 `const` 成员变量（如你的 `bookNo`），必须在初始化列表中进行初始化，因为常量只能初始化一次，之后不能再赋值。

   ```c
   const std::string bookNo;
   SalesData(const std::string &s) : bookNo(s) {} // 必须使用初始化列表
   ```

3. **引用成员**：

   - 对于引用成员变量，也必须在初始化列表中进行初始化，因为引用必须在创建时绑定到一个对象，之后不能再更改。

   ```c
   const std::string &bookNo;
   SalesData(const std::string &s) : bookNo(s) {} // 必须使用初始化列表
   ```

4. **初始化顺序**：

   - 成员初始化列表按照成员在类中声明的顺序初始化，而不是列表中的顺序。这样可以避免成员之间依赖关系带来的问题。

#### 具体示例解释

你的类定义使用了成员初始化列表：

```c
SalesData(const std::string &s, unsigned n, double p) :
    bookNo(s), units_sold(n), renvenue(p * n) {}
```

这行代码的意思是：

- `bookNo(s)`：使用参数 `s` 初始化成员变量 `bookNo`。
- `units_sold(n)`：使用参数 `n` 初始化成员变量 `units_sold`。
- `renvenue(p * n)`：使用 `p * n` 初始化成员变量 `renvenue`。

如果使用构造函数体内赋值的方式，代码会是这样：

```c
SalesData(const std::string &s, unsigned n, double p) {
    bookNo = s;
    units_sold = n;
    renvenue = p * n;
}
```

虽然这在大多数情况下也能工作，但对于 `const` 或引用成员变量会导致编译错误，并且在效率上通常不如成员初始化列表。

#### 小结

- 成员初始化列表直接在成员变量创建时进行初始化，通常更高效，并且是初始化 `const` 和引用成员的唯一方法。
- 构造函数体内赋值方式在成员变量已经被默认构造函数初始化后再进行赋值，可能会产生额外的开销，且不能用于 `const` 和引用成员



### set和get函数



在C++中的类与对象中，`set`和`get`函数（有时称为“setter”和“getter”函数）通常用于访问和修改类的私有数据成员。这些函数提供了一种受控的方式来操作类的成员变量，从而增强了封装性和数据完整性。

#### `set`函数

`set`函数（或setter函数）用于设置类的私有数据成员的值。通过使用`set`函数，你可以控制对成员变量的修改，确保数据的一致性和有效性。通常，`set`函数会对输入值进行验证，防止无效数据被赋值给成员变量。

#### `get`函数

`get`函数（或getter函数）用于获取类的私有数据成员的值。通过使用`get`函数，你可以访问类的私有数据成员，而无需直接暴露这些成员变量。

#### 示例

以下是一个简单的例子，展示了如何在C++类中使用`set`和`get`函数：

```cpp
#include <iostream>
#include <string>

class Person {
private:
    std::string name;
    int age;

public:
    // Setter for name
    void setName(const std::string &n) {
        name = n;
    }

    // Getter for name
    std::string getName() const {
        return name;
    }

    // Setter for age
    void setAge(int a) {
        if (a >= 0) { // simple validation
            age = a;
        }
    }

    // Getter for age
    int getAge() const {
        return age;
    }
};

int main() {
    Person person;
    person.setName("Alice");
    person.setAge(30);

    std::cout << "Name: " << person.getName() << std::endl;
    std::cout << "Age: " << person.getAge() << std::endl;

    return 0;
}
```

在这个例子中：

- `Person`类有两个私有成员变量：`name`和`age`。
- `setName`函数用于设置`name`的值。
- `getName`函数用于获取`name`的值。
- `setAge`函数用于设置`age`的值，并对输入值进行简单验证（确保年龄不为负数）。
- `getAge`函数用于获取`age`的值。

#### 好处

使用`set`和`get`函数的好处包括：

1. **封装性**：通过将数据成员设置为私有，并使用公共的`set`和`get`函数访问它们，可以更好地封装数据，防止外部直接访问和修改数据。
2. **数据验证**：可以在`set`函数中添加数据验证逻辑，确保数据的有效性。
3. **维护性**：如果需要更改数据成员的内部表示，只需修改`set`和`get`函数的实现，而不需要修改使用这些函数的代码。

#### 总结

`set`和`get`函数是C++类中的常见方法，用于受控地访问和修改类的私有数据成员。它们通过提供数据封装和验证机制，提高了代码的健壮性和可维护性。

### 友元再探

#### 友元声明和作用域

![Clip_2024-07-18_17-59-07](https://gitee.com/salianbooth/picture/raw/master/202407181759430.png)

## 7.4 类的作用域

![Clip_2024-07-18_18-08-50](https://gitee.com/salianbooth/picture/raw/master/202407181808544.png)

## 7.5 构造函数再探

### 7.5.2 委托构造函数

成员初始值列表只有一个唯一的入口，就是类名本身。

![Clip_2024-07-18_18-48-28](https://gitee.com/salianbooth/picture/raw/master/202407181848600.png)

### 7.5.3 默认构造函数的作用

对象被默认初始化会自动执行默认的构造函数

![Clip_2024-07-18_18-49-48](https://gitee.com/salianbooth/picture/raw/master/202407181849206.png)

### 7.5.5 聚合类

​	**聚合类**使得用户可以直接访问其成员，并且具有特殊的初始化语法形式。

- 所有成员都是public
- 没有定义任何构造函数
- 没有类内初始值
- 没有基类，没有virtual函数

### 7.5.6 字面值常量类

常量表达式（constant expressions）是在编译时就能够被计算出来的表达式。

```c++
constexpr int max_size = 100; // 常量表达式声明

int arr[max_size]; // 使用常量表达式来定义数组大小
```

在上面的示例中，`max_size`是一个常量表达式，它的值在编译时就被确定了。它被用于定义数组的大小、枚举类中的成员、以及模板函数的参数中。



#### **constexper **   

它用于声明可以在编译时求值的常量表达式函数或变量。`constexpr` 函数的返回值在编译时是确定的，这使得编译器可以在编译过程中进行更多的优化，从而提高程序的性能。

`constexpr` 变量必须在声明时初始化，并且初始化表达式必须是常量表达式。

##### `constexpr` 函数

`constexpr` 函数可以用于定义在编译时计算结果的函数。所有在 `constexpr` 函数中的代码必须是常量表达式。

```c++
constexpr int add(int a, int b) {
    return a + b;
}

int main() {
    constexpr int result = add(3, 4); // 在编译时求值
    return 0;
}
```

1. **数据成员都是字面值类型**：
   - 类的所有数据成员必须是字面值类型。字面值类型是可以在编译时确定的类型，如整数、浮点数、指针等。
2. **类必须至少含有一个 `constexpr` 构造函数**：
   - 类需要至少有一个 `constexpr` 构造函数，这意味着该构造函数可以在编译时常量表达式中使用。
3. **类的数据成员初始化**：
   - 如果一个数据成员含有类内初始值，则初始值必须是一条常量表达式，或者该成员属于某种类类型，则初始值必须使用成员自己的 `constexpr` 构造函数。
4. **析构函数的默认定义**：
   - 类必须使用析构函数的默认定义，该成员负责销毁段类的对象。这意味着析构函数应该是编译器生成的默认析构函数，而不是自定义的析构函数。

这些要求确保了类在编译时可以完全确定，从而使类的对象可以在编译时创建和使用。

# Chapter 08 I/O库

 ## 8.1 .1条件状态（condition state）



在C++的输入/输出（I/O）库中，条件状态（condition state）是指流对象的当前状态，表示流在操作过程中是否遇到了错误或特殊情况。这些状态标志有助于程序员了解I/O操作是否成功，并采取适当的措施进行处理。C++标准库通过`std::ios`类提供了一组流状态标志，用于表示和检查这些条件状态。

### 流状态标志

以下是主要的流状态标志及其含义：

1. **eofbit**（end-of-file bit）

   - 表示流到达文件末尾。
   - 当读取操作尝试从流中读取数据，但已到达文件末尾时设置。
   - 可以使用`eof()`成员函数检查。

   ```c++
   if (inputFile.eof()) {
       std::cout << "Reached end of file." << std::endl;
   }
   ```

2. **failbit**（fail bit）

   - 表示一次非致命性格式错误或其他输入/输出操作失败。
   - 通常在尝试读取类型不匹配的数据时设置。
   - 可以使用`fail()`成员函数检查。

   ```c++
   if (inputFile.fail()) {
       std::cout << "Input operation failed." << std::endl;
   }
   ```

3. **badbit**（bad bit）

   - 表示流出现不可恢复的错误，如物理读写错误。
   - 一旦设置，流通常无法再继续使用。
   - 可以使用`bad()`成员函数检查。

   ```c++
   if (inputFile.bad()) {
       std::cout << "Irrecoverable stream error." << std::endl;
   }
   ```

4. **goodbit**（good bit）

   - 表示流状态正常，没有错误。
   - 可以使用`good()`成员函数检查。

   ```c++
   if (inputFile.good()) {
       std::cout << "Stream is in good state." << std::endl;
   }
   ```

### 检查和重置流状态

- **检查流状态**：可以通过流对象的成员函数来检查不同的流状态。

- 清除状态标志

  ：可以使用

  ```c++
  clear()
  ```

  成员函数重置流的状态标志，使其回到有效状态。

  ```c++
  inputFile.clear(); // 清除所有错误标志
  ```

## 8.1.2 关联输入和输出

### `ie`函数的作用

在C++标准库中，`tie`函数用于将一个`istream`对象与一个`ostream`对象关联起来。这样做的目的是在每次读取输入流（如`cin`）时，自动刷新（flush）输出流（如`cout`），确保输出流中的所有数据都已经输出到终端或文件。

### 代码解析

图片中的代码展示了如何使用`tie`函数来关联和解除关联流对象：

```
cin.tie(&cout);  // 将cin与cout关联起来
```

- 这段代码将`cin`与`cout`关联起来。标准库默认情况下已经将`cin`和`cout`关联在一起，因此这行代码只是展示了如何进行这种关联。

```
ostream *old_tie = cin.tie(nullptr);  // 解除cin与其他流的关联
```

- 这行代码将`cin`与所有输出流解除关联。函数`cin.tie(nullptr)`返回的是之前`cin`关联的输出流指针，这里存储在`old_tie`中，以便稍后恢复这种关联。

```
cin.tie(&cerr);  // 将cin与cerr关联起来
```

- 这行代码将`cin`与`cerr`（标准错误输出流）关联起来。这意味着每次从`cin`读取输入时，都会刷新`cerr`而不是`cout`。这种做法一般不推荐，因为标准输入`cin`通常应该与标准输出`cout`关联。

```
cin.tie(old_tie);  // 恢复cin和cout之间的正常关联
```

- 这行代码恢复了之前`cin`和`cout`之间的关联，将`cin`重新与之前保存的流（即`cout`）关联。

### 说明

在这段代码中，通过将一个给定的流关联到一个新的输出流，我们使用新的指针传递给`tie`函数。为了彻底解除流的关联，我们传递了一个空指针（`nullptr`）。每个流同时最多只能关联到一个输出流，但多个流可以同时关联到同一个输出流。

#### 关联流的用途

- 主要用于确保输入流读取之前，输出流中的数据已被刷新。
- 常见用例是将`cin`和`cout`关联在一起，以确保用户在输入数据之前看到所有的提示信息。

通过这段代码和解释，你可以更好地理解如何在C++中管理输入输出流的关联，以及为什么这种关联有助于确保数据的正确读写顺序。

## 8.2 文件的输入和输出

在C++中，处理文件输入和输出的主要类是`ifstream`（输入文件流）和`ofstream`（输出文件流），它们分别继承自`istream`和`ostream`。

#### 8.2.1 使用文件流对象

为了从文件中读取数据或向文件中写入数据，首先需要创建一个`ifstream`或`ofstream`对象并打开文件。

**打开文件**

- ifstream和ofstream对象在创建时可以直接打开文件，方法是将文件名作为构造函数的参数传递：

  ```
  std::ifstream inputFile("input.txt");  // 打开用于读的文件
  std::ofstream outputFile("output.txt");  // 打开用于写的文件
  ```

- 也可以使用open成员函数打开文件：

  ```
  std::ifstream inputFile;
  inputFile.open("input.txt");
  
  std::ofstream outputFile;
  outputFile.open("output.txt");
  ```

**检查文件是否打开成功**

- 文件流对象可以转换为一个布尔值，以检查文件是否成功打开：

  ```
  if (!inputFile) {
      std::cerr << "Failed to open input file" << std::endl;
  }
  if (!outputFile) {
      std::cerr << "Failed to open output file" << std::endl;
  }
  ```

#### 8.2.2 文件输入和输出

**读取文件**

- 可以使用标准输入流操作符>>或getline函数从文件中读取数据：

  ```
  std::string line;
  while (std::getline(inputFile, line)) {
      std::cout << line << std::endl;
  }
  ```

**写入文件**

- 可以使用标准输出流操作符<<向文件中写入数据：

  ```
  outputFile << "Hello, file!" << std::endl;
  ```

**文件流的关闭**

- 文件流在析构时会自动关闭，但也可以显式地调用close函数关闭文件：

  ```
  inputFile.close();
  outputFile.close();
  ```

#### 8.2.3 文件模式

文件流可以用不同的模式打开，以控制文件的读写行为。常用的文件模式如下：

- `std::ios::in`：以读模式打开文件（`ifstream`默认）。
- `std::ios::out`：以写模式打开文件（`ofstream`默认）。
- `std::ios::app`：以追加模式打开文件，写入的数据会追加到文件末尾。
- `std::ios::ate`：打开文件并定位到文件末尾。
- `std::ios::trunc`：如果文件已存在，截断文件长度为0。
- `std::ios::binary`：以二进制模式打开文件。

这些模式可以组合使用，例如：

```
std::ofstream outputFile("output.txt", std::ios::out | std::ios::app);
```

#### 8.2.4 随机访问文件

C++文件流提供了一些函数，可以在文件中随机访问数据：

- `seekg`：在输入文件流中定位到指定位置。
- `seekp`：在输出文件流中定位到指定位置。
- `tellg`：返回输入文件流中的当前位置。
- `tellp`：返回输出文件流中的当前位置。

示例：

```
inputFile.seekg(0, std::ios::beg);  // 定位到文件开始
std::streampos pos = inputFile.tellg();  // 获取当前位置
inputFile.seekg(pos);  // 定位到pos
```

通过这些函数，可以在文件中任意位置进行读写操作。

# Chapter 9 顺序容器

## 9.1 顺序容器概述

**Tips：使用vector是最好的选择，除非有更好的理由选择其他**

### 选择容器的基本原则

1. **默认选择`vector`**：
   除非你有很好的理由选择其他容器，否则应该使用`vector`。
2. **元素数量多且空间重要时**：
   如果你的程序有很多小的元素，并且空间的额外开销很重要，则不要使用`list`或`forward_list`。
3. **随机访问元素时**：
   如果程序要求随机访问元素，应使用`vector`或`deque`。
4. **中间插入或删除元素时**：
   如果程序要求在容器的中间插入或删除元素，应使用`list`或`forward_list`。
5. **头尾插入或删除但不在中间操作时**：
   如果程序需要在头尾位置插入或删除元素，但不会在中间位置进行插入或删除操作，则使用`deque`。
6. **读输入时才需要插入并随机访问时**：
   如果程序只有在读取输入时才需要在容器中间插入元素，随后需要随机访问元素，则：
   - 首先，确定是否真的需要在容器中间位置添加元素。当处理输入数据时，通常可以很容易地向`vector`添加数据，然后再调用标准库的`sort`函数来重新排序容器中的元素，从而避免在中间位置添加元素。
   - 如果必须在中间位置插入元素，考虑在输入阶段使用`list`，一旦输入完成，将`list`中的内容拷贝到一个`vector`中。

## 9.2 容器库概述

#### 9.2.1 迭代器

顺序容器提供了迭代器，用于遍历和操作容器中的元素。常用的迭代器类型包括：

- **`iterator`**：读写访问。
- **`const_iterator`**：只读访问。
- **`reverse_iterator`**：反向读写访问。
- **`const_reverse_iterator`**：反向只读访问。

示例代码：

```c++
#include <iostream>
#include <vector>

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5};

    for (std::vector<int>::iterator it = vec.begin(); it != vec.end(); ++it) {
        std::cout << *it << " ";
    }
    std::cout << std::endl;

    return 0;
}
```

#### 9.2.2 容器的大小操作

顺序容器提供了多种方法来获取和修改容器的大小：

- **`size`**：返回容器中元素的数量。
- **`empty`**：检查容器是否为空。
- **`max_size`**：返回容器能够容纳的最大元素数量。
- **`resize`**：改变容器的大小。

示例代码：

```c++
#include <iostream>
#include <vector>

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5};
    std::cout << "Size: " << vec.size() << std::endl;
    std::cout << "Is empty: " << vec.empty() << std::endl;

    vec.resize(10);
    std::cout << "Resized vector size: " << vec.size() << std::endl;

    return 0;
}
```

#### 9.2.3 添加元素

顺序容器提供了多种方法来添加元素：

- **`push_back`**：在容器末尾添加元素。
- **`insert`**：在指定位置插入元素。
- **`emplace_back`**：在容器末尾原地构造元素。
- **`emplace`**：在指定位置原地构造元素。

示例代码：

```c++
#include <iostream>
#include <vector>

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5};
    vec.push_back(6);
    vec.insert(vec.begin(), 0);
    vec.emplace_back(7);

    for (int n : vec) {
        std::cout << n << " ";
    }
    std::cout << std::endl;

    return 0;
}
```

#### 9.2.4 删除元素

顺序容器提供了多种方法来删除元素：

- **`pop_back`**：删除容器末尾的元素。
- **`erase`**：删除指定位置的元素或范围内的元素。
- **`clear`**：删除容器中的所有元素。

示例代码：

```c++
#include <iostream>
#include <vector>

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5};
    vec.pop_back();
    vec.erase(vec.begin());

    for (int n : vec) {
        std::cout << n << " ";
    }
    std::cout << std::endl;

    vec.clear();
    std::cout << "Size after clear: " << vec.size() << std::endl;

    return 0;
}
```

#### 9.2.5 访问元素

顺序容器提供了多种方法来访问元素：

- **`operator[]`**：随机访问指定位置的元素。
- **`at`**：随机访问指定位置的元素，并进行边界检查。
- **`front`**：访问第一个元素。
- **`back`**：访问最后一个元素。

示例代码：

```c++
#include <iostream>
#include <vector>

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5};

    std::cout << "First element: " << vec.front() << std::endl;
    std::cout << "Last element: " << vec.back() << std::endl;
    std::cout << "Element at index 2: " << vec[2] << std::endl;
    std::cout << "Element at index 3: " << vec.at(3) << std::endl;

    return 0;
}
```

## 9.3 顺序容器操作

### 9.3.1 添加元素

#### `emplace` 系列函数的基本概念

`emplace` 系列函数将传入的参数直接传递给对象的构造函数，以在容器的特定位置构造对象。这种方式能够在避免临时对象的情况下高效地将新元素添加到容器中。

#### 常用的 `emplace` 系列函数

1. **`emplace_back`**：

   - 在容器末尾直接构造对象。

   ```c++
   std::vector<std::pair<int, std::string>> vec;
   vec.emplace_back(42, "hello"); // 直接在末尾构造一个 std::pair<int, std::string>
   ```

2. **`emplace`**：

   - 在容器的指定位置直接构造对象。

   ```c++
   std::list<std::string> lst;
   auto it = lst.emplace(lst.begin(), "world"); // 在开头直接构造一个 std::string
   ```

#### `emplace` 系列函数的优势

- **避免不必要的拷贝或移动**：`emplace` 系列函数直接在容器内部构造对象，省去了拷贝或移动对象所带来的开销。
- **简化代码**：使用 `emplace` 系列函数可以省略创建临时对象的步骤，代码更加简洁。

#### 使用示例

假设有一个自定义类 `Person`：

```c++
class Person {
public:
    Person(const std::string& name, int age) : name(name), age(age) {}
private:
    std::string name;
    int age;
};
```

可以使用 `emplace_back` 直接在容器中构造 `Person` 对象：

```c++
std::vector<Person> people;
people.emplace_back("Alice", 30); // 直接在末尾构造一个 Person 对象
```

与 `push_back` 对比：

```c++
std::vector<Person> people;
people.push_back(Person("Alice", 30)); // 先创建临时对象，再拷贝到容器中
```

**总之** emplace的主要优势在于 **构造类对象**

### 9.3.6 容器操作会使迭代器失效

#### 容器操作及其对迭代器失效的影响

1. **`vector` 和 `string`**：
   - **增加元素**：当 `vector` 或 `string` 需要重新分配内存时，所有的迭代器、引用和指针都会失效。
   - **删除元素**：从 `vector` 或 `string` 中删除元素时，指向删除点及其后的迭代器、引用和指针都会失效。
2. **`deque`**：
   - **增加元素**：在头部或尾部增加元素时，所有迭代器都会失效。
   - **删除元素**：在头部或尾部删除元素时，所有迭代器都会失效。
   - **中间插入或删除**：只会使指向操作点及其后的迭代器失效。
3. **`list` 和 `forward_list`**：
   - 由于 `list` 是双向链表，`forward_list` 是单向链表，它们的节点在内存中不连续，增加或删除元素时，指向其他节点的迭代器不会失效，除非是指向被删除节点的迭代器。
4. **`associative containers` (如 `set`, `map` 等)**：
   - 增加或删除元素时，指向其他元素的迭代器不会失效，除非是指向被删除元素的迭代器。
5. **`unordered containers`**：
   - 插入元素可能会导致容器重新哈希，从而使所有迭代器失效。
   - 删除元素时，指向其他元素的迭代器不会失效，除非是指向被删除元素的迭代器。

```c++
int main(){
    vector<int> v = {1,2,3,4,5};
    for(auto it = v.begin();it < v.end(); it++){
        cout<<"========" << endl;
        cout<<"v capacity:" << v.capacity()<<endl;
        cout<<*it<<endl;
        if((*it) == 2){
            cout<<"starting pushing " << endl;
            v.push_back(100);
        }
    }

    return 0;
}
```

```c++
========
v capacity:5
1
========
v capacity:5
2
starting pushing   //在这里，我们push_back了一个新元素进去
========
v capacity:10  //这个时候，v的容量变成了原来的2倍，也就是10
-1434582704
========
v capacity:10
606
========
v capacity:10  //旧的迭代器结束
5
========
v capacity:10    //中间多了3个其他的数值，来区分开来新旧迭代器
0
========
v capacity:10
-1174562285
========
v capacity:10
134280406
========
v capacity:10   //新的迭代器
1
========
v capacity:10
2
starting pushing
========
v capacity:10
3
========
v capacity:10
4
========
v capacity:10
5
========
v capacity:10
100
========
v capacity:10
100
```

## 9.4 vector是如何增长的

### 1. **检查是否需要扩容**

当我们向`vector`中添加元素时，首先会检查当前的大小是否已经达到了容量。如果没有，则元素可以直接添加，无需分配新的内存。

### 2. **确定新的容量**

如果`size`已经等于`capacity`，则`vector`需要增加容量。新的容量通常是现有容量的两倍，但这并非标准规定的行为，而是一种常见的实现策略。这个策略的优点是减少了频繁分配内存的开销。

### 3. **分配新内存并复制旧数据**

`vector`会分配一块新的内存空间，其大小等于新的容量。然后，它会将旧内存中的元素逐个复制到新内存中。这是因为标准要求元素要在与它们被销毁的地方不同的内存位置构造。复制完成后，旧的内存会被释放。

### 4. **调整内部指针**

新内存中的元素构造完成后，`vector`的内部指针和相关数据成员会更新，以反映新的容量和内存地址。

### 5. **更新容量和大小**

扩容完成后，`vector`的`capacity`会被更新为新的容量，而`size`会增加到包含新元素的大小。

```c++
int main(int argc,char** argv){

    vector<int> vec;

    int prev_capacity = 0;
    for(int i = 0; i < 1000000; i++){
        if(prev_capacity != vec.capacity()){
            cout<<"changed ->" << vec.capacity()<<endl;
            prev_capacity = vec.capacity();
        }
        vec.push_back(i);
    }


    return 0;
}
```

## 9.5 String的额外操作

### 1. **修改操作**

#### **插入操作**

- **插入字符或字符串**：可以使用`insert`方法在指定位置插入字符或字符串。

```c++
std::string str = "Hello";
str.insert(5, " World"); // str变为 "Hello World"
str.insert(0, 1, 'X');   // str变为 "XHello World"
```

#### **删除操作**

- **删除子串**：可以使用`erase`方法从指定位置开始删除指定长度的字符。

```c++
std::string str = "Hello World";
str.erase(5, 6); // str变为 "Hello"
```

#### **替换操作**

- **替换子串**：可以使用`replace`方法将指定位置的子串替换为另一个字符串。

```c++
std::string str = "Hello World";
str.replace(6, 5, "C++"); // str变为 "Hello C++"
```

### 2. **搜索操作**

#### **查找字符或子串**

- **查找字符**：可以使用`find`方法查找字符或字符串的第一次出现位置。

```c++
std::string str = "Hello World";
size_t pos = str.find('W');   // pos为6
pos = str.find("World");      // pos为6
pos = str.find('X');          // pos为std::string::npos（未找到）
```

- **从末尾查找**：可以使用`rfind`方法从字符串末尾开始查找字符或子串。

```c++
std::string str = "Hello World World";
size_t pos = str.rfind("World"); // pos为12
```

### 3. **比较操作**

#### **字符串比较**

- **比较字符串**：可以使用`compare`方法比较两个字符串。

```c++
std::string str1 = "Hello";
std::string str2 = "World";
int result = str1.compare(str2); // result < 0, 因为 "Hello" < "World"
result = str1.compare("Hello");  // result == 0, 因为字符串相同
```

### 4. **子串操作**

#### **获取子串**

- **提取子串**：可以使用`substr`方法从指定位置开始提取一定长度的子串。

```c++
std::string str = "Hello World";
std::string sub = str.substr(6, 5); // sub为 "World"
```

### 5. **大小写转换**

#### **转换为小写或大写**

C++标准库没有内置的方法来直接将`string`转换为大写或小写，但可以通过使用`<cctype>`库中的`std::tolower`和`std::toupper`函数来实现。

```c++
#include <cctype> // for std::tolower and std::toupper

std::string toLower(const std::string& str) {
    std::string result;
    for (char c : str) {
        result += std::tolower(c);
    }
    return result;
}

std::string toUpper(const std::string& str) {
    std::string result;
    for (char c : str) {
        result += std::toupper(c);
    }
    return result;
}

int main() {
    std::string str = "Hello World";
    std::string lower = toLower(str); // lower为 "hello world"
    std::string upper = toUpper(str); // upper为 "HELLO WORLD"
    return 0;
}
```

### 6. **字符串长度**

#### **获取长度**

- **长度和容量**：可以使用`size`或`length`方法获取字符串的长度，使用`capacity`方法获取分配的内存大小。

```c++
std::string str = "Hello World";
size_t len = str.size();    // len为11
size_t cap = str.capacity();// cap为分配的内存大小
```

### 7. **字符串连接**

#### **拼接字符串**

- **使用`+`操作符**：可以使用`+`操作符连接两个字符串。

```c++
std::string str1 = "Hello";
std::string str2 = "World";
std::string result = str1 + " " + str2; // result为 "Hello World"
```

- **使用`append`方法**：可以使用`append`方法将一个字符串追加到另一个字符串的末尾。

```c++
std::string str = "Hello";
str.append(" World"); // str变为 "Hello World"
```

### 8. **输入输出操作**

#### **从标准输入读取**

- **使用`getline`**：可以使用`getline`从标准输入读取一行。

```c++
std::string str;
std::getline(std::cin, str);
```

#### **输出到标准输出**

- **使用`<<`操作符**：可以使用`<<`操作符将字符串输出到标准输出。

```c++
std::string str = "Hello World";
std::cout << str << std::endl;
```

# Chapter10 泛型算法

## 10.1 泛型算法概述

泛型算法是独立于数据类型的算法，它们通过模板机制实现，主要用于标准容器。C++标准库提供了一组丰富的泛型算法，可以进行排序、搜索、变换等操作。

### 常用泛型算法

1. **排序算法**
   - `sort`：对范围内的元素进行排序。
   - `stable_sort`：保持相等元素的相对顺序的排序。
   - `partial_sort`：部分排序，仅使得部分元素有序。
2. **查找算法**
   - `find`：在范围内查找等于给定值的第一个元素。
   - `find_if`：根据谓词在范围内查找第一个满足条件的元素。
   - `binary_search`：二分查找，要求范围内元素有序。
3. **修改序列的算法**
   - `copy`：将一个范围内的元素复制到另一个范围。
   - `copy_if`：将满足条件的元素复制到另一个范围。
   - `replace`：将范围内所有等于某值的元素替换为另一个值。
4. **删除算法**
   - `remove`：移除所有等于某值的元素（不改变容器大小）。
   - `remove_if`：移除所有满足条件的元素（不改变容器大小）。
5. **变换算法**
   - `transform`：对范围内的元素应用给定操作，并将结果存储到另一个范围。
   - `accumulate`：计算范围内元素的累积和。

### 迭代器

迭代器是泛型算法的关键组件，STL提供了不同类型的迭代器：

- **输入迭代器**：只读，单遍历。
- **输出迭代器**：只写，单遍历。
- **前向迭代器**：可读可写，多遍历。
- **双向迭代器**：前向迭代器的扩展，可向前和向后遍历。
- **随机访问迭代器**：支持常量时间的随机访问。

## 绑定器的作用

绑定器可以将函数的一部分参数预先绑定，然后生成一个新的可调用对象，这个对象可以在需要时提供其余的参数。这在需要部分应用函数时特别有用，例如与泛型算法结合使用。

### `std::bind` 的使用

`std::bind` 函数在头文件 `<functional>` 中定义，其用法如下：

```
auto bound_function = std::bind(function, arg1, arg2, ...);
```

- `function`：要绑定的函数，可以是普通函数、成员函数、函数对象、lambda表达式等。
- `arg1, arg2, ...`：要绑定的参数，可以是具体值、占位符 `_1, _2, ...`（表示将在调用时提供的参数）。

### 示例

以下示例展示了如何使用 `std::bind` 绑定参数：

```C++
#include <iostream>
#include <functional>

void print_sum(int a, int b) {
    std::cout << "Sum: " << a + b << std::endl;
}

int main() {
    // 使用std::bind绑定第一个参数
    auto print_sum_with_10 = std::bind(print_sum, 10, std::placeholders::_1);

    // 调用绑定后的函数，只需提供剩余的一个参数
    print_sum_with_10(5);  // 输出: Sum: 15

    return 0;
}
```

### 绑定成员函数

绑定成员函数需要使用对象实例或对象指针：

```C++
#include <iostream>
#include <functional>

class Printer {
public:
    void print(int a, int b) {
        std::cout << "Sum: " << a + b << std::endl;
    }
};

int main() {
    Printer printer;

    // 使用std::bind绑定成员函数
    auto bound_print = std::bind(&Printer::print, &printer, std::placeholders::_1, std::placeholders::_2);

    // 调用绑定后的函数
    bound_print(10, 5);  // 输出: Sum: 15

    return 0;
}
```

### 结合泛型算法使用

`std::bind` 常与泛型算法结合使用，使得算法可以接受自定义的函数行为。例如：

```C++
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>

bool check_size(const std::string& s, std::string::size_type sz) {
    return s.size() >= sz;
}

int main() {
    std::vector<std::string> words = {"apple", "banana", "cherry", "date", "elderberry"};
    std::string::size_type sz = 6;

    // 使用std::bind将参数绑定到函数
    auto check = std::bind(check_size, std::placeholders::_1, sz);

    // 使用find_if和绑定的函数
    auto it = std::find_if(words.begin(), words.end(), check);
    if (it != words.end()) {
        std::cout << "First word with size >= " << sz << " is " << *it << std::endl;
    } else {
        std::cout << "No word found with size >= " << sz << std::endl;
    }

    return 0;
}
```

### 总结

`std::bind` 是一个强大的工具，用于创建部分应用函数和延迟调用。它通过绑定函数的部分参数，生成一个新的可调用对象，使得代码更加简洁和灵活。在泛型编程中，`std::bind` 常用于将自定义操作传递给算法，从而实现复杂的功能。

## 再探迭代器

1. **迭代器类别**：

   - 输入迭代器（Input Iterator）
   - 输出迭代器（Output Iterator）
   - 前向迭代器（Forward Iterator）
   - 双向迭代器（Bidirectional Iterator）
   - 随机访问迭代器（Random Access Iterator）

   每种类别迭代器具有不同的功能和限制，例如随机访问迭代器支持跳跃访问和算术操作，而输入迭代器只支持单向遍历和读取等。

2. **算法与迭代器**：
   C++标准库中的算法（如`std::sort`, `std::find`, `std::transform`等）通常通过迭代器来操作数据。不同类别的迭代器能够配合不同的算法使用，这种灵活性使得算法能够适应各种数据结构和访问方式。

3. **迭代器的自定义**：
   可以通过定义自己的迭代器类型，来适配自定义的数据结构或者提供特定的遍历行为。自定义迭代器需要实现一组迭代器的接口，例如`operator++`, `operator*`等，以便被标准库算法正确使用。

4. **示例代码**：
   下面是一个简单的示例，展示了使用迭代器和算法来处理容器中的数据：

   ```c++
   #include <iostream>
   #include <vector>
   #include <algorithm>
   
   int main() {
       std::vector<int> vec = {3, 1, 4, 1, 5, 9, 2, 6};
   
       // 使用标准库算法和迭代器进行排序
       std::sort(vec.begin(), vec.end());
   
       // 使用for循环和迭代器打印排序后的结果
       for (auto it = vec.begin(); it != vec.end(); ++it) {
           std::cout << *it << " ";
       }
       std::cout << std::endl;
   
       return 0;
   }
   ```

   在这个示例中，`std::sort` 算法使用了双向迭代器，对`vec`进行排序，然后通过`for`循环遍历输出排序后的结果。

## 插入迭代器的使用

插入迭代器有几个常见的变种，主要包括：

1. `std::insert_iterator`

   ：

   - 这是最常用的插入迭代器类型。
   - 需要指定容器和插入位置。

### 创建插入迭代器

使用`std::insert_iterator`需要包含头文件`<iterator>`，其构造函数的格式如下：

```
std::insert_iterator<Container> it(container, position);
```

- `Container` 是容器类型。
- `position` 是插入位置的迭代器。

### 示例代码

以下是一个使用插入迭代器的示例：

```c++
#include <iostream>
#include <vector>
#include <iterator>
#include <algorithm>

int main() {
    std::vector<int> vec1 = {1, 2, 3, 4};
    std::vector<int> vec2 = {5, 6, 7};

    // 使用std::insert_iterator将vec2的内容插入到vec1的末尾
    std::copy(vec2.begin(), vec2.end(), std::back_inserter(vec1));

    // 打印vec1的内容
    std::cout << "vec1 after insertion: ";
    for (int num : vec1) {
        std::cout << num << " ";
    }
    std::cout << std::endl;

    // 使用std::insert_iterator将vec2的内容插入到vec1的指定位置
    std::vector<int> vec3 = {8, 9, 10};
    std::vector<int>::iterator it = vec1.begin() + 2; // 指定插入位置
    std::copy(vec3.begin(), vec3.end(), std::insert_iterator<std::vector<int>>(vec1, it));

    // 打印vec1的内容
    std::cout << "vec1 after insertion at position: ";
    for (int num : vec1) {
        std::cout << num << " ";
    }
    std::cout << std::endl;

    return 0;
}
```

### 解析示例代码

1. **`std::back_inserter`**：一个方便的插入迭代器，用于将元素添加到容器的末尾。它的使用方式如下：

   ```c++
   std::copy(vec2.begin(), vec2.end(), std::back_inserter(vec1));
   ```

2. **`std::insert_iterator`**：用来指定插入位置的迭代器。例如：

   ```c++
   std::copy(vec3.begin(), vec3.end(), std::insert_iterator<std::vector<int>>(vec1, it));
   ```

   其中`it`是指向插入位置的迭代器。

## lamada表达式

Lambda 表达式是 C++11 引入的一种功能，允许你在代码中定义匿名函数。它们通常用于简化代码，使代码更简洁、更易读。Lambda 表达式的基本语法如下：

```c++
[capture](parameters) -> return_type { body }
```

- **`capture`**：捕获列表，用于捕获上下文中的变量。
- **`parameters`**：参数列表，与普通函数的参数列表类似。
- **`return_type`**：返回类型，可以省略，如果可以从返回的表达式中推断出来。
- **`body`**：函数体，包含要执行的代码。

### 捕获列表

捕获列表可以捕获外部作用域中的变量，使它们在 Lambda 表达式内部可用。捕获方式包括：

- **按值捕获**（`[=]`）：捕获外部变量的值。
- **按引用捕获**（`[&]`）：捕获外部变量的引用。
- **显式捕获**：可以选择性地捕获某些变量，如 `[x, &y]` 表示按值捕获 `x`，按引用捕获 `y`。

### 示例

以下是一些使用 Lambda 表达式的示例：

#### 1. 基本用法

```c++
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5};

    // 使用 Lambda 表达式打印每个元素
    std::for_each(numbers.begin(), numbers.end(), [](int n) {
        std::cout << n << " ";
    });
    std::cout << std::endl;

    return 0;
}
```

#### 2. 带捕获的 Lambda 表达式

```c++
#include <iostream>

int main() {
    int factor = 2;
    auto multiply = [factor](int n) -> int {
        return n * factor;
    };

    std::cout << "3 * factor = " << multiply(3) << std::endl;

    return 0;
}
```

#### 3. 修改捕获的变量

按值捕获的变量在 Lambda 表达式内部是不可修改的，但可以通过 `mutable` 关键字允许修改：

```c++
#include <iostream>

int main() {
    int x = 10;

    auto increment = [x]() mutable {
        ++x;
        std::cout << "Inside Lambda: x = " << x << std::endl;
    };

    increment();
    std::cout << "Outside Lambda: x = " << x << std::endl;

    return 0;
}
```

#### 4. 捕获外部变量的引用

通过引用捕获外部变量，可以在 Lambda 表达式内部修改这些变量：

```c++
#include <iostream>

int main() {
    int x = 10;

    auto increment = [&x]() {
        ++x;
        std::cout << "Inside Lambda: x = " << x << std::endl;
    };

    increment();
    std::cout << "Outside Lambda: x = " << x << std::endl;

    return 0;
}
```

#### 5. 复杂示例：排序

使用 Lambda 表达式进行自定义排序：

```c++
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> numbers = {5, 3, 1, 4, 2};

    // 使用 Lambda 表达式按降序排序
    std::sort(numbers.begin(), numbers.end(), [](int a, int b) {
        return a > b;
    });

    for (const auto& n : numbers) {
        std::cout << n << " ";
    }
    std::cout << std::endl;

    return 0;
}
```

### Lambda 表达式的优点

1. **简洁**：可以在需要函数的地方直接定义，无需单独声明和定义。
2. **灵活**：可以捕获外部变量，使得编写处理复杂逻辑的函数变得更容易。
3. **提高代码可读性**：特别是在使用标准库算法时，Lambda 表达式可以使代码更简洁、更易读。

Lambda 表达式在 C++11 引入后，已经成为现代 C++ 编程中的重要工具，广泛应用于标准库算法、自定义操作、事件处理等场景中。



# Chapter 11 关联容器

## 11.1关联容器概述

关联容器根据键来存储元素，并提供快速的查找、插入和删除操作。不同于顺序容器（如 `std::vector`、`std::list`），关联容器是基于树结构实现的，通常使用红黑树。这使得关联容器中的元素总是有序的（按键排序）。

#### 主要关联容器

1. **`std::map`**：一个键值对的集合，每个键是唯一的。
2. **`std::multimap`**：一个键值对的集合，允许多个相同的键。
3. **`std::set`**：一个键的集合，每个键是唯一的。
4. **`std::multiset`**：一个键的集合，允许多个相同的键。

### 示例代码

#### 1. 使用 `std::map`

`std::map` 是一个键值对容器，键是唯一的，按键排序。以下是一个简单的示例，展示如何插入、访问和遍历 `std::map`：

```c++
#include <iostream>
#include <map>
#include <string>

int main() {
    // 创建一个map
    std::map<std::string, int> word_count;

    // 插入元素
    word_count["hello"] = 1;
    word_count["world"] = 2;
    word_count["hello"]++;  // 修改元素

    // 访问元素
    std::cout << "hello: " << word_count["hello"] << std::endl;
    std::cout << "world: " << word_count["world"] << std::endl;

    // 遍历map
    for (const auto& pair : word_count) {
        std::cout << pair.first << ": " << pair.second << std::endl;
    }

    return 0;
}
```

#### 2. 使用 `std::set`

`std::set` 是一个键的集合，键是唯一的，按键排序。以下是一个简单的示例，展示如何插入、访问和遍历 `std::set`：

```c++
#include <iostream>
#include <set>

int main() {
    // 创建一个set
    std::set<int> numbers;

    // 插入元素
    numbers.insert(1);
    numbers.insert(2);
    numbers.insert(3);
    numbers.insert(1);  // 重复元素，不会插入

    // 访问元素
    if (numbers.find(2) != numbers.end()) {
        std::cout << "2 is in the set" << std::endl;
    }

    // 遍历set
    for (const auto& num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;

    return 0;
}
```

#### 3. 使用 `std::multimap`

`std::multimap` 允许多个相同的键。以下是一个示例，展示如何插入、访问和遍历 `std::multimap`：

```c++
#include <iostream>
#include <map>
#include <string>

int main() {
    // 创建一个multimap
    std::multimap<std::string, int> grades;

    // 插入元素
    grades.insert({"Alice", 85});
    grades.insert({"Bob", 90});
    grades.insert({"Alice", 95});  // 允许重复键

    // 访问元素
    auto range = grades.equal_range("Alice");
    for (auto it = range.first; it != range.second; ++it) {
        std::cout << it->first << ": " << it->second << std::endl;
    }

    // 遍历multimap
    for (const auto& pair : grades) {
        std::cout << pair.first << ": " << pair.second << std::endl;
    }

    return 0;
}
```


## map和unordered-map的区别
### 1. `map`

- **底层实现**: `map` 是基于平衡二叉搜索树（通常是红黑树）实现的。
- **元素有序性**: `map` 会根据键值的大小自动对元素进行排序，键的顺序是严格有序的（按照小到大的顺序，或根据自定义的比较函数）。
- **查找、插入、删除的时间复杂度**: 平均情况下为 O(log n)，最坏情况也为 O(log n)。
- **内存占用**: 由于平衡二叉树的结构，`map` 的内存开销相对较大。

### 2. `unordered_map`

- **底层实现**: `unordered_map` 是基于哈希表实现的。
- **元素有序性**: `unordered_map` 中的元素没有固定顺序，键值对的顺序与插入顺序无关。
- **查找、插入、删除的时间复杂度**: 平均情况下为 O(1)，最坏情况下为 O(n)，但最坏情况较少发生（如出现哈希冲突或不良的哈希函数）。
- **内存占用**: `unordered_map` 由于哈希表需要额外的空间来处理冲突，可能会占用更多的内存。

### 选择指南

- 如果需要对键进行排序或者需要按顺序遍历元素，选择 `map`。
- 如果不需要顺序，且对性能要求较高，选择 `unordered_map`。
```C
#include <iostream>
#include <map>
#include <unordered_map>

int main() {
    // map 示例
    std::map<int, std::string> ordered_map;
    ordered_map[2] = "Two";
    ordered_map[1] = "One";
    ordered_map[3] = "Three";

    std::cout << "Ordered map (sorted by keys):" << std::endl;
    for (const auto& pair : ordered_map) {
        std::cout << pair.first << ": " << pair.second << std::endl;
    }

    // unordered_map 示例
    std::unordered_map<int, std::string> unordered_map;
    unordered_map[2] = "Two";
    unordered_map[1] = "One";
    unordered_map[3] = "Three";

    std::cout << "\nUnordered map (no specific order):" << std::endl;
    for (const auto& pair : unordered_map) {
        std::cout << pair.first << ": " << pair.second << std::endl;
    }

    return 0;
}

```
**运行结果** 
```
Ordered map (sorted by keys):
1: One
2: Two
3: Three

Unordered map (no specific order):
2: Two
1: One
3: Three

```


## 11.2 关联容器的操作

### 关联容器的常用操作

1. **插入元素**：`insert` 方法。
2. **访问元素**：`operator[]`（适用于 `std::map`），`find` 方法。
3. **删除元素**：`erase` 方法。
4. **查找元素**：`find` 方法。
5. **遍历元素**：使用迭代器或基于范围的 `for` 循环。

### 关联容器的迭代器

关联容器支持随机访问迭代器，迭代器的操作方式与其他容器类似。常用的迭代器包括：

- **begin()**：返回指向容器第一个元素的迭代器。
- **end()**：返回指向容器尾后元素的迭代器。
- **cbegin()** 和 **cend()**：返回常量迭代器，不能用于修改元素。
- **rbegin()** 和 **rend()**：返回指向容器最后一个元素的反向迭代器。
- **crbegin()** 和 **crend()**：返回常量反向迭代器。

### 添加元素

向关联容器添加元素可以使用以下方法：

- **insert**：在 `map` 和 `set` 中插入元素。
- **emplace**：直接在容器中构造元素，避免不必要的拷贝或移动。

#### 示例代码：

```c++
#include <iostream>
#include <map>
#include <set>

int main() {
    std::map<std::string, int> word_count;
    std::set<int> numbers;

    // 向map插入元素
    word_count.insert({"hello", 1});
    word_count.insert(std::make_pair("world", 2));
    word_count.emplace("cpp", 3);

    // 向set插入元素
    numbers.insert(1);
    numbers.insert(2);
    numbers.emplace(3);

    // 遍历map
    for (const auto& pair : word_count) {
        std::cout << pair.first << ": " << pair.second << std::endl;
    }

    // 遍历set
    for (const auto& num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;

    return 0;
}
```

### 删除元素

从关联容器中删除元素的方法有：

- **erase**：按迭代器、按键值或按键值范围删除元素。
- **clear**：删除容器中的所有元素。

#### 示例代码：

```c++
#include <iostream>
#include <map>
#include <set>

int main() {
    std::map<std::string, int> word_count = {{"hello", 1}, {"world", 2}, {"cpp", 3}};
    std::set<int> numbers = {1, 2, 3};

    // 按键删除元素
    word_count.erase("hello");

    // 按迭代器删除元素
    auto it = numbers.find(2);
    if (it != numbers.end()) {
        numbers.erase(it);
    }

    // 删除范围内的元素
    word_count.erase(word_count.begin(), word_count.end());

    // 清空set
    numbers.clear();

    return 0;
}
```

### `map` 的下标访问

`map` 提供了下标操作符 `operator[]` 来访问和修改元素。如果键不存在，则插入一个新元素并初始化为默认值。

#### 示例代码：

```c
#include <iostream>
#include <map>

int main() {
    std::map<std::string, int> word_count;

    // 使用下标访问元素
    word_count["hello"] = 1;
    word_count["world"] = 2;

    // 修改元素
    word_count["hello"]++;

    // 访问元素
    std::cout << "hello: " << word_count["hello"] << std::endl;
    std::cout << "world: " << word_count["world"] << std::endl;

    // 如果键不存在，插入新元素并初始化为默认值
    std::cout << "cpp: " << word_count["cpp"] << std::endl;  // 输出0

    return 0;
}
```

# Chapter 12  动态内存

## ps：创建相应类的变量

#### 使用默认构造函数

```c++
int main() { 
	A a; // 使用默认构造函数创建对象 
	a.display(); 
	return 0; 
}
```

#### 使用带参数的构造函数

```c++
int main() {
    A a(10);  // 使用带参数的构造函数创建对象
    a.display();
    return 0;
}
```

#### 使用动态分配（指针）

```c++
int main() {
    A* a = new A(20);  // 使用带参数的构造函数动态分配对象
    a->display();
    delete a;  // 别忘了释放内存
    return 0;
}
```



#### 使用标准库的智能指针

```c++
int main() {
    std::unique_ptr<A> a = std::make_unique<A>(30);  // 使用智能指针
    a->display();
    return 0;
}
```

## 12.1动态内存和智能指针

### 智能指针

为了更好地管理动态内存，避免手动释放内存带来的问题，C++11 引入了智能指针。智能指针通过 RAII（Resource Acquisition Is Initialization）机制管理动态内存的生命周期。

#### `std::shared_ptr`

`std::shared_ptr` 是一种智能指针，多个 `shared_ptr` 对象可以共享同一个资源。当最后一个 `shared_ptr` 被销毁时，资源才会被释放。

```c++
#include <memory>
#include <iostream>

int main() {
    std::shared_ptr<int> p1 = std::make_shared<int>(42);  // 使用make_shared创建shared_ptr
    std::shared_ptr<int> p2(p1);  // p1和p2共享同一个int

    std::cout << *p1 << " " << *p2 << std::endl;  // 输出：42 42

    p1.reset();  // p1不再指向int
    std::cout << (p1 ? "p1 is not null" : "p1 is null") << std::endl;  // 输出：p1 is null
    std::cout << *p2 << std::endl;  // 输出：42，p2仍然指向int

    return 0;
}
```

#### `std::unique_ptr`

`std::unique_ptr` 是一种智能指针，独占所指向的资源，不允许其他 `unique_ptr` 共享该资源。

```c++
#include <memory>
#include <iostream>

int main() {
    std::unique_ptr<int> p1 = std::make_unique<int>(42);  // 使用make_unique创建unique_ptr

    std::cout << *p1 << std::endl;  // 输出：42

    // std::unique_ptr<int> p2(p1);  // 错误：unique_ptr不允许复制

    std::unique_ptr<int> p3 = std::move(p1);  // p3接管p1的所有权
    std::cout << *p3 << std::endl;  // 输出：42
    std::cout << (p1 ? "p1 is not null" : "p1 is null") << std::endl;  // 输出：p1 is null

    return 0;
}
```

### `release` 函数

`release` 函数用于将 `unique_ptr` 所管理的指针释放出来，并返回该指针。调用 `release` 后，`unique_ptr` 不再拥有该指针，也不再负责删除它。需要注意的是，释放出来的指针需要由调用者手动管理和删除，以防止内存泄漏。

#### 示例

```c++
#include <memory>
#include <iostream>

int main() {
    std::unique_ptr<int> p1 = std::make_unique<int>(42);

    int* rawPtr = p1.release();  // 释放所有权，p1不再管理这个指针
    std::cout << *rawPtr << std::endl;  // 输出：42

    delete rawPtr;  // 需要手动删除指针，以防内存泄漏

    // std::cout << *p1 << std::endl;  // 错误：p1已经不再管理任何指针

    return 0;
}
```

### `reset` 函数

`reset` 函数用于重置 `unique_ptr`，即释放当前所管理的对象（如果有），并选择性地接管一个新对象。如果没有提供新的指针，则 `unique_ptr` 将被置为空，之前管理的对象会被删除。

#### 示例

```c++
#include <memory>
#include <iostream>

int main() {
    std::unique_ptr<int> p1 = std::make_unique<int>(42);

    p1.reset(new int(55));  // 释放原有对象，并接管新对象
    std::cout << *p1 << std::endl;  // 输出：55

    p1.reset();  // 释放当前对象，p1被置为空
    std::cout << (p1 ? "p1 is not null" : "p1 is null") << std::endl;  // 输出：p1 is null

    return 0;
}
```

### 区别

1. **release**:
   - 返回当前管理的指针，并释放所有权。
   - `unique_ptr` 变为空，不再管理任何对象。
   - 调用者需要手动管理和删除返回的指针。
2. **reset**:
   - 释放当前管理的对象（如果有），并选择性地接管一个新对象。
   - 如果没有提供新指针，则 `unique_ptr` 变为空。
   - 不返回指针，直接管理新的对象或变为空。

#### `std::weak_ptr`

`std::weak_ptr` 是一种不控制资源生命周期的智能指针，它通常与 `std::shared_ptr` 一起使用来解决循环引用问题。

```c++
#include <memory>
#include <iostream>

struct Node {
    std::shared_ptr<Node> next;
    std::weak_ptr<Node> prev;
    int value;
    Node(int val) : value(val) {}
};

int main() {
    auto n1 = std::make_shared<Node>(1);
    auto n2 = std::make_shared<Node>(2);

    n1->next = n2;
    n2->prev = n1;  // 使用weak_ptr避免循环引用

    std::cout << n1->next->value << std::endl;  // 输出：2
    std::cout << (n2->prev.lock() ? "prev is valid" : "prev is expired") << std::endl;  // 输出：prev is valid

    return 0;
}
```


### `decltype` 基本用法

`decltype` 用于获取一个表达式的类型，而不计算该表达式。它可以用于声明变量或作为模板参数。

#### 示例

```c++
int i = 42;
decltype(i) j = i;  // j的类型是int

const int& k = i;
decltype(k) l = i;  // l的类型是const int&

auto f = [](int a, int b) -> int { return a + b; };
decltype(f) g = f;  // g的类型是lambda的类型

std::cout << typeid(j).name() << std::endl;  // 输出：int
std::cout << typeid(l).name() << std::endl;  // 输出：const int&
std::cout << typeid(g).name() << std::endl;  // 输出：lambda类型
```

### `decltype` 推导规则

1. **简单变量**: `decltype(x)` 的类型和 `x` 相同。

```c++
int a = 0;
decltype(a) b;  // b的类型是int
```

1. **表达式**: `decltype` 的类型与表达式的类型一致，包括引用性和常量性。

```c++
int x = 42;
int& ref = x;
decltype(ref) y = x;  // y的类型是int&，即引用
```

1. **函数调用**: `decltype` 可以用于推导函数返回值的类型。

```c++
int func();
decltype(func()) result;  // result的类型是int
```

### `decltype` 用于泛型编程

在模板编程中，`decltype` 可以用于推导复杂表达式的类型，确保模板函数能够正确处理各种类型的参数。

#### 示例

```c++
template<typename T1, typename T2>
auto add(T1 a, T2 b) -> decltype(a + b) {
    return a + b;
}

int main() {
    int a = 1;
    double b = 2.0;
    auto result = add(a, b);  // result的类型是double，因为a + b的类型是double

    std::cout << typeid(result).name() << std::endl;  // 输出：double
    return 0;
}
```

### `decltype(auto)`

C++14 引入了 `decltype(auto)`，它结合了 `auto` 和 `decltype` 的优点，用于在返回类型或变量类型推导时更加灵活。

#### 示例

```c++
int func(int& a) { return a; }

int main() {
    int x = 42;
    decltype(auto) y = (x);  // y的类型是int&，因为(x)是一个左值引用
    decltype(auto) z = x;    // z的类型是int

    std::cout << typeid(y).name() << std::endl;  // 输出：int&
    std::cout << typeid(z).name() << std::endl;  // 输出：int
    return 0;
}
```

### 使用 `explicit` 的场景

#### 1. **防止隐式转换**

当一个构造函数只有一个参数时，编译器可以使用它来进行隐式类型转换。这样可能会导致一些意外的行为。例如：

```c++
class MyClass {
public:
    MyClass(int x) {}  // 普通构造函数
};

void doSomething(MyClass obj) {
    // 函数体
}

int main() {
    doSomething(42);  // 隐式地将 int 转换为 MyClass 对象
    return 0;
}
```

在上面的代码中，`doSomething(42)` 会隐式地将 `42` 转换为 `MyClass` 对象。这种隐式转换有时会导致不可预料的结果。

通过在构造函数前加上 `explicit` 关键字，可以阻止这种隐式转换：

```c++
class MyClass {
public:
    explicit MyClass(int x) {}  // 显式构造函数
};

int main() {
    // doSomething(42);  // 编译器会报错，无法隐式转换
    MyClass obj(42);      // 需要显式地构造 MyClass 对象
    doSomething(obj);
    return 0;
}
```

在使用 `explicit` 关键字后，`doSomething(42)` 这一行会产生编译错误，强制程序员明确地创建 `MyClass` 对象。

#### 2. **防止不必要的类型转换**

`explicit` 还可以用于类型转换运算符，以防止不必要或不希望的类型转换。例如：

```c++
class MyClass {
public:
    explicit operator bool() const {
        return true;  // 假设返回一个布尔值
    }
};

int main() {
    MyClass obj;
    if (obj) {  // 需要显式转换为 bool
        // 代码块
    }

    // bool flag = obj;  // 这行代码将会编译失败
    return 0;
}
```

在这种情况下，`explicit` 关键字阻止了 `MyClass` 对象隐式地转换为布尔值，避免了可能的意外行为。

# Chapter 13 拷贝控制

## 13.1 拷贝，赋值与销毁

### 13.1.1 拷贝构造函数

**拷贝构造函数**是在用一个对象来初始化另一个对象时调用的，通常有以下几种情况：

- 使用一个对象来初始化另一个对象：

```c
    MyClass obj1; MyClass obj2 = obj1;  // 调用了拷贝构造函数
```

- 将对象作为参数传递给函数（按值传递）。

- 从函数按值返回对象。

  默认情况下，编译器会为类**自动**生成一个**拷贝构造函数**，该函数对类的每个非静态数据成员执行逐个拷贝（shallow copy）。然而，如果类中包含指针成员，或者需要深拷贝（deep copy），则通常需要手动定义拷贝构造函数。

### 13.1.3 析构函数

**析构函数**是在对象的生命周期结束时自动调用的，用于清理对象持有的资源（如内存、文件句柄等）。析构函数没有返回值，也不接受参数，通常形式如下：

```c
~MyClass() { 
	// 清理操作 
}
```

**什么时候回调用析构函数**
无论何时一个对象被销毁，就会自动调用其析构函数：

- 变量离开其作用域
- 一个对象被销毁时，其成员被销毁
- 容器被销毁时，起源说被销毁
- 对于动态分配的对象，当指向它的指针应用delete运算符时被销毁
- 对于临时对象，当创建它的完整表达式结束时被销毁

### 13.1.4 三/五法则

### 三法则（Rule of Three）

**三法则**指出，如果一个类需要自定义以下任意一个函数，通常也需要自定义另外两个：

1. **拷贝构造函数** (`Copy Constructor`)：
   - 用于创建一个对象的副本，如`MyClass obj2 = obj1;`。
2. **拷贝赋值运算符** (`Copy Assignment Operator`)：
   - 用于将一个对象赋值给另一个已存在的对象，如`obj2 = obj1;`。
3. **析构函数** (`Destructor`)：
   - 用于清理对象在其生命周期内分配的资源。

这些函数通常一起实现是因为它们都涉及到类的资源管理。例如，如果类涉及到动态内存分配，在一个函数中没有适当地管理内存会导致资源泄漏、双重释放等问题。通过遵循三法则，可以确保对象的安全使用。

### 五法则（Rule of Five）

**五法则**是三法则的扩展，考虑了C++11及更高版本中的移动语义。除了三法则中的三个函数外，还增加了两个与移动操作相关的函数：

1. **移动构造函数** (`Move Constructor`)：
   - 用于将资源从一个临时对象（右值）“移动”到另一个对象，而不进行拷贝操作。例如：`MyClass obj3 = std::move(obj1);`
2. **移动赋值运算符** (`Move Assignment Operator`)：
   - 用于将一个临时对象（右值）赋值给另一个已存在的对象，并“移动”资源，如`obj3 = std::move(obj1);`。

这两个移动操作通常用于优化性能，避免不必要的拷贝，特别是在涉及大型数据结构或资源时。遵循五法则可以确保类在拷贝和移动操作中都能正确处理资源。

## 13.2 拷贝控制和资源管理

### 行为像值的类（Value-like Class）

**定义**：行为像值的类是指其对象在拷贝、赋值时表现得像内置的值类型（如`int`、`double`等），即拷贝和赋值操作会产生该对象的独立副本。

#### 特点

- **深拷贝**：对象的拷贝构造函数和拷贝赋值运算符通常执行深拷贝，这意味着对象的所有资源（如动态分配的内存）都会被复制。
- **独立性**：每个对象都维护自己的资源副本，彼此独立。对一个对象的修改不会影响其他对象。
- **安全性**：因为对象之间没有共享资源，避免了资源的重复释放和不必要的依赖。

#### 适用场景

- 当对象需要独立管理其资源时（如动态内存、文件句柄等）。
- 需要避免多个对象共享资源，导致复杂的内存管理问题。

#### 示例

```c++
class ValueLike {
public:
    ValueLike(const char* s) {
        data = new char[strlen(s) + 1];
        strcpy(data, s);
    }

    // 拷贝构造函数实现深拷贝
    ValueLike(const ValueLike& other) {
        data = new char[strlen(other.data) + 1];
        strcpy(data, other.data);
    }

    // 拷贝赋值运算符实现深拷贝
    ValueLike& operator=(const ValueLike& other) {
        if (this != &other) {
            delete[] data;
            data = new char[strlen(other.data) + 1];
            strcpy(data, other.data);
        }
        return *this;
    }

    ~ValueLike() {
        delete[] data;
    }

private:
    char* data;
};
```

在这个例子中，每个`ValueLike`对象在拷贝或赋值时都会产生一个独立的副本。

### 行为像指针的类（Pointer-like Class）

**定义**：行为像指针的类是指其对象在拷贝和赋值时表现得像指针，即拷贝和赋值操作会共享相同的资源，而不是创建独立的副本。

#### 特点

- **浅拷贝**：对象的拷贝构造函数和拷贝赋值运算符执行浅拷贝，意味着多个对象共享同一份资源。
- **共享资源**：对象之间共享相同的资源，修改一个对象的资源可能会影响其他对象。
- **高效性**：由于不需要进行深拷贝，通常效率更高，但需要小心资源管理，避免双重释放等问题。

#### 适用场景

- 当多个对象需要共享相同的资源时。
- 需要减少资源消耗或提升性能的场景（如共享内存、大数据结构等）。

#### 示例

```c++
class PointerLike {
public:
    PointerLike(int* p) : ptr(p) {}

    // 拷贝构造函数实现浅拷贝
    PointerLike(const PointerLike& other) : ptr(other.ptr) {}

    // 拷贝赋值运算符实现浅拷贝
    PointerLike& operator=(const PointerLike& other) {
        if (this != &other) {
            ptr = other.ptr;
        }
        return *this;
    }

    // 由于资源由外部管理，不在析构函数中释放内存
    ~PointerLike() = default;

private:
    int* ptr;  // 共享的资源
};
```

在这个例子中，多个`PointerLike`对象可以共享同一份资源（即同一个`int`指针）。

## 13.6 移动构造

### 13.6.1 右值和左值

**右值是易消失，左值是长久的**

在C++中，**左值（lvalue）** 是指那些可以取地址的表达式，即持久存在的内存位置，如变量名。而 **右值（rvalue）** 则是不能取地址的临时对象或表达式的值。

- **左值**：`int x = 5; x` 是左值，因为 `x` 是一个变量，可以取地址。
- **右值**：`5` 是右值，它是一个字面量，没有持久的存储。

#### 移动构造与移动赋值

在经典的拷贝语义中，对象的复制通常涉及深拷贝操作，这可能非常耗时。移动语义引入了 **移动构造函数** 和 **移动赋值运算符**，允许对象的资源从一个临时对象中“移动”到另一个对象，而不是进行深拷贝。

#### 移动构造函数

移动构造函数通过“窃取”源对象的资源，使得新对象可以直接使用这些资源，而不需要重新分配内存。这大大提高了性能。

```c++
#include <utility> // for std::move
#include <string>

class HasPtr {
public:
    // 移动构造函数
    HasPtr(HasPtr &&p) noexcept : ps(p.ps), i(p.i) {
        p.ps = nullptr; // 使源对象不再指向资源
    }

    // 移动赋值运算符
    HasPtr& operator=(HasPtr &&rhs) noexcept {
        if (this != &rhs) {
            delete ps;  // 释放当前对象的资源
            ps = rhs.ps; // 窃取资源
            i = rhs.i;
            rhs.ps = nullptr; // 使源对象不再指向资源
        }
        return *this;
    }

    // 析构函数
    ~HasPtr() { delete ps; }

    // 构造函数与其他成员函数（略）
    HasPtr(const std::string &s = std::string())
        : ps(new std::string(s)), i(0) {}

private:
    std::string *ps;
    int i;
};
```

#### 代码解释

1. **移动构造函数**：
   - `HasPtr(HasPtr &&p) noexcept`：这是移动构造函数，它接受一个右值引用（`HasPtr&&`）。通过`std::move`，可以将一个左值强制转换为右值引用，从而调用移动构造函数。构造函数中，`ps`指针直接接管了右值对象的内存，而不是拷贝其内容。然后，将右值对象的指针设为`nullptr`，表示它不再管理该内存。
2. **移动赋值运算符**：
   - `HasPtr& operator=(HasPtr &&rhs) noexcept`：这是移动赋值运算符。它首先检查自赋值情况，然后释放当前对象的资源，再将右值对象的资源接管过来，最后将右值对象的指针设为`nullptr`。
3. **析构函数**：
   - 析构函数确保资源在对象销毁时被释放。

#### 移动语义的优势

- **性能提升**：移动操作通常比拷贝操作更高效，尤其是当对象持有动态分配的资源（如内存、文件句柄等）时。移动操作避免了不必要的资源分配和数据复制。
- **安全性**：通过将右值对象的资源转移到新对象，并将右值对象置为无效状态（例如将指针设为`nullptr`），可以安全地避免资源被多次释放的风险。

#### 右值引用与`std::move`

- **右值引用（rvalue reference）**：使用`&&`表示，右值引用允许我们捕获将要销毁的临时对象，从而可以安全高效地移动其资源。
- **`std::move`**：这是一个标准库函数，作用是将一个左值转换为右值引用，从而允许移动语义生效。



在C++中，`Move(Move&& move)` 是 **右值引用（rvalue reference）**，而使用右值引用是为了支持 **移动语义**，使得资源可以从一个对象“移动”到另一个对象，而不是复制。

### 为什么使用右值引用（&&）

#### 1. **右值与左值的区别**

- **左值（lvalue）** 是指那些可以取地址的对象，它们通常在内存中有一个固定位置，如变量。
- **右值（rvalue）** 是那些不能取地址的临时对象或表达式的结果，它们通常是短暂存在的，不再需要使用。

在以下示例中：

```c++
Move(10);  // 这是一个右值
```

`Move(10)` 创建了一个临时的 `Move` 对象，这是一个右值，它没有持久的位置。C++ 引入了右值引用来捕获这样的临时对象。

#### 2. **移动构造函数的作用**

移动构造函数的作用是接管右值对象的资源，而不是进行深拷贝，从而提高程序效率。

```c++
Move(Move&& move) noexcept {
    cout << "in move construct" << endl;
    this->a_ = move.a_; // 移动资源
}
```

- `Move&& move`：这里的 `&&` 表示右值引用。这个构造函数仅会在处理右值对象时被调用。
- **为什么是右值引用**：使用右值引用意味着这个函数可以接收右值对象，如 `Move(10)`，因为我们知道这个对象是临时的，可以放心地“窃取”它的资源，而不需要担心破坏其内容。

#### 总结

- **右值引用（&&）** 是专门用于捕获和操作那些临时对象（右值）的，它们通常即将被销毁。
- 使用 `Move(Move&& move)` 可以避免不必要的资源分配和数据复制，提高程序的运行效率。

如果不使用 `&&`，而是使用 `Move(Move& move)`，则这个构造函数只能处理左值，不能利用移动语义来优化性能。在右值引用的情况下，编译器会选择调用移动构造函数，而不是复制构造函数。

# Chapter14 重载

## 14.1 基本概念

运算符重载是C++中一个强大的特性，它允许程序员定义或修改C++中的运算符（如 `+`, `-`, `*`, `==` 等）以处理自定义的类型（例如类和结构体）。运算符重载使得用户定义的类型可以像内置类型一样方便地使用。

#### 主要内容包括：

1. **运算符函数**：
   - 运算符重载是通过定义一个特殊的函数来实现的，这个函数称为“运算符函数”。
   - 运算符函数的名字是 `operator` 后跟运算符符号，例如 `operator+`、`operator==`。
2. **重载的运算符的规则**：
   - 重载运算符必须是成员函数或友元函数。
   - 重载运算符的操作数必须包括至少一个用户定义的类型（如类的对象）。
   - 不能改变运算符的优先级、结合性或默认的操作数数量。
   - 不能重载内置类型的运算符。
3. **友元函数与成员函数**：
   - 对于二元运算符（如 `+`，`-`），可以选择将它们重载为友元函数或成员函数。
   - 如果运算符函数是成员函数，它的左操作数绑定到 `this` 指针，右操作数作为参数传递。
   - 如果运算符函数是友元函数，两个操作数都作为参数传递。
4. **输入/输出运算符的重载**：
   - 常见的运算符重载是输入（`>>`）和输出（`<<`）运算符，用于自定义类的输入和输出格式。
   - 这些通常实现为友元函数，以便直接访问类的私有成员。

### 示例代码：

```c++
#include <iostream>

class Complex {
public:
    Complex(double r = 0, double i = 0) : re(r), im(i) {}
    
    // 重载 + 运算符作为成员函数
    Complex operator+(const Complex& rhs) const {
        return Complex(re + rhs.re, im + rhs.im);
    }

    // 重载 << 运算符作为友元函数
    friend std::ostream& operator<<(std::ostream& os, const Complex& c) {
        os << "(" << c.re << ", " << c.im << ")";
        return os;
    }

private:
    double re, im;
};

int main() {
    Complex c1(1.0, 2.0);
    Complex c2(2.0, 3.0);
    Complex c3 = c1 + c2;

    std::cout << "c1 + c2 = " << c3 << std::endl;

    return 0;
}
```

在这个例子中，`Complex` 类重载了 `+` 运算符和 `<<` 运算符，允许我们自然地将两个复数相加，并输出它们的和。
## 14.2重载<< 和 >> 运算符
这一部分在之前写图书管理系统有用过，但是当时还是一知半解，借助chatgpt才完成的。C++标准库提供了 `<<` 和 `>>` 运算符用于输出和输入操作，这些运算符的默认行为只适用于标准数据类型，例如整数、浮点数、字符串等。但对于自定义的类或结构体，我们可以通过重载这两个运算符来实现输入输出的功能。
***重载输出运算符***
```c
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
```C
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
## 14.3 重载 = 和 != 
```c
class A{  
    friend ostream& operator<<(ostream&,const A&);  
private:  
    int b_;  
public:  
    int a_;  
    A() : a_(0) {}  
    A(int a) : a_(a) {}  
  
    bool operator==(A& a){  
        cout << "in compartor == " << endl;  
        return this->a_ == a.a_;  
    }  
  
    //在类内会有一个隐式的绑定，lhs就是this的A  
    A& operator+ (const A& a){  
        cout << "in operator + " << a.a_ << endl;  
        this->a_ += a.a_;  
        return *this;  
    }  
};  
  
int main(int argc,char** argv) {  
    std::cout << "Hello, World!" << std::endl;  
    A a1(1);  
    A a2(1);  
  
    cout <<(a1 == a2) << endl;  
  
    return 0;  
}

```

## 14.4重载赋值运算符
	 注意，重载赋值运算符必须是 成员函数
```C
#include <iostream>
#include <cstring>

class MyString {
public:
    MyString(const char* str = nullptr);   // 构造函数
    MyString(const MyString& other);       // 拷贝构造函数
    MyString& operator=(const MyString& other);  // 赋值运算符
    ~MyString();                           // 析构函数

    void print() const { std::cout << m_data << std::endl; }

private:
    char* m_data;
};

// 构造函数
MyString::MyString(const char* str) {
    if (str) {
        m_data = new char[strlen(str) + 1];
        strcpy(m_data, str);
    } else {
        m_data = new char[1];
        *m_data = '\0';
    }
}

// 拷贝构造函数
MyString::MyString(const MyString& other) {
    m_data = new char[strlen(other.m_data) + 1];
    strcpy(m_data, other.m_data);
}

// 赋值运算符重载
MyString& MyString::operator=(const MyString& other) {
    if (this == &other) {
        return *this;  // 自赋值检测
    }

    delete[] m_data;  // 释放原有资源

    m_data = new char[strlen(other.m_data) + 1];
    strcpy(m_data, other.m_data);

    return *this;  // 返回当前对象的引用
}

// 析构函数
MyString::~MyString() {
    delete[] m_data;
}

int main() {
    MyString str1("Hello");
    MyString str2("World");

    str1 = str2;  // 调用赋值运算符
    str1.print(); // 输出: World

    str1 = str1;  // 自赋值
    str1.print(); // 输出: World

    return 0;
}

```
## 14.5 下标Subscript [ ]
**下标运算符**（`[]`）通常用于访问数组或类中的元素。你可以重载下标运算符，以便允许类像数组一样使用下标来访问内部元素。这在自定义容器类（如向量、矩阵等）中尤其有用。
		在定义 [] 时，通常需要定义两个版本，一个是非常量版本呢，一个是常量版本
```C
#include <iostream>
#include <stdexcept>

class MyArray {
public:
    MyArray(int size);
    ~MyArray();

    int& operator[](int index);              // 非常量版本
    const int& operator[](int index) const;  // 常量版本

    int size() const { return m_size; }

private:
    int* m_data;
    int m_size;
};

// 构造函数
MyArray::MyArray(int size) : m_size(size), m_data(new int[size]) {}

// 析构函数
MyArray::~MyArray() {
    delete[] m_data;
}

// 非常量版本：允许修改元素
int& MyArray::operator[](int index) {
    if (index < 0 || index >= m_size) {
        throw std::out_of_range("Index out of range");
    }
    return m_data[index];
}

// 常量版本：不允许修改元素
const int& MyArray::operator[](int index) const {
    if (index < 0 || index >= m_size) {
        throw std::out_of_range("Index out of range");
    }
    return m_data[index];
}

int main() {
    MyArray arr(10);

    for (int i = 0; i < arr.size(); ++i) {
        arr[i] = i * 10;  // 使用非常量版本的下标运算符
    }

    for (int i = 0; i < arr.size(); ++i) {
        std::cout << arr[i] << " ";  // 使用常量版本的下标运算符
    }
    std::cout << std::endl;

    return 0;
}

```
1. **类的设计**：
   
    - `MyArray` 是一个简单的动态数组类，包含一个指向动态分配内存的指针 `m_data` 和数组大小 `m_size`。
2. **构造函数**：
   
    - `MyArray(int size)` 用于分配指定大小的动态数组。
3. **析构函数**：
   
    - `~MyArray()` 释放动态分配的内存，防止内存泄漏。
4. **重载下标运算符**：
   
    - **非常量版本 (`int& operator[](int index)`)**：返回数组中指定索引的引用，可以修改元素值。对于不合法的索引，抛出 `std::out_of_range` 异常。
    - **常量版本 (`const int& operator[](int index) const`)**：也返回指定索引的引用，但该引用是常量，不允许修改元素值。常量对象调用此版本。
5. **使用下标运算符**：
   
    - 在 `main` 函数中，使用非常量版本的下标运算符为数组元素赋值。
    - 然后，使用常量版本的下标运算符打印数组元素的值。

## 14.5前置++和后置++
在重载的时候我们如何才能区分->在后置++里的参数加入(int)

```C
#include <iostream>
using namespace std;

class Point {
public:
    int x_;
    int y_;

    Point(int x, int y) : x_(x), y_(y) {}

    // 前置递增运算符
    Point& operator++() {
        x_ += 1;
        y_ += 1;
        cout << "prefix current sum is  " << x_ << " " << y_ << " " << endl;
        return *this;
    }

    // 后置递增运算符
    Point operator++(int) {
        Point temp = *this; // 保存当前状态
        x_ += 1;
        y_ += 1;
        cout << "postfix current sum is  " << x_ << " " << y_ << " " << endl;
        return temp; // 返回旧的状态
    }
};

int main() {
    Point p(1, 2);

    ++p;   // 前置递增
    p++;   // 后置递增

    return 0;
}

```

## 14.8 重载 ()  函数调用运算符
### 14.8.1 lambda是函数对象
当你定义一个 Lambda 表达式时，编译器实际上会生成一个匿名的类，这个类重载了 `operator()` 运算符，因此它是一个函数对象。例如，上面 `sum` 的 Lambda 表达式大致等效于：
```c
struct SumLambda {
    int operator()(int a, int b) const {
        return a + b;
    }
};

int main() {
    SumLambda sum;
    cout << "Sum: " << sum(3, 4) << endl;

    return 0;
}

```

# Chapter15面向对象

## 15.3&4 抽象类和虚函数

### 抽象类的特点：

1. **纯虚函数（Pure Virtual Function）：**
   
    - 纯虚函数是一个在基类中声明但没有定义的函数，它使用 `= 0` 来表示。例如：
      
        `virtual void display() = 0;`
        
    - 任何包含至少一个纯虚函数的类就是一个抽象类。
2. **不能实例化：**
   
    - 由于抽象类包含未实现的纯虚函数，不能直接创建抽象类的对象。例如，假设 `Base` 是一个抽象类：
      
        `Base obj; // 错误：不能实例化抽象类`
    
3. **用于接口设计：**
   
    - 抽象类常用于设计接口，让子类实现具体功能。它提供了一种在基类中定义通用行为并强制子类实现特定功能的方式。
4. **子类实现：**
   
    - 继承抽象类的子类必须实现所有的纯虚函数，否则该子类也将成为一个抽象类。

## 15.5类的权限控制
### 1. **public（公共成员）**

- **描述**：使用 `public` 关键字声明的成员可以被任何地方访问。
- **应用场景**：通常用于定义类的接口（即希望外部代码可以直接访问和调用的成员）。

### 2. **protected（受保护成员）**

- **描述**：使用 `protected` 关键字声明的成员只能在类内部及其派生类中访问，而不能在类外部访问。
- **应用场景**：通常用于基类和派生类之间共享数据，但不希望这些数据暴露给类外部的任何部分。
```C
- class Base {
protected:
    int y;           // 受保护成员
};

class Derived : public Base {
public:
    void accessY() {
        y = 10;      // 可以在派生类中访问
    }
};

int main() {
    Derived obj;
    obj.accessY();   // 间接访问受保护成员
    // obj.y = 20;   // 错误：在类外部无法访问
}

```

### 3. **private（私有成员）**

- **描述**：使用 `private` 关键字声明的成员只能在类的内部访问，类的外部和派生类都不能直接访问。
- **应用场景**：用于隐藏类的实现细节，仅通过公共接口与外部交互。
```C
- class Example {
private:
    int z;           // 私有成员

public:
    void setZ(int value) {
        z = value;   // 公共接口来修改私有成员
    }

    int getZ() const {
        return z;    // 公共接口来访问私有成员
    }
};

int main() {
    Example obj;
    obj.setZ(15);    // 通过公共接口访问私有成员
    int value = obj.getZ(); // 通过公共接口获取私有成员值
}

```

### **类的权限控制在派生类中的表现：**

- 当一个类继承自另一个类时，基类中的访问控制符如何影响派生类访问基类成员是一个需要注意的地方。
- 当继承方式为 `public` 时：
    - 基类的 `public` 成员在派生类中仍然是 `public`。
    - 基类的 `protected` 成员在派生类中仍然是 `protected`。
    - 基类的 `private` 成员不能在派生类中访问。
- 当继承方式为 `protected` 时：
    - 基类的 `public` 成员在派生类中变为 `protected`。
    - 基类的 `protected` 成员在派生类中仍然是 `protected`。
    - 基类的 `private` 成员不能在派生类中访问。
- 当继承方式为 `private` 时：
    - 基类的 `public` 和 `protected` 成员在派生类中都变为 `private`。
    - 基类的 `private` 成员不能在派生类中访问。

### **struct 和 class 的区别：**
### 1. **默认访问控制：**

- **`class`**：默认的成员和基类继承权限是 `private`。
- **`struct`**：默认的成员和基类继承权限是 `public`。
```C
- class MyClass {
    int x;  // 默认为 private
};

struct MyStruct {
    int x;  // 默认为 public
};

int main() {
    MyClass obj1;
    // obj1.x = 10;  // 错误，x 是 private 的

    MyStruct obj2;
    obj2.x = 10;  // 正确，x 是 public 的

    return 0;
}

```

### 2. **继承默认权限：**

- **`class`**：继承默认是 `private`。
- **`struct`**：继承默认是 `public`。
```C
- class BaseClass { };

class DerivedClass : BaseClass { };  // 默认 private 继承

struct BaseStruct { };

struct DerivedStruct : BaseStruct { };  // 默认 public 继承

```

### 3. **其他方面的相似性：**

- **构造函数、析构函数、成员函数**：`class` 和 `struct` 都可以定义构造函数、析构函数和成员函数。
- **继承、多态性、模板**：两者都可以用于继承、实现多态性、以及用于模板编程。
- **成员变量**：无论是 `class` 还是 `struct`，都可以包含成员变量，并且成员变量的行为是一致的。
- **操作符重载**：两者都可以重载操作符。

### 4. **语义上使用上的习惯：**

在现代C++编程中，通常有以下习惯：

- **`struct`**：一般用于定义具有简单数据结构的数据类型，所有成员默认 `public`。常用于表示类似 C 语言中的数据结构，主要用于存储数据而不涉及复杂的行为或封装。
- **`class`**：通常用于定义具有更复杂行为、封装数据和逻辑的对象，主要用于面向对象编程，强调封装性、继承性、多态性等。

# Chapter16 模板
## 1. 模板初识

模板是C++中实现泛型编程的重要工具。它允许我们编写与类型无关的代码，避免代码的重复。模板可以用于函数和类，使得它们能够操作不同的数据类型。

### 1.1 函数模板

函数模板是模板在函数中的应用，通过使用模板参数，函数可以在编译时根据传入的实际参数类型生成相应的代码。例如：

```c++
template<typename T>
T myMax(T a, T b) {
    return (a > b) ? a : b;
}
```

这里 `myMax` 是一个函数模板，它可以处理任意类型 `T` 的数据（只要类型 `T` 支持比较操作）。

使用示例：

```c++
int x = 10, y = 20;
std::cout << myMax(x, y) << std::endl;  // 输出20

double a = 10.5, b = 9.5;
std::cout << myMax(a, b) << std::endl;  // 输出10.5
```

### 1.2 类模板

类模板是模板在类中的应用，允许类能够适用于不同的数据类型。例如：

```c++
template<typename T>
class MyPair {
public:
    MyPair(T first, T second) : first(first), second(second) {}
    T getFirst() const { return first; }
    T getSecond() const { return second; }

private:
    T first, second;
};
```

这个 `MyPair` 类模板能够生成任意类型 `T` 的成对对象。

使用示例：

```c++
MyPair<int> intPair(1, 2);
std::cout << intPair.getFirst() << ", " << intPair.getSecond() << std::endl;  // 输出1, 2

MyPair<std::string> strPair("hello", "world");
std::cout << strPair.getFirst() << ", " << strPair.getSecond() << std::endl;  // 输出hello, world
```

### 1.3 模板参数

模板参数是模板的核心，它可以是类型参数（`typename` 或 `class`），也可以是非类型参数（例如整数、指针等）。

####  类型模板参数

类型模板参数用于指定模板所操作的数据类型。通常使用 `typename` 或 `class` 关键字来定义。

```c++
template<typename T>
T add(T a, T b) {
    return a + b;
}
```

在这个例子中，`T` 是一个类型模板参数，代表任意类型。

#### 非类型模板参数

非类型模板参数用于指定模板中的常量值或对象。例如：

```c++
template<int N>
int arraySize(int (&arr)[N]) {
    return N;
}
```

这里的 `N` 是一个非类型模板参数，它表示数组的大小。

使用示例：

```c++
int arr[10];
std::cout << arraySize(arr) << std::endl;  // 输出10
```

### 1.4 模板类嵌套
#### 1. 模板类嵌套的基本概念

在模板类中嵌套其他模板类时，外层模板类的模板参数可以传递给内层的模板类，从而实现更强的泛型能力。通常，模板类嵌套用于实现多级结构，例如树、图等数据结构。
```C++
#include <iostream>
#include <string>

// 外层模板类
template<typename T>
class Outer {
public:
    T value;

    Outer(T val) : value(val) {}

    // 定义一个嵌套的模板类
    template<typename U>
    class Inner {
    public:
        U innerValue;

        Inner(U val) : innerValue(val) {}

        void show() {
            std::cout << "Outer value: " << value << ", Inner value: " << innerValue << std::endl;
        }
    };
};

int main() {
    // 创建Outer类的实例
    Outer<int> outer(10);

    // 创建Inner类的实例，使用Outer的模板参数和独立的模板参数
    Outer<int>::Inner<std::string> inner("Hello");

    // 显示值
    inner.show();

    return 0;
}

```



### 1.5控制实例化的原因

1. **减少代码膨胀**：模板会在每次使用不同类型时生成新的实例化代码，这可能导致生成的可执行文件变大。通过控制实例化，我们可以限制实例化的数量。
2. **提高编译效率**：模板的实例化过程会增加编译时间，尤其是在大型项目中，减少不必要的实例化有助于加快编译速度。
3. **控制模板实例化的逻辑**：在某些情况下，我们希望在编译时确保模板的某些特定实例化存在，或者限制某些实例化的使用。

#### 显式实例化

C++ 允许程序员显式实例化模板，这意味着你可以在特定点强制编译器生成模板的实例，而不是依赖隐式实例化。显式实例化有两种形式：

1. **显式实例化定义（Explicit Instantiation Definition）**：

   - 使用 `template` 关键字并提供模板参数，这会导致编译器生成模板的具体代码。

   ```c
   template class MyClass<int>; // 生成 MyClass<int> 的代码
   ```

2. **显式实例化声明（Explicit Instantiation Declaration）**：

   - 使用 `extern template` 关键字，告诉编译器在当前翻译单元（通常是一个 `.cpp` 文件）中不要生成模板实例化代码，而是期望这些代码在其他地方已经生成。这种机制可以避免在多个文件中重复生成模板实例化代码。

   ```c
   extern template class MyClass<double>; // 声明 MyClass<double> 会在其他地方实例化
   ```

#### 示例：显式实例化

以下是一个简单示例，演示如何使用显式实例化来控制模板的实例化：

```c
#include <iostream>

// 一个简单的模板类
template<typename T>
class MyClass {
public:
    void display() {
        std::cout << "Type: " << typeid(T).name() << std::endl;
    }
};

// 显式实例化 MyClass<int> 的定义
template class MyClass<int>;

// 显式实例化 MyClass<double> 的声明（假设它在其他文件中有定义）
extern template class MyClass<double>;

int main() {
    MyClass<int> obj1; // 使用显式实例化的 MyClass<int>
    obj1.display();

    // MyClass<double> obj2; // 如果尝试使用，可能会导致链接错误

    return 0;
}
```

#### 显式实例化的好处

- **避免重复实例化**：在头文件中使用模板时，如果多个源文件都包含该头文件，那么每个源文件都会实例化模板。使用显式实例化声明可以避免这种情况。
- **提高编译速度**：减少重复实例化可以显著减少大型项目的编译时间。
- **减少可执行文件大小**：通过控制模板实例化的数量，可以减少生成的机器代码，从而减小可执行文件的大小。

#### 显式实例化的使用注意事项

1. **仅在必要时使用**：显式实例化通常用在需要精细控制模板实例化的高级场景中，例如库开发或需要优化编译和链接时间的大型项目中。
2. **确保实例化的唯一性**：在一个项目中，显式实例化的定义应该仅出现一次，以避免重复定义导致的链接错误。
3. **与 `extern` 关键字配合使用**：显式实例化声明和定义可以配合使用来优化模板实例化。


# 结尾

到这里，这本书也算是把基础部分学完了，这2个月来的学习，算是够慢的了（之前学长说的是建议2周完成，但我感觉那也太快了吧）。里面很多知识点虽然都有所了解，但是在应用方面还说有所欠缺，这几天做几个项目来趁热打铁。
昨日和宿舍的学长聊了一下，他说感觉我没有从事计算机行业的样子，看我平日社交啥的都挺好的，活泼，虽然听到有一些打击，但是感觉也没啥事儿，毕竟当程序员的人不一定非得是死气沉沉的，不擅长社交的，这样还能说我的一个优点吧。虽然我现在还是不太清楚将来要干些什么，但是这样一步一地走下去，不断的打磨自己，希望自己一定能成功的。