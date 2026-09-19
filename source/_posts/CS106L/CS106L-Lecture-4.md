---
title: CS106L:Lecture 4
date: 2024-09-11 20:40:15
tags:
  - Cpp
categories:
  - CS106L
---
# Content

- [x] template的使用周期
- [x] 实例化
- [x] 模板就是你子定义的时候不清楚是什么类型，但是有人使用的时候会明确知道是什么类型了
- [x] 函数模板，其本质还说函数吗
- [x] 概念提升（concept lifting）
- [x] PPT P76
- [x] g++ std=C++17 hello.cpp -o hello 
- [x] 函数出现的错误
- [x] 决策和特定决策（decision ）
- [x] predicate
- [ ] inline
- [ ] thunks
- [x] ` auto func = [&,a]() {}`
- [x]  隐式接口
- [ ] tree set

>这一次课主要讲述了 template模板，它为C++提供了强大的**泛型编程**，是C++的核心部分。其中在本次课程中主要讲述了一些C++中的编程思想，**Concept Lifting**和**Implicit Interfaces**这样的编程思想。

# 1.Template
假如说我们有下面这一段代码，是用来求出2个数值中哪个更小一些，返回时小的在前面。
```Cpp
pair<int, int> my_minmax(int a, int b) {
    if (a < b) return {a, b};
    else return {b, a};
}
```
但是上面的代码只能处理int类型的pair，如果我们想要比较两个double或者是string类型的呢？我们可以想到重载，没错，通过重载的话我们可以得到下面的2组代码，这样就满足了我们想要比较double和string的需求了。
```cpp
pair<double, double> my_minmax(double a, double b) {
    if (a < b) return {a, b};
    else return {b, a};
}

pair<string, string> my_minmax(string a, string b) {
    if (a < b) return {a, b};
    else return {b, a};
}
```
但是这样未免有一个问题，那么，我们怎么样才能比较我们自己定义的types呢？比方说两个class对象。我们可以观察发现，这三组代码中是由很多相似的地方的，那么我们能否将它们变成同一段代码呢？答案是可以的，这时候我们就要用到template模板了。

