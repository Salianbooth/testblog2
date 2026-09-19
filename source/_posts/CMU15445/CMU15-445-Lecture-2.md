---
title: CMU15-445:Lecture 2
date: 2024-10-05 22:46:38
tags:
  - DB
  - Cpp
categories:
  - CMU15-445
---
>lecture2的标题叫做Moden SQL，主要是讲了SQL的一些基本的语句，这一块我看的是中文的讲解+黑马程序员的MySQL数据库的基础视频，所以本次的blog也是按照黑马程序员的内容进行讲解，并且包含本次课程的内容。

# SQL History
- SQL的前身是1971年IBM创建的SQUARE，随后演变为1972年的SEQUEL，最终发展为SQL。
- 当前SQL标准是_SQL:2023_，它支持属性图查询和多维数组。之前的版本如SQL:2016引入了对JSON的支持，SQL:2011支持时间数据库等【8†source】。
![Clip_2024-10-06_18-20-27.png](https://gitee.com/salianbooth/picture/raw/master/202410061820185.png)

# SQL 分类
SQL可以基本分成以下的4类：
1. Data Manipulation Language (DML) 
2. Data Definition Language (DDL) 
3. Data Query Language（DQL）
4. Data Control Language (DCL)
![Clip_2024-10-06_18-23-36.png](https://gitee.com/salianbooth/picture/raw/master/202410061823443.png)
接下来我们分别介绍基本操作。

# DDL
Data Definition Language，数据定义语言，用来定义数据库对象(数据库，表，字段) 。
就是对数据库或者表进行创建，删除，查询等操作。
### 1. **数据库操作**

- **查询所有数据库**:
  ```sql
  SHOW DATABASES;
  ```

- **创建数据库**:
  ```sql
  CREATE DATABASE [IF NOT EXISTS] 数据库名 [DEFAULT CHARSET 字符集] [COLLATE 排序规则];
  ```
  示例：
  ```sql
  CREATE DATABASE itcast;
  ```

- **删除数据库**:
  ```sql
  DROP DATABASE [IF EXISTS] 数据库名;
  ```

- **切换数据库**:
  ```sql
  USE 数据库名;
  ```
### 2. **表操作**

- **查询当前数据库所有表**:
  ```sql
  SHOW TABLES;
  ```

- **查看表结构**:
  ```sql
  DESC 表名;
  ```

- **查询创建表的语句**:
  ```sql
  SHOW CREATE TABLE 表名;
  ```

- **创建表**:
  ```sql
  CREATE TABLE 表名 (
      字段1 字段1类型 [COMMENT 字段1注释],
      字段2 字段2类型 [COMMENT 字段2注释],
      字段3 字段3类型 [COMMENT 字段3注释],
      ...,
      字段n 字段n类型 [COMMENT 字段n注释]
  ) [COMMENT 表注释];
  ```
  示例：设计一张员工信息表，要求如下： 
  1. 编号（纯数字） 
  2. 员工工号 (字符串类型，长度不超过10位) 
  3. 员工姓名（字符串类型，长度不超过10位） 
  4. 性别（男/女，存储一个汉字） 
  5. 年龄（正常人年龄，不可能存储负数）
  6. 身份证号（二代身份证号均为18位，身份证中有X这样的字符） 
  7. 入职时间（取值年月日即可）
  ```sql
  CREATE TABLE tb_user (
      id INT COMMENT '编号',
      name VARCHAR(50) COMMENT '姓名',
      age INT COMMENT '年龄',
      gender VARCHAR(1) COMMENT '性别'
  ) COMMENT '用户表';
  ```

- **修改表结构**
  1. **添加字段**:
     ```sql
     ALTER TABLE 表名 ADD 字段名 数据类型 [COMMENT 注释];
     ```
     示例：为emp表增加一个新的字段”昵称”为nickname，类型为varchar(20)
     ```sql
     ALTER TABLE emp ADD nickname VARCHAR(20) COMMENT '昵称';
     ```

  2. **修改字段类型**:
     ```sql
     ALTER TABLE 表名 MODIFY 字段名 新数据类型;
     ```

  3. **修改字段名**:
     ```sql
     ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型;
     ```
     示例：将emp表的nickname字段修改为username，类型为varchar(30)
     ```sql
     ALTER TABLE emp CHANGE nickname username VARCHAR(30) COMMENT '用户名';
     ```

  4. **删除字段**:
     ```sql
     ALTER TABLE 表名 DROP 字段名;
     ```

  5. **修改表名**:
     ```sql
     ALTER TABLE 表名 RENAME TO 新表名;
     ```
### 3. **删除表**

- **删除表**:
  ```sql
  DROP TABLE [IF EXISTS] 表名;
  ```

- **清空表数据**:
  ```sql
  TRUNCATE TABLE 表名;
  ```

### 4.数据类型
- 整形
![Clip_2024-10-06_18-33-13.png](https://gitee.com/salianbooth/picture/raw/master/202410061833874.png)
![Clip_2024-10-06_18-33-22.png](https://gitee.com/salianbooth/picture/raw/master/202410061833828.png)
- 字符串类型
![Clip_2024-10-06_18-33-31.png](https://gitee.com/salianbooth/picture/raw/master/202410061834819.png)

 - 日期类型
 ![Clip_2024-10-06_18-34-15.png](https://gitee.com/salianbooth/picture/raw/master/202410061834769.png)

# DML
DML英文全称是Data Manipulation Language(数据操作语言)，用来对数据库中表的数据记录进 行增、删、改操作。
- 添加数据（INSERT） 
- 修改数据（UPDATE） 
- 删除数据（DELETE）
### 1. **插入数据 (INSERT)**
用于向表中添加数据。

- **语法**：
  ```sql
  INSERT INTO 表名 (字段1, 字段2, ...) VALUES (值1, 值2, ...);
  ```
  - 示例1：向`employee`表所有字段插入数据：
    ```sql
    INSERT INTO employee(id, workno, name, gender, age, idcard, entrydate) 
    VALUES (1, '1', 'Itcast', '男', 10, '123456789012345678', '2000-01-01');
    ```

  - 示例2：向所有字段插入批量数据：
    ```sql
    INSERT INTO employee 
    VALUES (2, '2', '张无忌', '男', 18, '123456789012345670', '2005-01-01'), 
           (3, '3', '韦一笑', '男', 38, '123456789012345670', '2005-01-01');
    ```

### 2. **更新数据 (UPDATE)**
用于修改表中的数据。

- **语法**：
  ```sql
  UPDATE 表名 SET 字段1 = 值1, 字段2 = 值2, ... [WHERE 条件];
  ```
  - 示例1：将`id`为1的记录的`name`修改为`itheima`：
    ```sql
    UPDATE employee SET name = 'itheima' WHERE id = 1;
    ```

  - 示例2：修改多个字段：
    ```sql
    UPDATE employee SET name = '小昭', gender = '女' WHERE id = 1;
    ```

### 3. **删除数据 (DELETE)**
用于删除表中的数据。

- **语法**：
  ```sql
  DELETE FROM 表名 [WHERE 条件];
  ```
  - 示例1：删除`gender`为`女`的员工：
    ```sql
    DELETE FROM employee WHERE gender = '女';
    ```

  - 示例2：删除表中的所有数据：
    ```sql
    DELETE FROM employee;
    ```

### 注意事项：
- **INSERT**：插入数据时，字段的顺序和数据的顺序需对应。
- **UPDATE**：若不指定`WHERE`条件，则将更新所有记录。
- **DELETE**：不指定`WHERE`条件时，将删除所有数据，慎用。

# DQL
### 1. **基础查询**
- **语法**：
  ```sql
  SELECT 字段列表 FROM 表名;
  ```
  - 示例：查询所有字段：
    ```sql
    SELECT * FROM emp;
    ```
  - 示例：查询指定字段：
    ```sql
    SELECT name, age FROM emp;
    ```

### 2. **条件查询 (WHERE)**
- **语法**：
  ```sql
  SELECT 字段列表 FROM 表名 WHERE 条件;
  ```
  - 示例：查询年龄大于25的员工：
    ```sql
    SELECT * FROM emp WHERE age > 25;
    ```

### 3. **聚合函数**
聚合函数用于对查询结果进行统计，如`COUNT`、`SUM`、`AVG`、`MAX`、`MIN`等。
- **语法**：
  ```sql
  SELECT 聚合函数(字段) FROM 表名;
  ```
  - 示例：统计员工人数：
    ```sql
    SELECT COUNT(*) FROM emp;
    ```

### 4. **分组查询 (GROUP BY)**
分组查询用于将结果按某一列进行分组。
- **语法**：
  ```sql
  SELECT 字段, 聚合函数(字段) FROM 表名 GROUP BY 字段;
  ```
  - 示例：按性别统计人数：
    ```sql
    SELECT gender, COUNT(*) FROM emp GROUP BY gender;
    ```

### 5. **排序查询 (ORDER BY)**
排序查询用于对结果按某一列进行排序。
- **语法**：
  ```sql
  SELECT 字段列表 FROM 表名 ORDER BY 字段 [ASC|DESC];
  ```
  - 示例：按年龄升序排序：
    ```sql
    SELECT * FROM emp ORDER BY age ASC;
    ```

### 6. **分页查询 (LIMIT)**
分页查询用于限制查询结果的数量，常用于分页显示。
- **语法**：
  ```sql
  SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询条数;
  ```
  - 示例：查询前10条记录：
    ```sql
    SELECT * FROM emp LIMIT 0, 10;
    ```

### 7. **执行顺序**
DQL语句的执行顺序一般为：
1. `FROM`：确定数据来源。
2. `WHERE`：筛选条件。
3. `GROUP BY`：分组操作。
4. `HAVING`：对分组后的结果进行筛选。
5. `SELECT`：选取字段。
6. `ORDER BY`：排序操作。
7. `LIMIT`：分页查询。
![Clip_2024-10-07_17-00-04.png](https://gitee.com/salianbooth/picture/raw/master/202410071700571.png)

# 约束
### 1. **非空约束 (NOT NULL)**
   - **描述**：该约束确保字段不能为`NULL`，也就是说数据必须有值。
   - **示例**：
     ```sql
     CREATE TABLE users (
       id INT,
       name VARCHAR(100) NOT NULL
     );
     ```
### 2. **唯一约束 (UNIQUE)**
   - **描述**：该约束确保字段的值在表中是唯一的，不允许重复值。
   - **示例**：
     ```sql
     CREATE TABLE users (
       id INT,
       email VARCHAR(100) UNIQUE
     );
     ```
### 3. **主键约束 (PRIMARY KEY)**
   - **描述**：主键约束用于唯一标识表中的每一行，要求非空且唯一。一个表只能有一个主键。
   - **示例**：
     ```sql
     CREATE TABLE users (
       id INT PRIMARY KEY,
       name VARCHAR(100)
     );
     ```
### 4. **默认约束 (DEFAULT)**
   - **描述**：默认约束允许在插入数据时，如果没有指定某字段的值，则自动使用默认值。
   - **示例**：
     ```sql
     CREATE TABLE users (
       id INT,
       status CHAR(1) DEFAULT '1'
     );
     ```

### 5. **检查约束 (CHECK)**
   - **描述**：检查约束确保字段的值满足某个条件。MySQL 8.0.16版本及以后支持。
   - **示例**：
     ```sql
     CREATE TABLE users (
       id INT,
       age INT CHECK (age > 0 AND age <= 120)
     );
     ```
### 6. **外键约束 (FOREIGN KEY)**
   - **描述**：外键用于将两张表的数据关联起来，确保引用的字段在另一张表中存在，保证数据的一致性和完整性。
   - **示例**：
     ```sql
     CREATE TABLE orders (
       id INT,
       user_id INT,
       FOREIGN KEY (user_id) REFERENCES users(id)
     );
     ```

# 多表查询

在你提供的PDF中，**多表查询**是通过使用多张表进行数据检索的操作。多表查询主要包括**内连接**、**外连接**（左连接、右连接）、**交叉连接**、**子查询**等。

### 1. **内连接（INNER JOIN）**
   - **描述**：内连接返回两张表中满足连接条件的记录。如果没有匹配的记录，结果集中不会包含该记录。
   - **语法**：
     ```sql
     SELECT 列名列表
     FROM 表1
     INNER JOIN 表2 ON 表1.列名 = 表2.列名;
     ```
   - **示例**：查询所有订单及其对应的用户信息
     ```sql
     SELECT orders.id, users.name, orders.amount
     FROM orders
     INNER JOIN users ON orders.user_id = users.id;
     ```

### 2. **左连接（LEFT JOIN）**
   - **描述**：左连接会返回左表中的所有记录，即使右表中没有匹配的记录。对于没有匹配的记录，右表的字段值将显示为`NULL`。
   - **语法**：
     ```sql
     SELECT 列名列表
     FROM 表1
     LEFT JOIN 表2 ON 表1.列名 = 表2.列名;
     ```
   - **示例**：查询所有用户及其对应的订单信息（即使某些用户没有下订单）
     ```sql
     SELECT users.name, orders.amount
     FROM users
     LEFT JOIN orders ON users.id = orders.user_id;
     ```

### 3. **右连接（RIGHT JOIN）**
   - **描述**：右连接与左连接相反，返回右表中的所有记录，即使左表中没有匹配的记录。对于没有匹配的记录，左表的字段值将显示为`NULL`。
   - **语法**：
     ```sql
     SELECT 列名列表
     FROM 表1
     RIGHT JOIN 表2 ON 表1.列名 = 表2.列名;
     ```
   - **示例**：查询所有订单及其对应的用户信息（即使某些订单没有对应的用户）
     ```sql
     SELECT users.name, orders.amount
     FROM users
     RIGHT JOIN orders ON users.id = orders.user_id;
     ```

### 4. **交叉连接（CROSS JOIN）**
   - **描述**：交叉连接返回表1和表2的**笛卡尔积**，即每一行表1都会与每一行表2组合。交叉连接通常不使用`ON`条件。
   - **语法**：
     ```sql
     SELECT 列名列表
     FROM 表1
     CROSS JOIN 表2;
     ```
   - **示例**：查询所有用户与所有订单的组合
     ```sql
     SELECT users.name, orders.amount
     FROM users
     CROSS JOIN orders;
     ```

### 5. **子查询（Subquery）**
   - **描述**：子查询是在查询中嵌套另一条查询语句。可以用于筛选数据、判断条件等。
   - **语法**：
     ```sql
     SELECT 列名列表
     FROM 表
     WHERE 列名 IN (子查询);
     ```
   - **示例**：查询下了订单的用户信息
     ```sql
     SELECT name
     FROM users
     WHERE id IN (SELECT user_id FROM orders);
     ```

### 6. **联合查询（UNION）**
   - **描述**：`UNION`用于将两个或多个查询的结果合并为一个结果集，要求各查询返回的列数及数据类型相同。
   - **语法**：
     ```sql
     SELECT 列名列表 FROM 表1
     UNION
     SELECT 列名列表 FROM 表2;
     ```
   - **示例**：查询用户和管理员的姓名
     ```sql
     SELECT name FROM users
     UNION
     SELECT name FROM admins;
     ```

### 总结
- **INNER JOIN** 返回两张表中满足条件的记录。
- **LEFT JOIN** 返回左表的所有记录，如果右表中没有匹配则显示`NULL`。
- **RIGHT JOIN** 返回右表的所有记录，如果左表中没有匹配则显示`NULL`。
- **CROSS JOIN** 返回表1和表2的笛卡尔积。
- **子查询** 允许查询嵌套其他查询。
- **UNION** 合并多个查询结果。
