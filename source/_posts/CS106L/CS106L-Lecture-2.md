---
title: CS106L:Lecture 2
date: 2024-09-03 20:09:52
tags:
  - Cpp
categories:
  - CS106L
---
# Stream
>stream流这一部分在[C++Primer](https://salianbooth.github.io/2024/08/02/C-Primer-learning/)这本书里面我都是随便一看就过去了，没想到这门课竟然开始就在讲，那还是简单看一下吧。

## 1.Overview
我们在编程中会经常想让我们的程序和外部的设备沟通，比方说控制台&键盘，文件等等。

接下来，我们就以console（控制台为例），我们怎么样才能将一个Data 对象打印在console上呢？
![Clip_2024-09-04_09-33-48.png](https://gitee.com/salianbooth/picture/raw/master/202409040933571.png)
- 开始，我们应该会把这个Data对象转换成一个string
	![Clip_2024-09-04_09-34-56.png](https://gitee.com/salianbooth/picture/raw/master/202409040934451.png)
- 然后，将这个string写入console中。
	![Clip_2024-09-04_09-35-36.png](https://gitee.com/salianbooth/picture/raw/master/202409040935095.png)

那么，我们如何从文件中读取到一个双精度的值呢？
- 我们可以先从File中读出character并且把它转换成一个string
- 然后，再将string转换成double
	![Clip_2024-09-04_09-37-01.png](https://gitee.com/salianbooth/picture/raw/master/202409040937784.png)


总的来说，有2个主要的步骤：

**一**：我们需要以字符串形式从数据源获取或发送数据。

**二**：我们需要在程序中的数据和其字符串表示之间进行转换。

![Clip_2024-09-04_09-44-19.png](https://gitee.com/salianbooth/picture/raw/master/202409040944656.png)

这样就引出了Stream->Streams 为我们提供了一个与外部输入交互的统一接口。

你可以将流想象成一个自动与外部数据源交互的字符缓冲区。

![Clip_2024-09-04_09-45-46.png](https://gitee.com/salianbooth/picture/raw/master/202409040945216.png)


## 2.Stringstream

`stringstream` is a type of stream not connected to any external device. Instead, it operates on a string, allowing you to extract data from or insert data into a string as if it were a stream.

Examples include using `istringstream` for input and `ostringstream` for output.

### 2.1stringstream constructors

```C
istringstream iss(“Initial”); 
ostringstream oss(“Initial”); 

ostringstream oss(“Initial“, stringstream::ate); 
istringstream iss(“Initial“, stringstream::bin);
```

我们可以看到这2组constructors是有所不同的，为什么后面的加上了stringstream::ate或者stringstream::bin呢？
其实是`stringstream`（以及其他类型的流，如`ifstream`, `ofstream`等）内部是使用指针（通常称为流指针）来管理数据的读取和写入位置。
而对于我们上面的那一组：
1. **`ostringstream oss("Initial", stringstream::ate);`**
   
    - **Type**: Output string stream (`ostringstream`)
    - **Mode**: Text mode with the `ate` flag
    - **Behavior**: Creates an output string stream with the initial content `"Initial"`. The `ate` flag moves the write pointer to the end of the initial content, so any new data written to the stream will be appended to the end of `"Initial"`  --> 指针是移动到initial的末尾，这样新添加的数据会加到 `“Initial”`的后面，而不会覆盖了
2. **`istringstream iss("Initial", stringstream::bin);`**
   
    - **Type**: Input string stream (`istringstream`)
    - **Mode**: Binary mode (`bin`)
    - **Behavior**: Creates an input string stream with the initial content `"Initial"`, but in binary mode. This means the stream will treat the content as raw binary data, without interpreting any control characters like newlines. -->采用二进制模式。这意味着流会将内容视为原始二进制数据，而不会解释任何控制字符，如换行符。

### 2.2whitespace separated token

A **whitespace-separated token** refers to a segment of text in a string that is separated by one or more whitespace characters, such as spaces, tabs, or newlines. When processing input, streams in C++ typically read data up until they encounter a whitespace, which marks the boundary of a token.
**以空格分隔的标记**是指字符串中由一个或多个空格字符（如空格、制表符或换行符）分隔的文本段。在处理输入时，C++ 中的流通常会读取数据，直到遇到空格，这标志着标记标记的边界

For example, given the string:

```C
"16.9 Ounces \t -38271"
```

This string contains three whitespace-separated tokens:
- `"16.9"`
- `"Ounces"`
- `"-38271"`

When you read from a stream using the `>>` operator, it extracts data until it hits a whitespace, thus separating the string into these tokens. The type of data you're reading (e.g., int, string, double) also affects how the token is interpreted.  （你所读取的类型也会对这个有所影响）

For example:

```cpp
std::istringstream iss("Hello world 42");
std::string word;
int number;

iss >> word;  // Reads "Hello"
iss >> word;  // Reads "world"
iss >> number;  // Reads 42
```

In this case:
- `"Hello"` is the first token, separated from the next token `"world"` by a space.
- `"world"` is the second token, separated from `"42"` by another space.
- `42` is the third token.

### 2.3Behavior in Streams

- **Textual Types (`std::string`, `char`)**:
  - The stream will read a sequence of non-whitespace characters until it encounters a whitespace character, at which point it stops and considers that sequence a token.
  
- **Numerical Types (`int`, `double`)**:
  - The stream will attempt to read a valid number. It stops reading either when it encounters a whitespace character or when it hits a character that is invalid for the type (e.g., a letter in the middle of a number).
  

For instance:

```cpp
std::istringstream iss("123abc 456");
int num;
iss >> num;  // Reads "123", stops at "a" because it's not valid for an int.
std::cout << num;  // Outputs: 123
```

Here, the stream stops reading as soon as it hits the letter `'a'`, which is not valid for an integer, resulting in `123` being the token that is extracted. （int类型，在读到'a'这个字符后就停止了）

#### Key Points:
- **Whitespace as a Delimiter**: The stream uses whitespace to determine where one token ends and another begins.
- **Tokenization**: The process of splitting the input based on whitespace is often referred to as tokenization.
- **Type-Dependent Behavior**: The stream reads input differently depending on the expected type (`int`, `float`, `std::string`), stopping at the first invalid character or whitespace.




### 2.4stringstream positioning functions （了解？）
`std::stringstream` 类允许你将字符串当作一个流来处理，其中包括读取和写入操作。为了更好地控制流中的数据处理，C++ 提供了一些函数用于管理 `stringstream` 的内部位置指针。这些函数也同样适用于其他流对象，如 `std::ifstream` 和 `std::ofstream`。以下是常用的定位函数：

#### 1. `tellg()`
- **作用**：获取当前读取位置的索引。
- **返回值**：流中的当前位置，以字节数（position）的形式返回。如果操作失败，则返回 `-1`。
- **用法**：
  ```cpp
  std::stringstream ss("Hello, world!");
  std::streampos pos = ss.tellg();  // 获取当前位置
  ```

#### 2. `tellp()`
- **作用**：获取当前写入位置的索引。
- **返回值**：流中的当前位置，以字节数（position）的形式返回。如果操作失败，则返回 `-1`。
- **用法**：
  ```cpp
  std::stringstream ss;
  ss << "Hello";
  std::streampos pos = ss.tellp();  // 获取当前位置
  ```

#### 3. `seekg(pos)`
- **作用**：设置流的读取位置为 `pos` 指定的位置。
- **参数**：`pos` 是目标位置（以字节数表示）。
- **用法**：
  ```cpp
  std::stringstream ss("Hello, world!");
  ss.seekg(7);  // 设置读取位置到第 7 个字节
  char ch;
  ss >> ch;  // 现在 ch == 'w'
  ```

#### 4. `seekp(pos)`
- **作用**：设置流的写入位置为 `pos` 指定的位置。
- **参数**：`pos` 是目标位置（以字节数表示）。
- **用法**：
  ```cpp
  std::stringstream ss;
  ss << "Hello";
  ss.seekp(2);  // 设置写入位置到第 2 个字节
  ss << "XX";  // 原来字符串中的第三个和第四个字符被替换成 'XX'
  ```


### Q&A
- Q1: What exactly does >> do?
	- A1：The `>>` operator is known as the **extraction operator** in C++. It is used with input streams (like `std::cin`, `std::ifstream`, `std::istringstream`) to extract data from the stream into variables. （读取数据，直到第一个空格）
- Q2:Why can you chain << and >>?
	- A2:The >> and << operators return a reference to the stream itself, so in each instance the stream is the left-hand operand.-----因为每次操作后，流对象仍然作为操作符的左侧操作数继续使用，这使得链式操作成为可能。


## 3.State bits
这一部分之前在[C++Primer](https://salianbooth.github.io/2024/08/02/C-Primer-learning/)上有了解过，但是也是一笔带过，这里再进行一遍复习。
![Clip_2024-09-04_10-58-21.png](https://gitee.com/salianbooth/picture/raw/master/202409041058925.png)


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



![Clip_2024-09-04_10-58-36.png](https://gitee.com/salianbooth/picture/raw/master/202409041058252.png)

![Clip_2024-09-04_10-58-54.png](https://gitee.com/salianbooth/picture/raw/master/202409041058579.png)

  



## 4.Input/Output Stream

### 4.1 Flush
1. **缓冲流：字符先存储在一个中间缓冲区中，然后才会被移动到外部目标。**
   
    - 当你在编写程序时，输出的数据（例如字符或字符串）并不会立刻被发送到终端、文件或其他外部设备。相反，这些数据会先存储在一个临时的内存区域中，这个区域称为**缓冲区**。缓冲区用于暂时存储数据，直到有足够的数据积累后再一起发送，以提高程序的性能。
2. **为了将字符推送到外部目标，必须“刷新”流。**
   
    - 如果你想立即将缓冲区中的数据发送到外部设备或目标（例如，显示在屏幕上或写入文件中），需要**刷新**流。刷新操作会将缓冲区中的所有数据立刻发送出去，而不是等待缓冲区填满或者程序结束时自动刷新。

**对于cout来说，有一些特殊情况**：
`cout`（标准输出流）通常情况下会使用缓冲区来暂存数据，而不是每次输出操作后都立即刷新到屏幕。这种缓冲机制可以提高输出操作的效率。不过，有几种情况会触发 `cout` 自动刷新缓冲区：

1. **遇到换行符（`\n`）时**：
   - 在大多数实现中，当 `cout` 输出一个换行符（`\n`）时，它会自动刷新缓冲区，将所有已输出的数据立即发送到屏幕。这是为了确保换行之前的内容及时显示。

   ```cpp
   std::cout << "Hello, world!\n";
   ```
   在这个例子中，`Hello, world!` 后的 `\n` 会触发 `cout` 的自动刷新。

2. **使用 `std::endl` 时**：
   - `std::endl` 是一个特殊的操作符，它不仅会插入一个换行符，还会强制刷新 `cout` 的缓冲区。
   ```cpp
   std::cout << "Hello, world!" << std::endl;
```

   在这个例子中，`std::endl` 不仅会输出换行，还会立即刷新缓冲区。

3. **程序正常结束时**：
   - 当程序正常结束或 `cout` 被关闭时，标准库会自动刷新 `cout` 的缓冲区，将所有未输出的数据发送到屏幕。

4. **当 `cin` 被使用时**：
   - 如前所述，当 `cin` 开始读取输入时，标准输出缓冲区也会自动刷新，以确保所有输出的内容在输入提示之前已经显示给用户。

### Q&A
1. **When does the program prompt the user for input?  
    程序何时提示用户输入？**
    
    - The program prompts the user for input whenever an `input operation` like `cin >>` is encountered, and it requires data to be read from the standard input. If there is no data in the input buffer or the program needs more data, it will "hang" and wait for the user to provide input.  
        每当遇到 `cin >>` 之类的`输入操作`时，程序都会提示用户输入，并且它要求从标准输入中读取数据。如果 input buffer 中没有数据或程序需要更多数据，它将 “挂起” 并等待用户提供 input。
    
1. **Why does the `cout` operation not immediately print the output onto the console? When is the output printed?  
    为什么 `cout` 操作没有立即将输出打印到控制台上？何时打印输出？**
    
    - `cout` is buffered, meaning it stores output data in a buffer and doesn't necessarily print it immediately to the console. The output is printed when:  
        `cout` 是缓冲的，这意味着它将输出数据存储在缓冲区中，并且不必立即将其打印到控制台。在以下情况下打印输出：
        - The buffer is full.
        - A newline (`\n`) is encountered, which often triggers a flush.  
            遇到换行符 （`\n`），这通常会触发 flush。
        - `std::endl` is used, which inserts a newline and forces a flush.  
            `std：：endl` 的 intent 插入换行符并强制刷新。
        - The program finishes or exits, which triggers a flush.
        - `std::flush` is explicitly called to manually flush the buffer.  
            显式调用 `std：：flush` 来手动刷新缓冲区。
3. **Does the position pointer skip whitespace before the token or after the token with each `>>` operation?  
    位置指针在每次 `>>` 操作时是否会跳过令牌之前或之后的空格？**
    
    - The position pointer skips whitespace **before** the token with each `>>` operation. This means that when extracting a value using `cin >>`, any leading spaces, newlines, or tabs are ignored before reading the actual token.  
        位置指针在每次 `>>` 操作中跳过标记**前**的空格。这意味着，使用 `cin >>` 提取值时，在读取实际标记之前，将忽略任何前导空格、换行符或制表符。
4. **Does the position pointer always read up to a whitespace? If not, provide a counterexample.  
    位置指针是否总是读取到空格？如果没有，请提供一个反例。**
    
    - The position pointer generally reads up to a whitespace, which is why it stops reading tokens like integers, floats, or strings when it encounters spaces, newlines, or tabs.  
        位置指针通常读取到空格，这就是为什么当遇到空格、换行符或制表符时，它会停止读取整数、浮点数或字符串等标记。
    - **Counterexample**: Consider the input `"86.2"`. If you extract an `int` from this input:
        
        `int number; std::cin >> number;`
        
        The `int` extraction reads up to the first non-numeric character, stopping at the decimal point, so `number` would be `86`. The position pointer will then be positioned at the decimal point, not a whitespace.  
        **反例**：考虑输入 `“86.2”。`如果从此输入中提取 `int`：`int` 提取最多读取第一个非数字字符，在小数点后停止，因此`数字`将为 `86`。然后，位置指针将位于小数点，而不是空格处。

>ok，以上基本就是这一节课的基本内容了，虽然不多，但是有很多的细节去发掘，仅仅是整理这些资料就花了我1h的时间，只能说有一点收获吧。