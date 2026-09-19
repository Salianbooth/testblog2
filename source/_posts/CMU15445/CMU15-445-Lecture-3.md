---
title: CMU15-445:Lecture 3
date: 2024-10-08 15:29:44
tags:
  - DB
  - Cpp
categories:
  - CMU15-445
---
>这一次课程主要讲解了Database Storage，数据库从存储层，数据库是如何存储在disk硬盘上的，还有pages layout页面布局这些内容，还是挺多的，半个月前看完了课程，但是一直没有整理笔记，今天又重新快速过了一遍，希望写笔记能写的更好。（写本笔记的时候，在听夜鹿的 [月光] live）

![Clip_2024-10-08_17-00-06.png](https://gitee.com/salianbooth/picture/raw/master/202410081700018.png)
我们将自底层向上学习，先从DISK Manager开始学起。
# Disk-based architecture

The DBMS assumes that the primary storage location of the database is on non-volatile disk. 
数据库管理系统（DBMS）假设数据库的主要存储位置是在非易失性磁盘上。
The DBMS's components manage the movement of data between non-volatile and volatile storage.
DBMS 的各个组件负责管理数据在非易失性存储和易失性存储之间的移动。

## **Storage hierarchy**
这一部分在计算机组成原理学过，这里就不多做赘述，其中要注意的一点是本课程讲述了Volatile易失性和Non-Volatile非易失性这一个特性。
![Clip_2024-10-08_17-01-29.png](https://gitee.com/salianbooth/picture/raw/master/202410081701499.png)

![Clip_2024-10-08_17-01-43.png](https://gitee.com/salianbooth/picture/raw/master/202410081701571.png)

## **Access time**
是指从请求数据到完成访问所需的时间。在计算机系统中，访问不同存储层次的数据的时间差异非常大，影响了整体系统性能。下面是常见的存储层次的访问时间：
![Clip_2024-10-08_17-04-00.png](https://gitee.com/salianbooth/picture/raw/master/202410081704942.png)

## **Sequential Access** VS **Random Access**
- **随机访问（Random Access）**：在非易失性存储（例如硬盘）上进行随机访问通常比顺序访问要慢得多。这是因为硬盘的机械性质决定了读取不同位置的数据需要移动磁头，会产生额外的寻道时间和延迟。
    
- **顺序访问（Sequential Access）**：指的是数据按连续的物理位置依次进行访问，这样的访问模式更高效，因为不需要频繁移动磁头。

DBMS 会尽量优化数据的存储和访问，以最大化顺序访问的使用，原因是这样能提升整体性能：

1. **减少对随机页面的写入次数**：
    - DBMS 通过算法尽量减少对随机页面（散乱分布的页面）的写入，以保证数据被存储在连续的块中。
2. **同时分配多个页面（Extent）**：
    - 当需要分配多个页面时，DBMS 会一次性分配相邻的多个页面，这种方式被称为一个**扩展区（Extent）**。这样可以尽量保证数据的物理连续性，从而提高顺序访问的可能性。

#  **System Design Goals**
在课程中提出了DBMS的设计目标：
- Allow the DBMS to manage databases that exceed the amount of memory available. 
- 允许数据库管理系统（DBMS）处理超出内存容量的数据库。

- Reading/writing to disk is expensive, so it must be managed carefully to avoid large stalls and performance degradation.
- 磁盘读写开销较大，因此需要精细管理。

- Random access on disk is usually much slower than sequential access, so the DBMS will want to maximize sequential access.
- 需要最大化的利用顺序存储。
#  **Disk-Oriented DBMS**
磁盘导向的DBMS中更新数据的基本流程：
在disk硬盘上的database file中存储了我们的pages，其中通过directory page来找到想要的几号pages；将想要的update 的pages加载到buffer pool缓冲池中，燃后execution engine get page#2，然后更新数据，最后将page#2存储回到database file中。
![Clip_2024-10-08_17-15-36.png](https://gitee.com/salianbooth/picture/raw/master/202410081715114.png)

1. 磁盘管理 (Disk Management)：
   - **主要职责**：负责将数据库存储为磁盘上的一个或多个文件。这些文件可能是以专有格式存储的，操作系统通常不了解其内部结构。
   - **工作方式**：磁盘管理器负责在磁盘上组织数据，以页 (pages) 的形式存储。每个页面都有唯一的标识符，数据和元数据都存储在这些页中。
2. 缓冲池管理 (Buffer Pool Management)：
   - **主要职责**：将从磁盘读取的页面暂时存储在内存中（缓冲池）以提高性能。数据库系统不能在每次需要数据时都直接访问磁盘，因为这会导致较大的性能开销。
   - **工作方式**：缓冲池管理器从磁盘中获取页面，将其缓存到内存中以便快速访问。它需要决定哪些页面应该留在缓冲池中，哪些页面可以被替换掉。


- **磁盘管理**决定了数据如何在磁盘上存储和组织，而**缓冲池管理**则负责将这些存储在磁盘上的数据页加载到内存中，并优化页面的读写操作以减少对磁盘的频繁访问。
- **缓冲池管理依赖于磁盘管理**：缓冲池中的页面需要由磁盘管理器提供，当页面不再缓存在内存中时，缓冲池管理器会通过磁盘管理器从磁盘上获取数据。
- **二者目标一致**：共同减少昂贵的磁盘I/O操作。缓冲池管理通过将页面保留在内存中来减少磁盘访问，而磁盘管理通过优化文件存储和读取顺序来减少随机访问的性能开销。
## Why not use OS

The DBMS can use memory mapping (mmap) to store the contents of a file into the address space of a program.
DBMS 可以使用内存映射（mmap）将文件的内容存储到程序的地址空间中。

OS is responsible for moving file pages in and out of memory, so the DBMS doesn’t need to worry about it.
操作系统负责将文件页面在内存和磁盘之间进行调度，因此 DBMS 不需要对此进行关心。

我们可以通过下面的流程了解os对文件进行调度：
1. **mmap 映射**：当程序使用 `mmap` 进行文件映射时，操作系统会将文件的某些部分（通常以页为单位）映射到程序的**虚拟地址空间**中。但此时，并没有实际将文件的数据加载到**物理内存**中。
2. **程序访问虚拟内存页面**：当程序访问映射到虚拟地址空间中的某个页面（例如 `page1`），操作系统会检查该页面是否已经在**物理内存**中。
    - 如果页面已经在物理内存中，程序就可以直接访问该页面的内容。
    - 如果页面不在物理内存中（通常这是第一次访问时的情况），就会触发**缺页中断**。
3. **缺页中断和页面加载**：缺页中断（Page Fault）发生时，操作系统会负责从磁盘上的文件中读取该页面的数据，并将其加载到**物理内存**中。加载完成后，虚拟内存中的这个页面就会和物理内存中的实际页面关联起来。此后，程序访问该页面时，数据就直接从物理内存中读取。
![Clip_2024-10-08_17-26-49.png](https://gitee.com/salianbooth/picture/raw/master/202410081728597.png)
但是当物理内存满了后会怎么样呢？mmap遇到页面错误，这将阻止进程，如果进程锁定了其他元组就麻烦了。
![Clip_2024-10-08_17-30-06.png](https://gitee.com/salianbooth/picture/raw/master/202410081730683.png)

### Memory Mapped I/O Problems

**Problem #1: Transaction Safety** 
→ OS can flush dirty pages at any time.
- 操作系统可以随时刷新脏页。

**Problem #2: I/O Stalls** 
→ DBMS doesn't know which pages are in memory. The OS will stall a thread on page fault. 
- 数据库管理系统（DBMS）不知道哪些页面在内存中。发生页面错误时，操作系统会导致线程停顿。

**Problem #3: Error Handling** 
→ Difficult to validate pages. Any access can cause a SIGBUS that the DBMS must handle. 
- 难以验证页面的有效性。任何访问都可能导致 `SIGBUS` 错误，DBMS 必须处理这些错误。

**Problem #4: Performance Issues** 
→ OS data structure contention. TLB shootdowns
- 操作系统的数据结构争用。TLB（翻译后备缓冲区）失效。

# 磁盘管理模块的核心问题

DBMS 的磁盘管理模块主要解决两个问题：
1. （本节）如何使用磁盘文件来表示数据库的数据（元数据、索引、数据表等）
2. 如何管理数据在内存与磁盘之间的移动.

# File Storage
The DBMS stores a database as one or more files on disk typically in a proprietary format.
DBMS以一种专门的方式将DB存储在disk上。
-> The OS doesn't know anything about the contents of these files.

### **Storage Manager**
The storage manager is responsible for maintaining a database's files.
→ Some do their own scheduling for reads and writes to improve spatial and temporal locality of ***pages*.**

It organizes the files as a collection of ***pages.*** 
→ Tracks data read/written to pages.    ---跟踪读取/写入页面的数据。
→ Tracks the available space.                ---跟踪可用空间。
### **Page**
DBMS中通常把文件切分成大小固定的一块数据->page页。每个page页面能存储元组，meta-data元数据，indexes，log等等，大多数的system不会mix page types（每种数据都有专门的page存储）。每个 page 带着一个唯一的 id，DBMS 使用一个 indirection layer 将 page id 与数据实际存储的物理位置关联起来。

A page is a fixed-size block of data.
→ It can contain tuples, meta-data, indexes, log records… 
→ Most systems do not mix page types. 
→ Some systems require a page to be self-contained.

Each page is given a unique identifier. → The DBMS uses an indirection layer to map page IDs to physical locations.

关于pages，有三个概念需要区分：
- Hardware Page：通常大小为 4KB
- OS Page: 通常大小为 4KB
- Database Page：(1-16KB)

![Clip_2024-10-08_17-49-55.png](https://gitee.com/salianbooth/picture/raw/master/202410081749759.png)

不同 DBMS 管理 pages 的方式不同，主要分为以下几种：
- Heap File Organization
- Sequential/Sorted File Organization
- Hashing File Organization
其中最常用的就是Heap了，下面我们着重讲解heap的管理。
### **Heap** **File**
A heap file is an unordered collection of pages with tuples that are stored in random order.
**堆文件（Heap File）** 是一个无序的页面集合，其中存储了以随机顺序排列的元组（tuples）。
- 创建 / 获取 / 写入 / 删除页面
- 还必须支持遍历所有页面的操作。
pages 管理模块需要记录哪些 pages 已经被使用，而哪些 pages 尚未被使用。那么具体如何来记录和管理呢？这里我们主要讲到了Page Directory。

The DBMS maintains special pages that tracks the location of data pages in the database files. 
数据库管理系统（DBMS）维护特殊的页面，用于跟踪数据库文件中数据页面的位置。  
→ Must make sure that the directory pages are in sync with the data pages.
→ 必须确保目录页面与数据页面保持同步。

The directory also records meta-data  about available space:  
目录还记录关于可用空间的元数据：  
→ The number of free slots per page.  → 每个页面的空闲槽数量。  
→ List of free / empty pages.                → 空闲/空页面的列表。

![Clip_2024-10-08_17-55-43.png](https://gitee.com/salianbooth/picture/raw/master/202410081755899.png)

# Page Layout
## page header
- 每个页面都有一个头部，用于存储页面内容的元数据。
- 元数据包括：页面大小、校验和、数据库版本、事务可见性、压缩/编码元数据、架构信息等。
![Clip_2024-10-08_23-08-54.png](https://gitee.com/salianbooth/picture/raw/master/202410082308890.png)


## Page Layout
For any page storage architecture, we now need to decide how to organize the data inside of the page. 对于任何页面存储架构，我们现在需要决定如何在页面内组织数据。  
→ We are still assuming that we are only storing tuples in a row-oriented storage model.
→ 我们仍然假设只在**行存储模型**中存储**元组**。

在页面中布置数据有三种方法：
	Approach #1: Tuple-oriented Storage 
	Approach #2: Log-structured Storage 
	Approach #3: Index-organized Storage
接下来我们分别讲解。

##  **Tuple-oriented Storage（基于元组的存储）**
Strawman Idea: Keep track of the number of tuples in a page and then just append a new tuple to the end.
初步想法：记录页面中的元组数量，然后只需将新元组追加到页面末尾。
![Clip_2024-10-09_15-34-18.png](https://gitee.com/salianbooth/picture/raw/master/202410091534401.png)
但是这样会有几个问题：
-> 如果我们删除了中间某一个元组怎么办？  -----> 一旦出现删除操作就要遍历一遍，寻找空位。
->如果我们有一个变长的对象呢？

为了解决上述的问题，***slotted*** **pages** 就诞生了。

### Slot Array
![Clip_2024-10-09_15-37-23.png](https://gitee.com/salianbooth/picture/raw/master/202410091537653.png)

如上图所示，header中的Slot Array存储每个slot的信息，如大小、位移等。
- 新增记录时：在 slot array 中新增一条记录，记录着改记录的入口地址。slot array 与 data 从 page 的两端向中间生长，二者相遇时，就认为这个 page 已经满了
    
- 删除记录时：假设删除 tuple#3 ，可以将 slot array 中的第三条记录删除，并将 tuple #4 及其以后的数据都都向下移动，填补 tuple #3 的空位。而这些细节对于 page 的使用者来说是透明的
    
- 处理定长和变长 tuple 数据都游刃有余。


## Log Structured Storage
不是通过在page页面上存储元组，而是存储记录了元组变化的log。

The DBMS appends new log entries to an inmemory buffer and then writes out the changes sequentially to disk.
DBMS将新的日志条目追加到内存缓冲区中，然后将这些更改按顺序写入磁盘。这种顺序写入避免了随机I/O。
每条日志条目代表一次元组的 PUT 或 DELETE 操作。如下图所示：
![Clip_2024-10-10_08-40-28.png](https://gitee.com/salianbooth/picture/raw/master/202410100840073.png)


当页面填满时，DBMS 会将其写入磁盘，并开始用记录填充下一个页面。
- 所有磁盘写入都是顺序进行的。
- 磁盘上的页面是不可变的。
 日志记录中还必须包含元组的唯一标识符（ID），如下图：
![Clip_2024-10-10_08-46-42.png](https://gitee.com/salianbooth/picture/raw/master/202410100846762.png)

- 当需要读取特定元组时，DBMS 会从最新的日志开始扫描，找到对应元组ID的最新日志记录。这样确保读取到的是最新版本的数据。
- 为了提高效率，DBMS 可以维护一个索引，将元组ID映射到其最新的日志记录。若日志记录在内存中，直接读取；若记录在磁盘页面上，则从磁盘检索。

### Log Structured Compaction
- 为减少空间浪费，DBMS 会周期性地压缩页面，移除过时或冗余的日志记录。压缩后的页面不再需要保持记录的时间顺序，而是可以根据元组ID排序。
- 这种压缩机制被称为“有序字符串表（Sorted String Tables, SSTables）”，进一步提升查找效率。

![Clip_2024-10-10_08-50-07.png](https://gitee.com/salianbooth/picture/raw/master/202410100850727.png)

将较大的驻磁日志文件合并为较小的文件，移除不必要的记录。
现在有2种压缩的方式：
 - universal compaction  ---> 通用压缩  侧重于处理 **写密集型** 工作负载，适合需要频繁更新或删除记录的场景。
 - level compaction         ---> 分层压缩  最常用的 LSM 压缩策略，适合 **读密集型** 场景。它将数据按层次（Level）进行管理，并定期将低层次的数据合并到高层次。
![Clip_2024-10-10_09-10-35.png](https://gitee.com/salianbooth/picture/raw/master/202410100910085.png)

## Index-oriented Storage
DBMS stores a table's tuples as the value of an index data structure. 
DBMS将一个表的元组存储为索引数据结构的值。  
→ Still use a page layout that looks like a slotted page.
→ 仍然使用类似于带槽页面（slotted page）的页面布局。  

Tuples are typically sorted in page based on key.
元组通常根据键在页面中排序。
![Clip_2024-10-10_15-47-11.png](https://gitee.com/salianbooth/picture/raw/master/202410101547709.png)

# Tuple Storage
- 元组本质上是一个**字节序列**。数据库管理系统负责解释这些字节并将其解析为不同的属性类型和值。
- DBMS使用系统目录中的**模式信息**（Schema），根据表的定义来理解元组的布局。这意味着存储的数据类型及其排列方式都由系统提前定义。
## Data Layout
- 元组存储时包含**表头信息**以及实际的属性值。DBMS需要理解这些字节以确定它们对应的具体字段。
- 例如，在存储表`AndySux`中的数据时，表头会保存每个字段的相关信息，而`id`和`value`字段分别存储其对应的数据。
![Clip_2024-10-10_15-52-05.png](https://gitee.com/salianbooth/picture/raw/master/202410101552850.png)

## Word-aligned Turple
- 为了让CPU能够有效地访问元组，DBMS会确保元组中的所有属性是**字对齐**（word-aligned）的，这可以避免非对齐读取所带来的额外工作或意外行为。
    
- 例如，一个64位CPU访问一个不对齐的32位数据时，可能会发生非对齐读取，导致性能下降。
![Clip_2024-10-10_15-52-42.png](https://gitee.com/salianbooth/picture/raw/master/202410101552632.png)
现在有以下的几种方法：
**方法 1：执行额外的读取**  
→ 执行两次读取来加载数据字的适当部分并将它们重新组装。
**方法 2：随机读取**  
→ 读取一些意外组合的字节，将其组装成一个64位字。
**方法 3：拒绝**  
→ 抛出一个异常，并希望应用程序能处理它。

### Padding
- **填充（Padding）**：在属性之间添加空白位，确保每个属性按字对齐方式存储。
![Clip_2024-10-10_15-55-09.png](https://gitee.com/salianbooth/picture/raw/master/202410101555782.png)

### Reordering
- **重新排序（Reordering）**：通过调整属性在元组中的顺序来减少填充的需求，从而提高存储效率。
![Clip_2024-10-10_15-55-44.png](https://gitee.com/salianbooth/picture/raw/master/202410101555451.png)


## Data Representation
![Clip_2024-10-10_15-57-13.png](https://gitee.com/salianbooth/picture/raw/master/202410101557552.png)

1. **整数类型**：
   - **INTEGER/BIGINT/SMALLINT/TINYINT**：这些整数类型的存储方式与C/C++语言中的相同。

2. **浮点数与定点数**：
   - **FLOAT/REAL vs. NUMERIC/DECIMAL**：浮点数使用IEEE-754标准进行存储，而定点数则是基于固定小数点表示法。

3. **可变长度的数据类型**：
   - **VARCHAR/VARBINARY/TEXT/BLOB**：这些数据类型会存储一个长度标识符，接着是实际的数据字节，或者指向数据存储位置的指针。使用这些类型时还需考虑排序和字符集等问题。

4. **时间与日期类型**：
   - **TIME/DATE/TIMESTAMP/INTERVAL**：这些类型通常存储为自Unix纪元以来的秒数或微秒数。

5. **浮点数的精度**：
   - 浮点数（如FLOAT、REAL）使用可变精度的原生C/C++类型存储，尽管它们可以快速计算，但不能保证完全准确的值。

6. **定点数的精度**：
   - **NUMERIC/DECIMAL**类型允许使用任意精度，适用于对精度要求严格的场景。这些类型通常存储为变量长度的二进制数据，并附带元数据。

7. **处理NULL值的几种方式**：
   - **Bitmap Header**：用一个集中位图来标识哪些字段为NULL。
   - **特殊值**：为每种数据类型指定一个特殊值代表NULL。
   - **Per Attribute Flag**：每个属性都有一个标识位表示其是否为NULL，但这种方式会影响字对齐。

8. **大值存储**：
   - 对于超过单个页大小的值，数据库系统会使用溢出页来存储。例如，Postgres的TOAST机制允许存储大于2KB的值，其他系统如MySQL和SQL Server也有类似的溢出页机制。

9. **外部值存储**：
   - 有些系统允许将大值存储在外部文件中，例如Oracle的BFILE和Microsoft的FILESTREAM。这些外部文件无法由数据库系统直接管理，因而缺乏持久性和事务保护。


> 下面的部分是存储层的第三次课的内容，其中主要讲述了**存储模型和压缩**，以下是具体的内容。

# DataBase Workloads
数据库在工作的时候也是有擅长和不擅长的部分的，所以人们对数据库处理不同任务的需求和特性，分成了以下的3种：
1. **在线事务处理（OLTP）**：擅长处理简单的，少量多次的写操作，比如，在JD上将商品加入购物车。
2. **在线分析处理（OLAP）**：擅长处理复杂的，大量的查询操作，比如，在JD上查询最热门的某商品。
3. **混合事务和分析处理（HTAP）**：同时支持上面的两种。
下面坐标系显示不同的workloads操作复杂度和读写分布：
![Clip_2024-10-14_10-55-49.png](https://gitee.com/salianbooth/picture/raw/master/202410141055094.png)


# Storage Models
数据库有着不同的工作负载，DBMS也有着相对应的不同的存储模型，不同的存储模型和相适配的工作负载组合，会让数据库的效率加倍，下面是DBMS的3种存储模型：
**Choice** #**1**: N-ary Storage Model (NSM)                ---> 行存储
**Choice** #2: Decomposition Storage Model (DSM)---->列存储
**Choice** #3: Hybrid Storage Model (PAX)                --->混合，常用列？

## NSM
NSM的存储方式我们通常称为“**行存储**”，基本就是将一个元组的所有属性全部存储在同一个界面中，就相似一行一行这样的排列（NSM的页面大小通常是某种硬件界面的整数倍，比如Mysql：16kb）。
这种存储方式很适用于OLTP负载，方便进行读写操作。

**OLTP EXAMPLE**
![Clip_2024-10-14_11-12-06.png](https://gitee.com/salianbooth/picture/raw/master/202410141112808.png)
对于这样的读操作，OLTP的场景下，是非常容易实现的，直接读取一行就获得了该元组的所有的信息。

**OLAP EXAMPLE**
但是对于一个普通的OLAP查询就不太友好了：
下面这段查询的目的是统计不同月份内 `.gov` 域名的用户登录次数。通过分组，可以得到每个月份 `.gov` 用户登录的总次数。
![Clip_2024-10-14_11-13-40.png](https://gitee.com/salianbooth/picture/raw/master/202410141113997.png)
我们可以发现，在查询的过程中，我们仅仅需要lastlogin和hostname这两个属性，但是还是需要遍历全部的属性，这这样的效率就大打折扣了。

总结一下，NSM 的优缺点如下：
- Advantages
    - 高效插入、更新、删除，涉及表中小部分 tuples
    - 有利于需要整个 tuple （所有 attributes）的查询    
- Disadvantages
    - 不利于需要检索表内大部分 tuples，或者只需要一小部分 attributes 的查询

## DSM
DSM是将所有元组的一个属性全部存储到同一张page中，我们也通常称之为“**列存储**”,这种存储方式特别使用于OLAP的，在这种情况下，查询是只读的，并且通常会对表的某些属性执行大规模扫描。

![Clip_2024-10-14_11-31-47.png](https://gitee.com/salianbooth/picture/raw/master/202410141131408.png)

将属性及元数据（例如，null 值）存储在单独的固定长度值数组中。

- 大多数系统使用这些数组的偏移量来唯一标识物理元组。
- 需要处理可变长度的值……

为每个属性维护一个单独的文件，其中有一个专用的头部区域，用于存储整个列的元数，如下图所示：
![Clip_2024-10-14_11-30-44.png](https://gitee.com/salianbooth/picture/raw/master/202410141130038.png)

**OLAP EXAMPLE**
这样就可以很方便的查询想要某一属性的信息了。
![Clip_2024-10-14_11-32-08.png](https://gitee.com/salianbooth/picture/raw/master/202410141132542.png)



由于 DSM 把 attributes 分开存储，也引入了新的问题，比如：
如何跟踪每个 tuple 的不同 attributes？可能的解决方案有：
1. Fixed-length Offsets：每个 attribute 都是定长的，直接靠 offset 来跟踪（常用）
2. Embedded Tuple Ids：在每个 attribute 前面都加上 tupleID

![Clip_2024-10-14_11-34-13.png](https://gitee.com/salianbooth/picture/raw/master/202410141134316.png)

总结一下，DSM 的优缺点如下：

- Advantages
    - 减少 I/O 操作
    - 更好的查询处理和数据压缩支持
- Disadvantages
    - 涉及少量 tuples、多数 attributes 的查询低效

## PAX
pax模型是一种混合存储模型，它有着列存储快速处理的优势，还有着列存储的空间优势。
那么它是如何做到的呢？下图：
![Clip_2024-10-14_14-16-05.png](https://gitee.com/salianbooth/picture/raw/master/202410141416054.png)
它先把数据分成不同的行组（row group），然后同一属性的数据像列存储一样存在一起，然后其他的属性也一样，这些不同的属性组又通过行存储在一起，这样就是一个row group，通过全局的**header**来存储各个group的偏移量。

# DATABASE COMPRESS
正如上面的核心问题所说的，这次的主要解决的是如何存放数据库文件在disk上。
在DBMS在从磁盘中提取数据进行查询时，I/O是主要的瓶颈。主要是因为磁盘访问比CPU和内容慢的多，并且如果数据不在连续的存储在磁盘上，DBMS还要进行大量的随机I/O操作，这大大提高了延迟。
所以为了缓解这一问题，我们要对数据进行压缩，争取每次交换都尽可能的获得更多的数据。
现在有以下的三个目标：
- **固定长度值**：压缩必须生成固定长度的值（可变长度数据除外）。
- **推迟解压缩**：尽量在查询执行的后期才进行解压缩操作，也称为“延迟物化”。
- **无损压缩**：数据库压缩必须是无损的，以确保不会丢失数据。

## Compress Granularity
压缩的粒度，颗粒程度？我也不知道为什么这一翻译，主要有以下几种：
- **块级压缩**：对同一表的多个元组进行块压缩。
- **元组级压缩**：压缩整个元组的内容，通常用于行存储模型（NSM）。
- **属性级压缩**：压缩单个元组中的某个属性，可以应用于同一元组的多个属性。
- **列级压缩**：压缩多个元组中一个或多个属性的值，通常用于列存储模型（DSM）。
我们接下来以列级压缩举例子，来展示各种不同的压缩方法。

## Column Compression
- **运行长度编码（Run-Length Encoding，RLE）**：将同一列中连续相同的值压缩成三元组（值、开始位置、连续个数）。
![Clip_2024-10-14_15-20-27.png](https://gitee.com/salianbooth/picture/raw/master/202410141520499.png)

- **位打包编码（Bit-Packing Encoding）**：当整数属性的值小于其数据类型范围时，减少表示每个值的位数。
![Clip_2024-10-14_15-27-53.png](https://gitee.com/salianbooth/picture/raw/master/202410141528140.png)
- **位图编码（Bitmap Encoding）**：为每个属性的唯一值存储一个独立的位图，适用于低基数的值。
![Clip_2024-10-14_15-28-35.png](https://gitee.com/salianbooth/picture/raw/master/202410141528703.png)
- **增量编码（Delta Encoding）**：记录同一列中连续值之间的差异。
![Clip_2024-10-14_15-28-48.png](https://gitee.com/salianbooth/picture/raw/master/202410141528389.png)
- **字典压缩（Dictionary Compression）**：用较小的定长码替换频繁出现的值，并维护一个字典来映射这些码和原始值。
![Clip_2024-10-14_15-29-00.png](https://gitee.com/salianbooth/picture/raw/master/202410141529723.png)
字典压缩的数据结构：

Choice # 1：数组
→ 一个包含可变长度字符串的数组和另一个指向字符串偏移量的指针数组。  
→ 更新代价高，因此仅适用于不可变文件。

Choice # 2：哈希表
→ 快速且紧凑。  
→ 无法支持范围查询和前缀查询。

Choice # 3：B+树
→ 比哈希表慢，占用更多内存。  
→ 可以支持范围查询和前缀查询。