## 1.1 模板简介
模板（Template）是C++中的一种泛型编程特性，允许编写通用的、类型独立的代码。使用模板可以让一个函数、类、结构等操作多个类型，而不必为每个类型单独编写代码。
模板可以分成**函数模板**和**类模板**，这两个模板在[C++Primer](https://salianbooth.github.io/2024/08/02/C-Primer-learning/)中都有简单的提及，这里我们就单独介绍函数模板。
在之前的例子中，我们可以将`my_minmax( int a,int b)`函数泛化成一个函数模板，下面是代码示例：
```Cpp
// 函数模板的定义
template <typename T>
pair<T, T> my_minmax(T a, T b) {
    if (a < b) 
        return {a, b};
    else 
        return {b, a};
}

int main() {
    // 这里模板实例化为 int 类型
    pair<int, int> result1 = my_minmax(3, 7);
    
    // 这里模板实例化为 double 类型
    pair<double, double> result2 = my_minmax(3.5, 2.1);
    
    // 这里模板实例化为 string 类型
    pair<string, string> result3 = my_minmax(string("apple"), string("banana"));

    return 0;
}

```
其实模板的核心思想就是我们在定义的时候不指定具体的类型，但是根据传递的类型来确定是什么类型。这种设计允许编写更加灵活和通用的代码，避免为不同类型重复编写类似的代码。
虽然模板很方便，但是需要正确的使用，其中不正确使用模板会出现不少错误，我们在编译的时候，如果我们传入了不正确的参数，那么编译器会逐个类型去尝试匹配（直到找到匹配正确），这样会报错。下面是一个简单的使用错误的例子：
```Cpp
template <typename T>
T add(T a, T b) {
    return a + b;
}

int main() {
    int x = 5;
    double y = 7.5;
    
    // 错误：int 和 double 是不同类型，无法推导出统一的 T
    cout << add(x, y) << endl;
    return 0;
}

```
## 1.2实例化
课程中就讲了如何隐式实例化，这里就直接chatgpt了一下其他的集中方法，说实话感觉这一节知识密度太低了。
**模板实例化**是C++模板编程中的一个核心概念，它是指编译器根据模板的定义和传入的具体类型，生成对应的具体代码的过程。模板实例化会在程序编译过程中发生，并生成针对具体类型的函数或类代码。

### 模板实例化的基本原理

当你定义一个模板时，实际上并不会生成任何代码。模板就像一个蓝图或占位符，只有在实际使用模板时，编译器才会根据传入的具体类型生成对应的函数或类。这就是模板实例化的过程。

#### 模板实例化可以分为以下几种方式：

1. **隐式实例化**（Implicit Instantiation）
2. **显式实例化**（Explicit Instantiation）
3. **显式特化**（Explicit Specialization）

### 1. 隐式实例化

**隐式实例化**是最常见的模板实例化方式，发生在你**调用**模板时。编译器会根据传入的参数类型，自动推导模板的类型参数，并生成对应的代码。这种方式最常用，且不需要开发者额外干预。

#### 例子：
```cpp
template <typename T>
T add(T a, T b) {
    return a + b;
}

int main() {
    int x = 3, y = 4;
    double a = 1.1, b = 2.2;

    cout << add(x, y) << endl;    // 编译器隐式实例化 add<int>(int, int)
    cout << add(a, b) << endl;    // 编译器隐式实例化 add<double>(double, double)
}
```

在这个例子中，`add` 函数模板被定义了，编译器会在调用 `add(x, y)` 时实例化 `add<int>`，在调用 `add(a, b)` 时实例化 `add<double>`。这种实例化是**隐式的**，编译器会根据传入的类型自动推导。

### 2. 显式实例化

**显式实例化**是指开发者明确指定某个模板的类型参数，并让编译器提前生成特定类型的实例化代码。显式实例化可以确保模板的某个具体类型提前生成代码，而不是等到使用时才实例化。

显式实例化通常放在 `.cpp` 文件中，避免在每个包含模板的翻译单元中都实例化同一个类型的模板，减少编译时间和代码冗余。

#### 语法：
```cpp
template class/typename 模板名<类型>;
```

#### 例子：
```cpp
template <typename T>
T multiply(T a, T b) {
    return a * b;
}

// 在某个.cpp文件中，显式实例化 multiply<int> 和 multiply<double>
template int multiply<int>(int, int);
template double multiply<double>(double, double);

int main() {
    cout << multiply(3, 4) << endl;         // multiply<int> 已经显式实例化
    cout << multiply(2.5, 3.5) << endl;     // multiply<double> 已经显式实例化
}
```

这里，`multiply<int>` 和 `multiply<double>` 被**显式实例化**，即便程序中没有显式调用它们，编译器也会提前生成对应的代码。

### 3. 显式特化

**显式特化**用于某种类型的模板提供特殊实现。这与普通的实例化不同，显式特化定义了一个完全独立的、定制的版本。

#### 例子：
```cpp
template <typename T>
T max(T a, T b) {
    return (a > b) ? a : b;
}

// 针对 const char* 类型的特化
template <>
const char* max<const char*>(const char* a, const char* b) {
    return (strcmp(a, b) > 0) ? a : b;
}

int main() {
    cout << max(10, 20) << endl;             // 使用通用模板
    cout << max("apple", "banana") << endl;  // 使用特化版本
}
```

在这个例子中，`max` 函数模板被定义为通用模板，适用于大部分类型。但对于 `const char*` 类型，提供了一个特殊的实现。当使用 `max("apple", "banana")` 时，会调用这个特化版本，而不是通用版本。

### 模板实例化的机制

模板实例化是在编译期完成的，编译器在**模板的使用点**根据传入的类型生成模板代码。模板实例化的具体机制可以分为以下几个步骤：

1. **类型推导**：编译器根据模板函数或类的参数类型推导出模板参数。
2. **模板生成**：根据推导出的模板参数，编译器生成一个具体的模板实例。
3. **代码生成**：编译器为这个模板实例生成具体的代码。

### 重复实例化问题

模板实例化在每个使用模板的翻译单元（即每个 `.cpp` 文件）都会单独生成代码，这可能导致重复实例化问题，尤其在大型项目中，代码的重复生成会增加编译时间和最终的可执行文件大小。

为了解决这个问题，可以通过**显式实例化**或使用 `inline` 指令让编译器优化实例化行为。

#### 解决重复实例化的方法：

1. **显式实例化**：在某个 `.cpp` 文件中显式实例化模板，确保每个类型的实例化只生成一次。
2. **`inline` 关键字**：对于模板函数，可以使用 `inline` 关键字让编译器优化实例化行为，避免重复生成代码。




## 1.3函数模板本质
**函数模板**在本质上与普通的函数有一些不同，虽然它们在语法上看起来相似，但它们的工作机制和编译过程存在显著差异。
#### 函数模板：
```cpp
template <typename T>
T add(T a, T b) {
    return a + b;
}
```
在这个函数模板中，`T` 是一个**占位符**，代表任意类型。编译器在使用时，会根据传入的类型生成不同的 `add` 函数。例如：
- `add(3, 4)` 会生成 `int add(int a, int b)`。
- `add(2.5, 3.5)` 会生成 `double add(double a, double b)`。
所以对于函数模板来说：
- **函数模板**本质上是一个**生成函数的工具**，它是参数化的，直到被使用时才生成真正的函数。
- **普通函数**是已经确定的函数，编译时就为它生成了代码。
因此，从本质上讲，**函数模板并不是一个函数**，而是一个可以生成具体函数的**蓝图**。一旦函数模板被实例化，所生成的具体函数才可以被视为函数。


# 2.concept lifting
**Concept Lifting** 是 C++20 中与**概念（Concepts）**相关的一个高级技术话题。尽管这个术语没有明确的官方定义，但通常在泛型编程或模板编程的背景下，**concept lifting** 指的是 **将泛型代码中的约束提升到概念层次**，以便更好地表达代码意图、提高可读性、增强可重用性，并确保类型约束在编译时被准确验证。


在你提到的“Concept Lifting”的部分，讲解了如何通过放宽对函数参数的假设条件，使得函数能够适应更多的场景。这种技术通常用于模板编程中，使得代码更加通用和灵活。

### 什么是“Concept Lifting”？

**“Concept Lifting”** 的目的是通过审视并质疑我们对参数所作的假设，看看是否能够在不影响功能的前提下，放宽这些限制，从而解决更通用的问题。例如，假设我们有一个函数最初只针对 `vector<int>` 进行操作，但通过放宽对容器类型的限制，我们可以使这个函数处理所有类型的容器，这就是“Concept Lifting”的基本思想。

### 例子说明

从文档中我们看到，原始的函数可能如下所示：

```cpp
template <>
int countOccurences(const vector<int>& vec, int val) {
    int count = 0; 
    for (size_t i = 0; i < vec.size(); ++i) { 
        if (vec[i] == val) ++count; 
    } 
    return count; 
}
```

这个函数计算一个整数在 `vector<int>` 中出现的次数。这个实现存在以下假设：
1. 容器类型是 `vector<int>`。
2. 容器中的元素是 `int` 类型。

**通过 Concept Lifting，我们可以把这个函数提升到更通用的形式**：

### 第一步：处理不同的数据类型

首先，我们可以通过模板将数据类型泛化：

```cpp
template <typename DataType>
int countOccurences(const vector<DataType>& vec, DataType val) {
    int count = 0; 
    for (size_t i = 0; i < vec.size(); ++i) { 
        if (vec[i] == val) ++count; 
    } 
    return count; 
}
```

这段代码可以处理任何类型的 `vector`，而不仅仅是 `int` 类型。
但是这个时候如果我们传入的是list类型，那么就会发生错误，这是因为list底层是通过链表实现的，不能通过`[]`下标进行访问。
### 第二步：处理不同的容器类型

接下来，我们可以进一步泛化容器的类型，使得这个函数能够处理任何 STL 容器，而不仅仅是 `vector`。

```cpp
template <typename Collection, typename DataType>
int countOccurences(const Collection& collection, DataType val) {
    int count = 0; 
    for (auto iter = collection.begin(); iter != collection.end(); ++iter) { 
        if (*iter == val) ++count; 
    } 
    return count; 
}
```

通过这一改动，`countOccurences` 函数现在可以处理各种容器类型（如 `list`、`deque` 等），并计算任何类型元素的出现次数。

### 第三步：使用迭代器

最后，为了更进一步，我们可以使用迭代器来实现函数，这样可以处理任何支持迭代器的范围，而不仅仅是容器：

```cpp
template <typename InputIt, typename DataType>
int countOccurences(InputIt begin, InputIt end, DataType val) {
    int count = 0; 
    for (auto iter = begin; iter != end; ++iter) { 
        if (*iter == val) ++count; 
    } 
    return count; 
}
```

这个版本更加通用，它可以处理任何输入迭代器范围，无论它们来自哪种容器或集合。


# 3.Lambda
lambda[C++Primer](https://salianbooth.github.io/2024/08/02/C-Primer-learning/)之前也有所提过：
Lambda 表达式是 C++11 引入的一种功能，允许你在代码中定义匿名函数。它们通常用于简化代码，使代码更简洁、更易读。Lambda 表达式的基本语法如下：

```c++
[capture](parameters) -> return_type { body }
```

- **`capture`**：捕获列表，用于捕获上下文中的变量。
- **`parameters`**：参数列表，与普通函数的参数列表类似。
- **`return_type`**：返回类型，可以省略，如果可以从返回的表达式中推断出来。
- **`body`**：函数体，包含要执行的代码。
我们这里要讲一些其他的东西，比方说，谓词predicate。
## 3.1 predicate
在 C++ 中，**predicate（谓词）** 是一个返回布尔值的函数或函数对象，通常用于检查某种条件或性质。谓词主要用于算法中，来判断给定元素是否满足某些条件。这种条件可以是简单的比较，也可以是更复杂的逻辑判断。
C++ 中的谓词可以分为以下两类：

1. **一元谓词（Unary Predicate）**：
    
    - 接受一个参数，返回一个布尔值。
    - 例如，`std::find_if`、`std::count_if` 等标准算法函数可以使用一元谓词。
    - 常用于判断单个元素是否符合某个条件。
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

// 一元谓词函数
bool isEven(int n) {
    return n % 2 == 0;
}

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5, 6};
    
    // 使用 std::find_if 查找第一个偶数
    auto it = std::find_if(vec.begin(), vec.end(), isEven);

    if (it != vec.end()) {
        std::cout << "First even number is " << *it << std::endl;
    }
}

