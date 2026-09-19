---
title: CMU15-445:Lecture 1
date: 2024-09-29 11:06:47
tags:
  - Cpp
  - DB
categories:
  - CMU15-445
---
>CMU15-445是卡内基梅隆大学的一门神级课程，由多名教授，经过历年的时间打造出的。这是一门数据库系统入门的课程，笔者所看的课程是23年fall的，是由Andy老师主讲的，每节课开始都由DJ，属于是传统了。而前3节课听下来，其中第一次课程由于学校的麦克风问题，导致音频有些地方没有录上音，于是我去看了[中文版的讲解](https://www.bilibili.com/video/BV1bQ4y1Y7iT/?spm_id_from=333.999.0.0)，并且在讲到第三节存储的时候回到英文课程中。
>而这门课程入门前还是需要一些数据库系统和SQL语法基础的，正好笔者这学期在学数据库系统概念这门课程，算是相辅相成了。话不多说，直接开始第一章。

# DATA BASE
在学习数据库系统时，我们要搞清楚什么是数据库，什么是数据库系统，还有我们经常说的MySQL到底是什么东西。这些问题都会一一解决，首先我们先来看什么是数据库。

>Organized collection of inter-related data that models some aspect of the real-world. Databases are the core component of most computer applications.

上面的话是课程ppt的原话，意思是：数据库（**databases**）是由相互关联的数据组成的有组织的集合，它通常用于对现实世界的某些方面进行建模或表示。换句话说，数据库用来存储和管理与实际问题或情况有关的数据，比如一家公司客户信息、销售记录等。由于数据库能够系统地存储、检索和管理大量数据，它们成为了大多数计算机应用程序的核心组件。

下面，我们就举一个简单的数据库的例子：
我们创建一个模拟数字音乐商店的数据库，以跟踪艺术家和专辑。我们需要存储一下的一些信息：
- 艺术家的信息
- 艺术家什么时候发布的专辑
我们如何存储呢？Store our database as comma-separated value (CSV) files.

(CSV) files that we manage in our own code.
- Use a separate file per entity（每一个实体使用一个单独的文件，例如：Artist 和 Album）
- The application has to parse the files each time they want to read/update records.（应用程序需要在读取或者更新记录时解析这些文件）
![Clip_2024-09-29_11-50-46.png](https://gitee.com/salianbooth/picture/raw/master/202409291150699.png)
Example: Get the year that GZA went solo.我们可以写一个 python 程序，遍历文件中的所有行，通过 parse 函数将行记录解析成数据，然后判断数组的第一个值是否等于 GZA，相等则返回第二个值。
![Clip_2024-09-29_11-51-33.png](https://gitee.com/salianbooth/picture/raw/master/202409291151810.png)

通过这种方式查找数据会存在一些问题，这些问题也是我们想要构建一个数据库管理系统的动机。具体问题如下：
1. **DATA INTEGRITY（数据完整性）**
   
    - How do we ensure that the artist is the same for each album entry? 我们如何保证 artist 艺术家信息和 album 专辑中的艺术家信息相同？例如：Ice Cube 拼写错误，或者 Ice Cube 修改了自己的名字，album 表如何保证数据的一致？数据库中使用外键保证？
    
    - What if somebody overwrites the album year with an invalid string? 我们该如何保证对不同类型数据的存储是有效的，例如 year 输入了非法值
      
    - How do we store that there are multiple artists on an album? 如果一个专辑是多个艺术家创作的，我们该如何存储？
    
2. **IMPLEMENTATION（实现）**
   
    - How do you find a particular record? 如何去查找一条具体的记录？如果有 10 亿条数据，for 循环的方式就无法高效处理。
      
    - What if we now want to create a new application that uses the same database? 如何实现数据库逻辑的复用，示例中是使用 python 实现的数据库查询逻辑，其他语言无法复用。
      
    - What if two threads try to write to the same file at the same time? 如果两个线程同时尝试写入同一个数据文件，该如何处理？如果不进行特殊处理，很可能会出现记录的覆盖，第一个线程修改的内容将会丢失。
    
3. **DURABILITY（持久性）**
   
    - What if the machine crashes while our program is updating a record? 当程序正在更新一条记录时，此时程序或者机器宕机了，记录是更新完了，还是只更新了一半？我该如何推断它的正确状态呢？
      
    - What if we want to replicate the database on multiple machines for high availability? 因为机器的不可靠性，我们会考虑将数据库文件复制到不同的机器上以保证高可用。如果一台机器崩溃了，还可以使用备库提供服务。

# DBMS

基于以上存在的各种问题，我们需要有一个通用的数据库解决方案，也就是数据库管理系统 DBMS。那么什么是数据库管理系统呢？

database management system（数据库管理系统） 简称DBMS，s software that allows applications to store and analyze information in a database.
DBMS是介于软件和数据库之间的，能够使应用能存储和分析数据库里的信息。

A general-purpose DBMS supports the definition, creation, querying, update, and administration of databases in accordance with some data model.（DBMS应该支持的功能）

## Data models
A data model is a collection of concepts for describing the data in a database.
**数据模型**是描述数据库中数据的概念的集合。从本质上讲，它定义了数据在数据库系统中的**存储、管理和操作**方式。

A schema is a description of a particular collection of data, using a given data model.
**模式**是使用给定数据模型对特定数据集合的描述。

- Relational——关系模型，大部分的 DBMS 都采用了关系模型，本课程重点内容。
- Key/Value——NoSQL （由于sql是使用的关系模型，所以其他的模型都称为NoSQL--"Not Only SQL"）
- Graph——NoSQL
- Document——NoSQL
- Column-family——NoSQL
- Array / Matrix——数组和矩阵模型，通常会在机器学习中使用
- Hierarchical——层次数据模型（很古老的模型）
- Network——网络数据模型（很古老的模型）

数据模型的组成要素：数据结构，数据操作，数据的完整性约束。

## Early  DBMSs
Early database applications were difficult to build and maintain on available DBMSs in the 1960s. 
基于当时可用的数据库管理系统（DBMS）开发和维护数据库应用程序是十分困难的。

→ Examples: IDS, IMS, CODASYL 
→ Computers were expensive, humans were cheap. 当时计算机昂贵，而人力成本较低。

Tight coupling between logical and physical layers. 
逻辑层和物理层之间的紧密耦合导致开发复杂。  

Programmers had to (roughly) know what queries the application would execute before they could deploy the database.
程序员在部署数据库之前，必须大致了解应用程序会执行哪些查询。


Ted Codd发现了这个问题，为了避免人们重复地进行编码和重构，Codd saw IBM's developers rewriting database programs every time the database’s schema or layout changed. 
Devised the relational model in 1969.
![Clip_2024-10-03_11-12-07.png](https://gitee.com/salianbooth/picture/raw/master/202410031112542.png)

## RELATIONAL MODEL
The relational model defines a database abstraction based on relations to avoid maintenance overhead.
关系模型定义了一种基于关系的数据库抽象，以避免维护的额外负担。

它有一下的3个关键的**原则** ：
→ 以简单的数据结构（关系）存储数据库。  
→ 物理存储由数据库管理系统（DBMS）实现决定。  
→ 通过高级语言访问数据，DBMS会自动确定最佳执行策略。

**三要素**
- **Structure（关系结构）**: The definition of relations and their contents. 关系及其内容的定义。
- **Integrity（数据完整性约束）**: Ensure the database’s contents satisfy constraints. 保证数据库内容满足约束条件。
- **Manipulation（操纵）**: How to access and modify a database’s contents. 如何访问和修改数据库的内容。
### 相关术语
- **relation 关系** is unordered set that contain the relationship of attributes that represent entities. 对应一张表格。
- **tuple 元组**is a set of attribute values (also known as its domain) in the relation.对应表格中的一行。
	→ Values are (normally) atomic/scalar. 
	→ The special value NULL is a member of every domain (if allowed).
	![Clip_2024-10-03_11-28-26.png](https://gitee.com/salianbooth/picture/raw/master/202410031128940.png)

- **primary key 主键**是唯一一个标记一个元组的属性（例如身份证）。如果没用定义主键，一些DBMS会自动船舰一个内部的主键。
	![Clip_2024-10-03_11-30-59.png](https://gitee.com/salianbooth/picture/raw/master/202410031131281.png)

- **foreign key 外键** 是指将一个元组中的属性映射到另外一个元组的属性上，可以用来维护不同关系之间的数据一致性。
	比如说，我有三张表分别是，学生信息表，课程信息表，还有学生选课信息表，其中学生选课信息表中的学号和课程号就分别来自其他的2个表，这样就称学号和课程号是SC表的外键。
	![Clip_2024-10-03_11-36-32.png](https://gitee.com/salianbooth/picture/raw/master/202410031136967.png)

# DML

**Data Manipulation Languages (DML)**，即**数据操作语言**，是用于在数据库中查询、插入、更新和删除数据的一类语言。在关系数据库管理系统中，DML是一部分SQL（结构化查询语言），专注于数据的操作和管理。

以下是DML的主要功能：

1. **查询数据 (SELECT)**：用于从数据库中检索数据。
    
    - 示例：`SELECT * FROM Employees;`
        - 该语句从表 `Employees` 中检索所有的记录。
2. **插入数据 (INSERT)**：用于向数据库的表中插入新数据。
    
    - 示例：`INSERT INTO Employees (Name, Age) VALUES ('John', 30);`
        - 该语句向 `Employees` 表中插入一条新记录，名字为John，年龄为30。
3. **更新数据 (UPDATE)**：用于修改已存在的数据。
    
    - 示例：`UPDATE Employees SET Age = 31 WHERE Name = 'John';`
        - 该语句将 `Employees` 表中名字为John的员工的年龄更新为31。
4. **删除数据 (DELETE)**：用于删除数据库中的数据。
    
    - 示例：`DELETE FROM Employees WHERE Age = 31;`
        - 该语句删除所有年龄为31的员工记录。

## Relational Algebra
Fundamental operations to retrieve and manipulate tuples in a relation. 检索和操作关系中的元组的基本操作。
- Based on set algebra. 基于集合的代数。
Each operator takes one or more relations as its inputs and outputs a new relation. 每个操作符以一个或多个关系作为其输入，并输出一个新的关系。
![Clip_2024-10-05_15-40-58.png](https://gitee.com/salianbooth/picture/raw/master/202410051541991.png)

- **选择 (Select, σ)**: 通过选择谓词筛选元组（行），相当于过滤操作。
    
    - 语法: σpredicate(R)
    - 示例: σa_id='a2'(R) 返回所有 `a_id='a2'` 的行。
- **投影 (Projection, π)**: 只返回指定的属性（列）。
    
    - 语法: ΠA1,A2,…,An(R)
    - 示例: Πb_id-100,a_id(σa_id='a2'(R)) 移除无关列并操作列的值。
- **并集 (Union, ∪)**: 返回两个输入关系中的所有元组。
    
    - 语法: (R ∪ S)
- **交集 (Intersection, ∩)**: 仅返回出现在两个关系中的元组。
    
    - 语法: (R ∩ S)
- **差集 (Difference, −)**: 返回在第一个关系中但不在第二个关系中的元组。
    
    - 语法: (R − S)
- **笛卡尔积 (Product, ×)**: 生成两个关系中所有元组的组合。
    
    - 语法: (R × S)
- **连接 (Join, ⋈)**: 通过共同属性值将两个关系中的元组合并。
    
    - 语法: (R ⋈ S)

这些内容都很简单，无需过多的理解，想要深入了解，可以去b站看[《数据库系统概论》](https://www.bilibili.com/video/BV1W3411y7dw/?spm_id_from=333.999.0.0&vd_source=b2413ecf60f1e8b5a48adcc77ce32005)这门课的讲解。