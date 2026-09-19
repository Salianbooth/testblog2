---
title: CS106L:Lecture 3
date: 2024-09-04 16:44:59
tags:
  - Cpp
categories:
  - CS106L
---
>这一次主要讲述了STL，这是cpp中很重要的一部分，但是笔者前几天去打数模大赛了，熬了2天夜，昨天又休息了一天😭，今天开始写了（9.10）,加油吧！

# 0.Extended Discussion
>在开始正式的内容之前，讲述了一些有关C++的新特性
## 1 Structured Bindings
这是C++17引入的一种语法特性，它允许我们可以将多个返回值绑定到不同的函数上，经常用于pair中，下面是简答的示例:
1.用于pair
```Cpp
#include <iostream>
#include <utility>  // for std::pair

int main() {
    std::pair<int, double> p = {1, 4.5};

    // 使用 structured binding 直接解构
    auto [a, b] = p;

    std::cout << "a: " << a << ", b: " << b << std::endl;

    return 0;
}

```
在这个示例中，我们将p的2个值解构成a，b两个变量，这样就能直接输出p的两个值，不用再用p.x和p.y了。
2.用于元组
```cpp
#include <iostream>
#include <tuple>  // for std::tuple

std::tuple<int, double, char> getTuple() {
    return {1, 3.14, 'c'};
}

int main() {
    // 解构 tuple
    auto [x, y, z] = getTuple();

    std::cout << "x: " << x << ", y: " << y << ", z: " << z << std::endl;

    return 0;
}

```
在这个示例中，我们通过将getTuple( )函数的返回值，绑定在x，y，z中，这样就能分别得到3个返回值了。
3.用于数组
这里就不示范了
## 2.Uniform Initialization（统一初始化）
这是C++11引入的新概念，这主要是为所有的类提供统一初始化的方式。主要是通过 `{ }`来实现的。
1. **统一语法**：
   
    - 无论是内置类型还是自定义类型，都可以使用相同的初始化语法。
    - 例如，使用大括号 `{}` 来初始化数组、结构体、对象、指针等。
2. **避免窄化转换（Narrowing Conversion）**：
   
    - 统一初始化语法能够防止某些隐式类型转换，特别是会导致数据精度丢失的窄化转换。例如，尝试将一个 `double` 值初始化为 `int` 会产生编译错误。

1. **基本类型的统一初始化**：
   ```cpp
   int a{10};       // 初始化为10
   double b{3.14};  // 初始化为3.14
   ```

2. **数组的统一初始化**：
   ```cpp
   int arr[3]{1, 2, 3};  // 初始化一个数组
   ```

3. **类对象的统一初始化**：
   ```cpp
   class MyClass {
   public:
       int x;
       double y;
   };
   
   MyClass obj{10, 3.14};  // 使用统一初始化初始化类对象
   ```

4. **容器的统一初始化**：
   ```cpp
   std::vector<int> vec{1, 2, 3, 4, 5};  // 初始化一个vector
   ```

5. **防止窄化转换**：
   ```cpp
   int x{3.14};  // 编译错误，防止窄化转换
   ```