```
**二元谓词（Binary Predicate）**：

- 接受两个参数，返回一个布尔值。
- 例如，`std::sort`、`std::set_intersection` 等标准算法函数可以使用二元谓词。
- 常用于比较两个元素。
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

// 二元谓词函数
bool customCompare(int a, int b) {
    return a > b; // 降序排序
}

int main() {
    std::vector<int> vec = {5, 2, 9, 1, 5, 6};

    // 使用 std::sort 并传递二元谓词
    std::sort(vec.begin(), vec.end(), customCompare);

    for (int n : vec) {
        std::cout << n << " ";
    }
}

```
这里ppt中用了一段代码来解释了谓词Predicate怎么使用的：
```cpp
int countOccurrences(InputIt begin, InputIt end, UniPred predicate) {

	int count = 0;
	for (auto iter = begin; iter != end; ++iter) {
	    if (predicate(*iter)) 
	        ++count;
	}
	return count;
}
```
- 这里使用了模板定义，参数有三个：
    
    - `InputIt`：表示输入迭代器类型，通常用于遍历容器中的元素。
        
    - `DataType`：表示要处理的数据类型。虽然这个参数在代码中没有直接使用，但它可以用于特定情况下的特化或限制。
        
    - `UniPred`：表示一个谓词（predicate），它是一个可以接受某种类型参数并返回布尔值的函数或函数对象（例如 Lambda 表达式或函数指针）。
