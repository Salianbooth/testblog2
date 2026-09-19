---
title: CS106L:Lecture 1
date: 2024-09-03 19:32:25
tags:
  - Cpp
categories:
  - CS106L
---
>本系列学习笔记是笔者学习Stanford开设的C++语言课程->CS106L个人学习心得体会，主要介绍了C++的现代特性，在基础语法上并不花时间，所以需要有一定的语言基础。
>本笔记只是对该课程的一个简单的介绍，正如这门课程的第一部分intro，讲述学习本课程能收获什么。
>此外，在课堂上学生们的提问也是各有千秋，不得不佩服他们课堂氛围之好，学生一直有着不同的问题，这是真正的深入思考了本节课的知识，老师也是很热情的回答问题，并且不时暂停课堂内容让学生去讨论。有时我觉得我失去了这样上课思考的能力，上课时一直被灌输知识，而不是去搞懂它。

# Introduction

Stanford cs106L是斯坦福大学讲Standard C++ Programming的一门课，与cs106B/X不同，cs106L专注于c++语言本身，并且讲到了modern c++，涵盖了很多c++11、c++14以及c++17的一些特性。

## 学习目标
![Clip_2024-09-03_19-48-31.png](https://gitee.com/salianbooth/picture/raw/master/202409031948986.png)

## 学习大纲
![Clip_2024-09-03_19-49-54.png](https://gitee.com/salianbooth/picture/raw/master/202409031950730.png)

## 环境配置
在2020年fall的课程中，老师要求的是使用QT Creater来进行运行代码，在第一个作业中也是使用到一个图形库，这在QT中可以直接调用的，但是对于我来说，直接使用了vs code+wsl，跳过了第一个作业，对于QT的部分在将来跟随国内的课程进行简单的学习。
![Clip_2024-09-03_19-52-38.png](https://gitee.com/salianbooth/picture/raw/master/202409031952557.png)


## Design Philosophy of C++

- Allow the programmer full control, responsibility, and choice if they want it.
- Express ideas and intent directly in code. 
- Enforce safety at compile time whenever possible. 
- Do not waste time or space.
- Compartmentalize messy constructs


## Q&A

### Some Code

```C++
// =========================  C++ Way  ==============================

#include <iostream>

int hello_cpp()
{
    std::cout << "Hello, world!" << std::endl;
    return 0;
}

// =========================  C Way  ==============================

#include "stdio.h"
#include "stdlib.h"

int hello_c()
{
    printf("%s", "Hello, world!\n");
    return EXIT_SUCCESS;
}
```


Q1:是什么让顶部的函数成为C++，而不是其他的函数呢？（这确实是一个不错的问题，我们一般学习c++的时候，直接记住它的一些基本语法，比如，cout输出，我们就可以知道这是cpp了）
A1:C++ - isms ---> c++的惯用法，在hellp_cpp( )函数中使用了极具C++特色的结构。

Q2:#include 后面的<> 和 “ ” 是有什么区别吗？（这也是我一直想去思考的一个问题，为什么c和c++ 导入库时不一样，而且c是.h文件，而c++却不用）
A1：其实是这样的，导入库的方式有两种，一直是<>，而另一种是“  .h”，在C++中我们用<>就是直接导入标准库中的部分库，而对于"  .h"，适用于我们自己写的头文件。