### 构造函数与统一初始化：
在C++11及之后，如果一个类具有构造函数，统一初始化语法会调用相应的构造函数。对于带有初始化列表的构造函数，统一初始化语法也可以和`std::initializer_list`配合使用。
# 1.Overview of STL
下图是STL的一些基本内容[C++Primer](https://salianbooth.github.io/2024/08/02/C-Primer-learning/)
![Clip_2024-09-10_19-41-06.png](https://gitee.com/salianbooth/picture/raw/master/202409101941784.png)


# 2.vector&deque
## 2.1默认边界
在C++标准库（STL）中，容器的默认访问方式并不会启用边界检查，具体来说，使用下标运算符 `[]` 访问容器元素时不会进行边界检查，而使用 `.at()` 方法时会启用边界检查。
### 1. **下标索引 `[]`**：
   - **不进行边界检查**：如果访问超出容器范围的元素，行为是未定义的（Undefined Behavior, UB），通常会导致程序崩溃或不确定的错误。
   - **适用场景**：如果你确信访问的下标是合法的，并且你追求性能，使用 `[]` 会更高效，因为它不增加额外的开销。

   ```cpp
   std::vector<int> vec = {1, 2, 3};
   int value = vec[5];  // 未定义行为：没有边界检查，超出范围
   ```
### 2. **`.at()` 函数**：
   - **启用边界检查**：如果尝试访问超出范围的元素，会抛出 `std::out_of_range` 异常。边界检查虽然稍微增加了运行时开销，但能显著提高安全性。
   - **适用场景**：适合在你不确定下标是否安全的情况下使用，或者在需要处理异常时。

   ```cpp
   try {
       int value = vec.at(5);  // 抛出std::out_of_range异常
   } catch (const std::out_of_range& e) {
       std::cerr << "Index out of bounds: " << e.what() << '\n';
   }
   ```

### 对比总结：
- **`[]`**：没有边界检查，性能较高，适合在高性能需求和确保下标合法时使用。
- **`.at()`**：有边界检查，较为安全，在超出范围时抛出异常，适合安全性更高的场景。

一般来说，都用`[]`。

## 2.2deque
这个也是STL的一个序列容器，名称来自“双端队列”（double-ended queue），它支持在序列的两端进行高效的插入和删除操作。这个和vector的不同点就是它支持在两端插入和删除，更加高效一些。
`std::deque` 是 C++ 标准库中的一种序列容器，名称来源于“双端队列”（double-ended queue），它支持在序列的两端进行高效的插入和删除操作。
### 1. **`std::deque` 的常用操作**：

以下是 `std::deque` 的一些常用操作和函数：

```cpp
#include <iostream>
#include <deque>

int main() {
    std::deque<int> dq = {1, 2, 3, 4, 5};  // 初始化

    // 插入和删除操作
    dq.push_back(6);     // 在末尾插入元素
    dq.push_front(0);    // 在开头插入元素
    dq.pop_back();       // 删除末尾元素
    dq.pop_front();      // 删除开头元素

    // 访问元素
    int front = dq.front();   // 访问第一个元素
    int back = dq.back();     // 访问最后一个元素
    int second = dq[1];       // 通过下标访问（无边界检查）
    int third = dq.at(2);     // 通过 .at() 方法访问（有边界检查）

    // 输出所有元素
    for (int n : dq) {
        std::cout << n << " ";
    }

    return 0;
}
```

# 3.Adapter
我们经常使用的stack和queue就是适配器。
## 3.1什么是适配器？

**适配器**就像是一个“转换器”。它把一种东西转换成另一种形式，方便我们使用。例如，充电插头的转换器就是一个适配器：它把不同形状的插头适配成你能用的插头。
适配器最常用于**容器适配器**。它们把已有的容器（比如 `std::deque`、`std::vector`）包装起来，变成另一种特定用途的数据结构。这样，你就可以像操作新的数据结构一样使用原来的容器。


### 常见的容器适配器

1. `std::stack`（栈适配器）
   
   - **栈**是后进先出（LIFO）的数据结构。想象一摞盘子，新的盘子总是放在最上面，拿的时候也只能从最上面拿。
   
   - `std::stack` 其实是包装了 `std::deque` 或 `std::vector`，让你只能从**顶部**放入或取出元素。
   
   - **例子：**
     ```cpp
     std::stack<int, std::vector<int>>
     std::stack<int> stack;
     stack.push(1);   // 放入元素1
     stack.push(2);   // 放入元素2
     stack.pop();     // 移除元素2，只能移除最上面的
     ```
   
2. **`std::queue`（队列适配器）**
   - **队列**是先进先出（FIFO）的数据结构。就像排队买票，最先排的人最先买到票。
   - `std::queue` 也包装了 `std::deque`，但它只允许在**末尾插入**元素，并从**头部删除**元素。
   - **例子：**
     ```cpp
     std::queue<int> queue;
     queue.push(1);   // 在队列尾部插入1
     queue.push(2);   // 在队列尾部插入2
     queue.pop();     // 移除队列头部的1
     ```

3. **`std::priority_queue`（优先队列适配器）**
   - **优先队列**是特殊的队列，元素根据优先级来出队。比如急诊室的病人，病情更严重的人会优先得到治疗。
   - `std::priority_queue` 包装了 `std::vector`，自动为你将元素按优先级排序。
   - **例子：**
     ```cpp
     std::priority_queue<int> pq;
     pq.push(5);   // 插入5
     pq.push(2);   // 插入2
     pq.push(8);   // 插入8，优先级最高
     pq.pop();     // 移除8，因为它优先级最高
     ```


PS: `std::stack<int, std::vector<int>>`是使用 `std::stack` 的一个特殊形式，其中底层容器类型被显式指定为 `std::vector<int>`。这是 `std::stack` 适配器的一种灵活用法，允许你选择用于存储的容器类型。
### 为什么需要适配器？

**适配器**的好处是它能让你轻松使用现有的容器，但只暴露出特定的功能，符合特定的数据结构需求。

比如，`std::stack` 是基于 `std::deque` 实现的。`std::deque` 可以在两端插入或删除元素，但 `std::stack` 只让你在“顶部”进行操作。这就是适配器的作用——限制某些功能，让它看起来更像你想要的数据结构。

### C++Philosophy 
这样我们本来可以用vector的地方，却用了stack，这样是为了什么？和C++哲学有关吗？
是的，这和C++的设计哲学密切相关。C++的哲学之一就是**提供灵活性和控制**。使用适配器如 `std::stack` 而不是直接使用 `std::vector` 有几个好处：
1. **简化代码**：`std::stack` 限制了操作，只允许在栈顶插入和删除元素。这种限制让代码更简洁，容易理解和维护，避免了对 `std::vector` 的不必要操作。

2. **明确数据结构的意图**：使用 `std::stack` 可以明确你的意图是要使用后进先出的数据结构，而不是一个通用的动态数组。这样，其他阅读代码的人可以迅速了解数据的处理方式。

3. **避免误用**：`std::stack` 通过封装 `std::vector`，限制了对容器的操作。这种限制帮助避免了不适合栈操作的其他操作，比如随机访问和在中间插入或删除元素。

4. **符合设计原则**：C++的设计原则之一是**“尽量封装实现，暴露接口”**。`std::stack` 通过提供栈操作的专用接口，实现了这一原则，让开发者只关注栈相关的操作，而不需要关心底层的实现细节。


# 4.Associative Containers

关联容器存储的是键值对，元素根据键自动排序或通过哈希映射。以下是主要的关联容器：

- **有序关联容器**（元素根据键排序）：
    - **`std::set`**：存储唯一元素，元素按顺序排列。
    - **`std::map`**：存储键值对，键唯一，按顺序排列。
    - **`std::multiset`**：存储重复元素，按顺序排列。
    - **`std::multimap`**：存储键值对，键可以重复，按顺序排列。
- **无序关联容器**（元素无序存储，通过哈希映射）：
    - **`std::unordered_set`**：存储唯一元素，无序存储。
    - **`std::unordered_map`**：存储键值对，键唯一，无序存储。
    - **`std::unordered_multiset`**：存储重复元素，无序存储。
## 4.1map的排序行为
map是基于红黑树来实现的。因此，map会对**其存储的键进行排序**。这个排序的依据是键值的比较操作，默认情况下是使用 `operator<`（即 "小于" 运算符）进行排序。
### `std::map` 的排序行为

1. **自动排序**：每当你向 `std::map` 插入键值对时，容器会根据键值对中的键，使用小于运算符（`<`）对键进行排序。`std::map` 始终保持其元素按键的升序排列。因此，遍历 `std::map` 的时候，你会看到按顺序排列的键。

2. **自定义排序**：如果你不想使用默认的 "小于" 运算符来排序键，你也可以在声明 `std::map` 时提供一个自定义的比较函数或函数对象。例如，可以传入一个自定义的比较函数对象，以改变排序规则。

### 示例：`std::map` 自动排序

```cpp
#include <iostream>
#include <map>

int main() {
    // 创建一个 std::map，键为 int，值为 std::string
    std::map<int, std::string> myMap;

    // 插入元素（会根据键自动排序）
    myMap[3] = "Three";
    myMap[1] = "One";
    myMap[2] = "Two";
    myMap[5] = "Five";
    myMap[4] = "Four";

    // 遍历 std::map，输出排序后的键值对
    for (const auto& pair : myMap) {
        std::cout << pair.first << ": " << pair.second << std::endl;
    }

    return 0;
}
```

**输出：**

```
1: One
2: Two
3: Three
4: Four
5: Five
```

在这个例子中，尽管插入的顺序是随机的，`std::map` 根据键值自动对其进行了升序排列。

### 自定义排序规则

如果你想让 `std::map` 按照其他规则（例如降序）进行排序，你可以提供一个自定义的比较函数对象或函数。以下是一个让 `std::map` 按键值降序排列的示例：

```cpp
#include <iostream>
#include <map>

// 自定义比较函数对象，实现降序
struct CustomCompare {
    bool operator()(const int& a, const int& b) const {
        return a > b;  // 改为 a > b 即可实现降序
    }
};

int main() {
    // 使用自定义比较函数的 std::map
    std::map<int, std::string, CustomCompare> myMap;

    myMap[3] = "Three";
    myMap[1] = "One";
    myMap[2] = "Two";
    myMap[5] = "Five";
    myMap[4] = "Four";

    // 遍历并输出降序排列的 std::map
    for (const auto& pair : myMap) {
        std::cout << pair.first << ": " << pair.second << std::endl;
    }

    return 0;
}
```

**输出：**

```
5: Five
4: Four
3: Three
2: Two
1: One
```

### 总结

- **`std::map` 自动对键进行排序**，默认按升序排列，使用小于运算符（`<`）。
- 如果你想改变排序规则，可以提供一个自定义的比较函数或函数对象。

`std::map` 的自动排序特性使得它非常适合在需要有序键值对的场景中使用，例如字典、数据库索引等。

## 4.2map的查找
在 C++ 中，`std::map` 提供了多种方法来查找元素并输出其内容。我们可以使用 `find()` 函数来查找特定的键，还可以通过遍历 `std::map` 来输出其中的键值对。

### 1. 在 `std::map` 中查找元素

#### 使用 `find()` 函数

`std::map::find()` 是查找元素的标准方法，它接受一个键作为参数，返回一个迭代器，指向对应的键值对。如果找不到键，`find()` 会返回 `map.end()` 迭代器。

**语法：**
```cpp
std::map<KeyType, ValueType>::iterator find(const KeyType& key);
```

**示例：**

```cpp
#include <iostream>
#include <map>

int main() {
    std::map<int, std::string> myMap;
    myMap[1] = "One";
    myMap[2] = "Two";
    myMap[3] = "Three";

    int keyToFind = 2;
    auto it = myMap.find(keyToFind);  // 查找键为 2 的元素

    if (it != myMap.end()) {
        std::cout << "Found: " << it->first << " => " << it->second << std::endl;
    } else {
        std::cout << "Key " << keyToFind << " not found" << std::endl;
    }

    return 0;
}
```

**输出：**

```
Found: 2 => Two
```

在这个例子中，我们查找了键 `2`，并成功找到了对应的值。如果键不存在，我们可以通过检查迭代器是否等于 `map.end()` 来判断是否找到了键。

#### 使用 `operator[]`

你也可以通过 `[]` 操作符来访问 `std::map` 中的元素。不过，**要注意**，如果键不存在，`operator[]` 会自动插入一个新元素并将其值初始化（例如，字符串为空字符串，整数为零）。

```cpp
std::cout << myMap[2] << std::endl;  // 直接访问键 2 对应的值
```

#### 使用cout（）
在 C++ 中，**你可以使用 `std::map` 的 `operator[]` 来查找和输出值**，并且可以通过检测返回的值是否为默认值来判断该键是否存在。但这种方式和 `find()` 不同，它会有以下行为：

- **`operator[]` 如果键不存在**，它会自动插入该键，并将其对应的值初始化为默认值（例如：`int` 类型的值会被初始化为 `0`，`std::string` 类型会被初始化为空字符串 `""`）。

因此，如果你使用 `operator[]` 来判断键是否存在，并通过输出值是否为 `0` 来检查，这是可以的，但是这种方法会 **无意间插入新键**，并可能不符合你的需求。

```Cpp
#include <iostream>
#include <map>

int main() {
    std::map<int, int> myMap;
    myMap[1] = 100;
    myMap[2] = 200;

    // 使用 [] 操作符查找
    std::cout << "Key 1: " << myMap[1] << std::endl;  // 存在，输出 100
    std::cout << "Key 3: " << myMap[3] << std::endl;  // 不存在，插入键 3，输出 0

    // 检查键 3 是否被插入
    std::cout << "Size of map: " << myMap.size() << std::endl;  // 输出 3，说明键 3 已插入

    return 0;
}

```


# 5.iterator
在学习完关联容器(associative containers)后，是否有这样的疑问，我们如何遍历它们呢？我们要知道，关联容器是没有下标，我们之前所看到的`map[1]`都是map中键值对中的键访问。
这时候，iterator就出现了，在查单词的过程中，我发现了iterate over这个词组指的是遍历的意思，那么迭代器被发明的意义就在于迭代遍历吧。
下面是一个简单的示例，其中的`std::vector<int>`可以换成其他不同的containers。
```cpp
std::vector<int> vec = {1, 2, 3};
for (std::vector<int>::iterator it = vec.begin(); it != vec.end(); ++it) {
    std::cout << *it << std::endl;
}
```
## 5.1 常用函数
### `begin()`
- **功能**：`begin()` 返回指向容器第一个元素的迭代器。如果容器为空，则 `begin()` 返回与 `end()` 相同的迭代器。
- **返回值**：正向迭代器，指向容器中第一个元素的位置。

```cpp
std::vector<int> vec = {1, 2, 3};
auto it = vec.begin();  // it 指向 vec 的第一个元素（值为 1）
std::cout << *it << std::endl;  // 输出 1
```
### `end()`
- **功能**：`end()` 返回指向容器 **末尾之后** 的迭代器，即指向 **最后一个元素之后** 的一个位置。这个迭代器并不指向一个有效的元素，通常用于标记遍历的终止条件。
- **返回值**：正向迭代器，指向容器中最后一个元素的下一个位置。

```cpp
std::vector<int> vec = {1, 2, 3};
auto it = vec.end();  // it 指向 vec 的末尾之后的位置
--it;                 // 移动到最后一个元素
std::cout << *it << std::endl;  // 输出 3
```


## 5.2 iterator type
#### **2.1 Input Iterator (输入迭代器)**

- **特性**：只读，只能一次性向前移动（单向遍历）。
- **用途**：用于单次读取数据，例如输入流或一次性处理序列。
- **示例**：`std::istream_iterator`

#### **2.2 Output Iterator (输出迭代器)**

- **特性**：只写，也只能一次性向前移动。
- **用途**：用于输出操作，写入数据到某个目的地。
- **示例**：`std::ostream_iterator`

#### **2.3 Forward Iterator (前向迭代器)**

- **特性**：可以读写，支持多次访问当前元素，但只能向前移动。
- **用途**：用于可以重复访问元素的场景。
- **示例**：`std::forward_list::iterator`

#### **2.4 Bidirectional Iterator (双向迭代器)**

- **特性**：支持前进和后退操作，可以读写。
- **用途**：适合需要双向遍历的容器。
- **示例**：`std::list::iterator`, `std::set::iterator`

#### **2.5 Random Access Iterator (随机访问迭代器)**

- **特性**：支持常数时间内的随机访问（如 `it + n`），以及前后移动。
- **用途**：用于需要快速访问任意位置的场景。
- **示例**：`std::vector::iterator`, `std::deque::iterator`, 原生指针也是一种随机访问迭代器。


## 3.Invalidation of Iterators
迭代器失效指的是迭代器指向的容器发生变化后，迭代器不再有效，使用它可能导致未定义行为。不同容器对迭代器失效的情况有所不同：

#### **3.1 常见的迭代器失效情况**

- **插入元素**：在某些容器中，插入元素后，所有或部分迭代器会失效。
  
    - **`std::vector`**：如果插入元素导致重新分配内存，则所有迭代器失效；否则只有插入位置之后的迭代器失效。
    - **`std::deque`** 和 **`std::string`**：类似 `std::vector`，内存重新分配后，所有迭代器失效。
    - **`std::list`** 和 **`std::set`**：插入不会使任何迭代器失效。
- **删除元素**：从容器中删除元素后，可能会导致迭代器失效。
  
    - **`std::vector`**：删除操作会使被删除元素之后的迭代器失效。
    - **`std::list`**：删除单个元素只会使指向该元素的迭代器失效，其他迭代器保持有效。
    - **`std::map` 和 `std::set`**：同样，只会使指向被删除元素的迭代器失效。
- **内存重新分配**：如 `std::vector` 扩容或缩容时，重新分配内存会导致所有迭代器失效。
  
- **容器销毁**：如果容器被销毁，指向它的所有迭代器都会失效。


# 6.Else
## 1.set无法使用sort()
其实，**`std::set`** 在 C++ 中是 **有序的** 容器，而不是无序的。`std::set` 底层通常实现为一种平衡二叉搜索树（例如红黑树），这意味着 **`set` 会自动根据元素的排序规则（默认是从小到大）存储元素**。因此，`std::set` 中的元素总是保持排序状态的，不需要也不支持像 `vector` 那样的手动 `sort()` 操作。
### 关键点：

- **`std::set` 自动排序**：`std::set` 自动根据元素的值进行排序，默认是升序（你可以通过传递自定义比较函数更改排序方式）。
  
- **无重复元素**：`std::set` 中不能有重复元素，如果你插入重复的元素，`set` 会忽略它。

- **底层结构**：`std::set` 通常使用红黑树等平衡二叉搜索树实现，这保证了插入、删除、查找等操作的时间复杂度为 O(log n)。

### 示例：

```cpp
#include <iostream>
#include <set>

int main() {
    std::set<int> mySet = {5, 2, 8, 1, 3};
    
    // 元素会自动按从小到大的顺序排序
    for (const auto& elem : mySet) {
        std::cout << elem << " ";  // 输出: 1 2 3 5 8
    }

    return 0;
}
```

在这个例子中，虽然我们按 {5, 2, 8, 1, 3} 的顺序插入元素，但当我们遍历 `set` 时，元素已经自动按升序排列：1 2 3 5 8。

### 为什么 `std::set` 不能使用 `sort()`？
- **不需要 `sort()`**：由于 `set` 是有序的，每次插入都会保持排序状态，因此没有必要使用 `sort()`，因为数据结构本身已经在维护排序。
- **没有随机访问迭代器**：`std::set` 不支持随机访问迭代器，而排序算法（如 `std::sort`）需要随机访问迭代器来高效地对元素进行排序。这也是 `std::set` 不能直接使用 `sort()` 的一个原因。

### 对比无序容器：`std::unordered_set`

`std::unordered_set` 是基于哈希表实现的，无序存储元素，插入元素的顺序是无法保证的。与 `std::set` 相比，它的查找和插入复杂度为 O(1)，但元素之间没有任何顺序关系。

```cpp
#include <iostream>
#include <unordered_set>

int main() {
    std::unordered_set<int> myUnorderedSet = {5, 2, 8, 1, 3};
    
    for (const auto& elem : myUnorderedSet) {
        std::cout << elem << " ";  // 输出的顺序无法保证，可能是: 5 2 8 1 3
    }

    return 0;
}
```

在这个例子中，`unordered_set` 没有顺序保证。

### 总结：
- **`std::set` 是有序的**，内部自动排序，底层实现通常是平衡二叉树。
- 不能对 `set` 进行 `sort()`，因为它本身已经自动排序。
- 如果需要无序的集合，可以使用 `std::unordered_set`，其底层是基于哈希表的实现。
## lower_bound（）
主要是第一次见了解即可
在 C++ 中，`std::lower_bound()` 是一个非常有用的算法函数，用于在 **有序区间** 中查找不小于给定值的第一个位置（即**第一个大于或等于目标值的位置**）。它是二分查找算法的一个实现，因此它的时间复杂度为 **O(log n)**，适用于有序容器或数组。

### 语法

```cpp
std::lower_bound(start, end, value);
```

- **start**：迭代器，指向区间的起始位置（包含）。
- **end**：迭代器，指向区间的末尾位置（不包含）。
- **value**：要查找的目标值。

### 返回值

- 返回一个迭代器，指向 **第一个不小于 `value` 的元素**。
- 如果没有找到不小于 `value` 的元素，返回 `end`（即最后一个元素之后的位置）。

### 使用条件

- 区间 `[start, end)` 必须是 **已排序的**。如果区间未排序，结果未定义。

### 示例

以下是一些 `std::lower_bound()` 的用法示例：

#### 例子 1：在有序数组中查找

```cpp
#include <iostream>
#include <vector>
#include <algorithm>  // 包含 std::lower_bound

int main() {
    std::vector<int> vec = {1, 3, 5, 7, 9};

    int value = 6;
    auto it = std::lower_bound(vec.begin(), vec.end(), value);

    if (it != vec.end()) {
        std::cout << "First element not less than " << value << " is " << *it << std::endl;
    } else {
        std::cout << "All elements are less than " << value << std::endl;
    }

    return 0;
}
```

**输出**：
```
First element not less than 6 is 7
```

#### 例子 2：查找存在的值

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> vec = {1, 3, 5, 7, 9};

    int value = 5;
    auto it = std::lower_bound(vec.begin(), vec.end(), value);

    if (it != vec.end()) {
        std::cout << "First element not less than " << value << " is " << *it << std::endl;
    } else {
        std::cout << "All elements are less than " << value << std::endl;
    }

    return 0;
}
```

**输出**：
```
First element not less than 5 is 5
```

在这个例子中，`5` 正好在数组中，因此 `lower_bound()` 返回指向 `5` 的迭代器。

#### 例子 3：使用自定义比较函数

对于复杂的数据结构，可以使用自定义的比较函数作为第四个参数。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

struct Person {
    int age;
    std::string name;
};

// 比较函数：根据 age 进行比较
bool compare(const Person &p1, const Person &p2) {
    return p1.age < p2.age;
}

int main() {
    std::vector<Person> people = {{30, "Alice"}, {40, "Bob"}, {50, "Charlie"}};

    Person target = {45, ""};  // 我们查找第一个年龄不小于 45 的人

    auto it = std::lower_bound(people.begin(), people.end(), target, compare);

    if (it != people.end()) {
        std::cout << "First person not younger than 45 is " << it->name << std::endl;
    } else {
        std::cout << "Everyone is younger than 45." << std::endl;
    }

    return 0;
}
```

**输出**：
```
First person not younger than 45 is Charlie
```


## 其他遍历方式
在 C++ 中，范围 `for` 循环（`range-based for loop`）提供了一种简洁的方式来遍历容器，如 `std::vector` 和 `std::map`。你提到的两种形式分别是遍历 `std::vector` 和 `std::map` 的方法。让我们来详细说明：

### 1. **遍历 `std::vector`**

```cpp
std::vector<int> vec = {1, 2, 3, 4, 5};

for (auto elem : vec) {
    std::cout << elem << " ";
}
```

- **解释**：该循环会遍历 `vec` 中的每个元素，并通过 `elem` 变量访问元素。
- **注意**：`auto elem` 默认是值传递，如果要通过引用访问元素，可以使用 `auto& elem`。

### 2. **遍历 `std::map`**

```cpp
std::map<int, std::string> myMap = {{1, "apple"}, {2, "banana"}, {3, "cherry"}};

for (auto [key, value] : myMap) {
    std::cout << key << ": " << value << std::endl;
}
```

- **解释**：`std::map` 的元素是 `std::pair`，包含键和值。使用结构化绑定语法 `auto [key, value]`，可以直接解构每个 `pair`，从而分别访问 `key` 和 `value`。
- **注意**：同样，`auto [key, value]` 默认是值传递。如果需要通过引用访问，可以使用 `auto& [key, value]`，以避免不必要的拷贝。

### 完整示例：

```cpp
#include <iostream>
#include <vector>
#include <map>

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5};
    std::map<int, std::string> myMap = {{1, "apple"}, {2, "banana"}, {3, "cherry"}};

    // 遍历 vector
    for (auto elem : vec) {
        std::cout << elem << " ";
    }
    std::cout << std::endl;

    // 遍历 map
    for (auto [key, value] : myMap) {
        std::cout << key << ": " << value << std::endl;
    }

    return 0;
}
```

### 总结：
- 对于 `std::vector`，可以直接使用 `for (auto elem : vec)` 进行遍历。
- 对于 `std::map`，使用结构化绑定 `for (auto [key, value] : map)` 来遍历键值对。

通过引用访问元素可以提升性能，尤其是在处理大型数据结构时。

>ok,这基本是第3节课的所有内容了，基本没多少，很多都是基础内容，笔者这里也是用chatgpt直接生成了一些内容，下一次就要看模板了，刚把累！