如何在main函数中使用呢？我们假设有一个数组，我们想要统计其中大于5的元素个数，可以这样使用这个函数：
```cpp
#include <iostream>
#include <vector>

int main() {
    std::vector<int> vec = {1, 2, 3, 6, 8, 5, 9};

    // 使用 lambda 表达式作为谓词函数
    auto greaterThanFive = [](int val) { return val > 5; };

    // 调用模板函数
    int count = countOccurrences(vec.begin(), vec.end(), greaterThanFive);

    std::cout << "Number of elements greater than 5: " << count << std::endl;
}
```
这样的话就能大大提高我们代码中的逻辑和泛式编程
## 3.2lambda capture
在C++的lambda表达式中，捕获外部变量的方式分为**按值传递**和**按引用传递**，分别对应不同的捕获机制。它们决定了lambda内部如何访问外部作用域的变量，以及是否可以修改这些变量。
### 1. **按值捕获（值传递）**
按值捕获意味着在创建lambda时，会对外部作用域的变量进行**拷贝**，lambda内部使用的是这些变量的**副本**。因此，在lambda表达式内部修改这些变量不会影响外部的原始变量。
#### 语法：
捕获列表中的变量名前面没有符号时，表示按值捕获：
```cpp
[a, b]() { /* 按值捕获a和b */ }
```
或者用`=`符号表示**按值捕获所有外部变量**：
```cpp
[=]() { /* 按值捕获所有外部变量 */ }
```
#### 示例：
```cpp
#include <iostream>

int main() {
    int x = 10;
    auto func = [x]() { 
        std::cout << "Inside lambda, x = " << x << std::endl; 
        // 此处x是一个副本，修改它不会影响外部的x
    };
    
    x = 20;  // 修改外部的x
    func();  // 调用lambda
    std::cout << "Outside lambda, x = " << x << std::endl;  // 输出x的值
    
    return 0;
}
```
#### 输出：
```
Inside lambda, x = 10
Outside lambda, x = 20
```

- 在lambda表达式内，`x`是按值捕获的，因此它是一个副本。即使外部的`x`后来修改为20，lambda内部的`x`仍然是最初捕获时的值10。
### 2. **按引用捕获（引用传递）**
按引用捕获意味着lambda表达式内部直接引用外部作用域中的变量，因此在lambda中对这些变量的修改会直接影响外部变量的值。
#### 语法：
捕获列表中的变量名前面有`&`符号，表示按引用捕获：
```cpp
[&a, &b]() { /* 按引用捕获a和b */ }
```
或者用`&`符号表示**按引用捕获所有外部变量**：
```cpp
[&]() { /* 按引用捕获所有外部变量 */ }
```
#### 示例：
```cpp
#include <iostream>

int main() {
    int x = 10;
    auto func = [&x]() { 
        x = 30;  // 修改外部的x
        std::cout << "Inside lambda, x = " << x << std::endl; 
    };
    
    x = 20;  // 修改外部的x
    func();  // 调用lambda
    std::cout << "Outside lambda, x = " << x << std::endl;  // 输出x的值
    
    return 0;
}
```

#### 输出：
```
Inside lambda, x = 30
Outside lambda, x = 30
```

- 这里的`x`是按引用捕获的，因此lambda内部修改了`x`，外部的`x`也随之被修改。

### 3. **混合捕获**
你可以在一个lambda中同时按值和按引用捕获不同的变量。通过捕获列表指定每个变量的捕获方式。

#### 示例：
```cpp
#include <iostream>

int main() {
    int x = 10, y = 20;
    auto func = [x, &y]() { 
        // x 按值捕获，y 按引用捕获
        std::cout << "Inside lambda: x = " << x << ", y = " << y << std::endl;
        // 修改y的值
        y = 30; 
    };

    x = 40; // 修改外部x
    y = 50; // 修改外部y

    func();  // 调用lambda

    std::cout << "Outside lambda: x = " << x << ", y = " << y << std::endl;

    return 0;
}
```

#### 输出：
```
Inside lambda: x = 10, y = 50
Outside lambda: x = 40, y = 30
```

- `x`是按值捕获的，因此lambda内的`x`仍然是10（捕获时的值），即使外部的`x`被修改为40。
- `y`是按引用捕获的，因此lambda内部修改了`y`的值，外部的`y`也被修改为30。

### 4. **按值捕获与按引用捕获的区别**
- **按值捕获**：lambda内部使用的是变量的副本，不能修改外部变量的值。
- **按引用捕获**：lambda内部使用的是外部变量的引用，修改lambda内部的变量会影响外部变量。

### 5. **捕获方式的选择**
选择按值捕获或按引用捕获取决于你的需求：
- 如果不希望lambda修改外部的变量，或者需要在lambda执行时保持变量的原始值，可以使用**按值捕获**。
- 如果希望lambda能够修改外部的变量值，或者希望lambda始终访问最新的外部变量状态，使用**按引用捕获**。
# 小知识
- [ ] auto不能再参数中使用
- [ ] [ ] C++中C语言的字符串和字符串的区别 ->c String
- [ ] [ ] strteam can't be copieds
- [ ] [ ] 迭代器传给函数时，它是自动按引用传递的吗 -> 实际上是copy？
- [ ] 那些类型不支持`[]`访问呢？


上课这么自由吗

![Clip_2024-09-11_21-10-04.png](https://gitee.com/salianbooth/picture/raw/master/202409112110442.png)

