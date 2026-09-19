---
title: Must-Know SQL Essentials
date: 2024-10-15 09:45:51
tags:
  - MySQL
categories:
  - book
---
>这是阅读完《SQL必知必会》的一篇总结blog，主要是记录了一些基础的sql语句，我们将按照书的顺序进行编写，从简单的创建到复杂的查询，我们将一步一步的实现。

# 1.Mysql基础
### 数据库操作
有创建数据库，删除数据库，显示所有数据库和切换数据库：
```sql
-- 显示所有数据库 
SHOW DATABASES; 
-- 创建数据库 
CREATE DATABASE itcast; 
-- 删除数据库 
DROP DATABASE IF EXISTS itcast; 
-- 切换数据库 
USE itcast;
```

## 表操作

有创建表，修改表结构，删除表：

```sql
-- 显示所有表
SHOW TABLES;

-- 查看表结构
DESC 表名;

-- 查看创建表的语句
SHOW CREATE TABLE 表名;

-- 创建表 tb_user 示例
CREATE TABLE tb_user (
    id INT COMMENT '编号',
    name VARCHAR(50) COMMENT '姓名',
    age INT COMMENT '年龄',
    gender VARCHAR(1) COMMENT '性别'
) COMMENT '用户表';

-- 修改表结构
-- 1. 添加字段
ALTER TABLE tb_user ADD nickname VARCHAR(20) COMMENT '昵称';

-- 2. 修改字段类型
ALTER TABLE tb_user MODIFY nickname VARCHAR(30);

-- 3. 修改字段名
ALTER TABLE tb_user CHANGE nickname username VARCHAR(30) COMMENT '用户名';

-- 4. 删除字段
ALTER TABLE tb_user DROP username;

-- 5. 修改表名
ALTER TABLE tb_user RENAME TO user_info;

-- 删除表
DROP TABLE IF EXISTS tb_user;

-- 清空表数据
TRUNCATE TABLE tb_user;
```

## 数据操作
插入，更新，删除
```sql
-- 插入数据
INSERT INTO employee(id, workno, name, gender, age, idcard, entrydate) 
VALUES (1, '1', 'Itcast', '男', 10, '123456789012345678', '2000-01-01');

-- 批量插入数据
INSERT INTO employee 
VALUES (2, '2', '张无忌', '男', 18, '123456789012345670', '2005-01-01'), 
       (3, '3', '韦一笑', '男', 38, '123456789012345670', '2005-01-01');

-- 更新数据
UPDATE employee SET name = 'itheima' WHERE id = 1;

-- 删除数据
DELETE FROM employee WHERE gender = '女';
```

# 检索数据

查询原本的语句很简单，但是它有着很多的子句来实现各种查询，我们先从单表查询开始，然后一步一步介绍不同的子句比如where,limit,like,group by，最后再到子查询，多表查询这样的复杂的查询部分。

## 基础查询和常用子句

```sql
-- 基础查询
SELECT * FROM emp;

-- 条件查询 where子句
SELECT * 
FROM emp 
WHERE age > 25;
-- 注意：`WHERE` 子句是在筛选记录之前执行的。

-- 分组查询
SELECT gender, COUNT(*) 
FROM emp 
GROUP BY gender;

-- having子句 -->用于对分组后的结果进行筛选
SELECT gender, COUNT(*) 
FROM emp 
GROUP BY gender 
HAVING COUNT(*) > 2;

-- 排序查询  --> 可以按升序（`ASC`，默认）或降序（`DESC`）排列
SELECT * 
FROM emp 
ORDER BY age ASC;  -- 一定要是最后一个子句

-- 分页查询
SELECT * 
FROM emp 
LIMIT 0, 10;   -- > 始索引从0开始，即`LIMIT 0, 10` 表示从第1行开始，返回10行记录。


-- In 子句  -- 查询部门编号为1、2、3的员工
SELECT * 
FROM emp 
WHERE dept_id IN (1, 2, 3);   -- > `IN` 子句可以替代多个 `OR` 条件的写法，简化查询。

-- Between子句
SELECT * 
FROM emp 
WHERE age 
BETWEEN 20 AND 30;    -- > `BETWEEN` 包括边界值，等价于 `>= 值1 AND <= 值2`。

-- LIKE 子句
SELECT * 
FROM emp 
WHERE name LIKE '张%';  -- > `LIKE` 通常用于模糊查询，匹配部分字符串。

-- EXIT子句
SELECT * 
FROM users 
WHERE EXISTS (
    SELECT 1 
    FROM orders 
    WHERE users.id = orders.user_id);
    
-- JOIN子句
SELECT orders.id, users.name, orders.amount
FROM orders
INNER JOIN users ON orders.user_id = users.id;
```

## 常用函数

```sql
-- 1. 聚合函数（Aggregate Functions）

-- 1.1 COUNT() — 计数
-- 统计员工总数
SELECT COUNT(*) FROM emp;

-- 统计年龄大于30的员工数量
SELECT COUNT(*) FROM emp WHERE age > 30;

-- 1.2 SUM() — 求和
-- 计算所有员工的总工资
SELECT SUM(salary) FROM emp;

-- 1.3 AVG() — 平均值
-- 计算员工的平均工资
SELECT AVG(salary) FROM emp;

-- 1.4 MAX() — 最大值
-- 查询员工的最高工资
SELECT MAX(salary) FROM emp;

-- 1.5 MIN() — 最小值
-- 查询员工的最低工资
SELECT MIN(salary) FROM emp;


-- 2. 字符串函数（String Functions）

-- 2.1 CONCAT() — 字符串连接
-- 将员工的姓和名拼接成全名
SELECT CONCAT(first_name, ' ', last_name) AS full_name FROM emp;

-- 2.2 LENGTH() — 字符串长度
-- 查询员工姓名的长度
SELECT LENGTH(first_name) FROM emp;

-- 2.3 UPPER() 和 LOWER() — 转换为大写/小写
-- 将员工名字全部转换为大写
SELECT UPPER(first_name) FROM emp;

-- 将员工名字全部转换为小写
SELECT LOWER(first_name) FROM emp;

-- 2.4 SUBSTRING() — 截取子字符串
-- 从员工的名字中提取前3个字符
SELECT SUBSTRING(first_name, 1, 3) FROM emp;

-- 2.5 TRIM() — 去除空格
-- 去除员工名字两端的空格
SELECT TRIM(first_name) FROM emp;


-- 3. 日期和时间函数（Date and Time Functions）

-- 3.1 NOW() — 当前日期和时间
-- 查询当前日期和时间
SELECT NOW();

-- 3.2 CURDATE() 和 CURTIME() — 当前日期/时间
-- 查询当前日期和当前时间
SELECT CURDATE(), CURTIME();

-- 3.3 DATE_ADD() — 日期加法
-- 查询员工入职日期之后30天的日期
SELECT DATE_ADD(hire_date, INTERVAL 30 DAY) FROM emp;

-- 3.4 DATEDIFF() — 日期差
-- 计算员工自入职以来的天数
SELECT DATEDIFF(NOW(), hire_date) FROM emp;


-- 4. 数学函数（Math Functions）

-- 4.1 ROUND() — 四舍五入
-- 将员工的工资四舍五入到整数
SELECT ROUND(salary, 0) FROM emp;

-- 4.2 CEIL() 和 FLOOR() — 向上取整/向下取整
-- 向上取整员工的奖金
SELECT CEIL(bonus) FROM emp;

-- 向下取整员工的奖金
SELECT FLOOR(bonus) FROM emp;

-- 4.3 ABS() — 绝对值
-- 查询员工的绩效分数的绝对值
SELECT ABS(performance_score) FROM emp;

-- 4.4 MOD() — 取余数
-- 查询员工编号除以3的余数
SELECT MOD(emp_id, 3) FROM emp;

```

## 多表查询
多表查询有着更多的内容，如子查询，联结表等等，我比较喜欢使用子查询，但是这样的话对于查询效率有所影响，
假设我们有以下的几张表，本博客的查询操作以这个表为基础：
![image.png](https://gitee.com/salianbooth/picture/raw/master/202410170842846.png)

![image.png](https://gitee.com/salianbooth/picture/raw/master/202410170842889.png)

![image.png](https://gitee.com/salianbooth/picture/raw/master/202410170842275.png)

![image.png](https://gitee.com/salianbooth/picture/raw/master/202410170842038.png)

![image.png](https://gitee.com/salianbooth/picture/raw/master/202410170842815.png)


### 子查询
子查询有点像是嵌套if语句？就像是我们先通过子查询把一个大范围的内容缩小到一个我们想要的范围，再从这个小范围里面进行查询。
要注意的是，子查询总是从内向外进行处理的，这样就导致语句难以阅读和调试，这里就建议使用换行和缩进来让代码更美观一些，下面是示例代码：
```sql
-- 查询咖啡类商品的平均售价。

select categoryno,avg(saleprice) as avg_sale
from goods
where categoryno in (select categoryno
                    from category
                    where categoryname = '咖啡')
group by categoryno;
```
### 联结表（Joins）
1. **内联结（INNER JOIN）**
2. **左联结（LEFT JOIN 或 LEFT OUTER JOIN）**
3. **右联结（RIGHT JOIN 或 RIGHT OUTER JOIN）**
4. **全外联结（FULL OUTER JOIN）**
5. **交叉联结（CROSS JOIN）**
6. **自联结（SELF JOIN）**
我们都只需要简单了解以下即可，正常的话直接使用 **join（inner join）** 就行，它只会返回亮哥表中匹配的记录，避免了不必要的数据重复。
#### 内联结
语法：
```sql
SELECT columns 
FROM table1 
INNER JOIN table2 ON table1.column = table2.column;
```
我们这里可以使用子查询的例子进行演示：
```sql
-- 查询咖啡类商品的平均售价。

-- 子查询
select categoryno,avg(saleprice) as avg_sale
from goods
where categoryno in (select categoryno
                    from category
                    where categoryname = '咖啡')
group by categoryno;

-- 内联结
select categoryno,avg(saleprice) as avg_sale
from goods g
inner join category c on g.categoryno = c.categoryno
where c.categoryname = '咖啡'
group by categoryno;

```
子查询并不总是执行复杂的SELECT操作的最有效方法，有时使用join会更适合。



###  使用别名（Aliases）

使用表别名可以简化查询语句，尤其是在联结多个表或进行自联结时。

**示例**：

```sql
SELECT e.name AS Employee, m.name AS Manager
FROM employees e
LEFT JOIN employees m
ON e.manager_id = m.id;
```

### 使用 ON 和 USING 子句

在联结时，可以使用 `ON` 或 `USING` 子句指定联结条件。

**使用 `ON` 子句**：

```sql
SELECT e.name, d.department_name
FROM employees e
INNER JOIN departments d
ON e.department_id = d.id;
```

**使用 `USING` 子句**（当两个表有同名的联结列时）：

```sql
SELECT e.name, d.department_name
FROM employees e
INNER JOIN departments d
USING (department_id);
```

**注意**：`USING` 子句要求联结列在两个表中同名。
### 组合查询（Union）
使用Union操作可以组合多条SQL查询，将结果组合成一个结果集。
使用Union很简单，只需要我们在要组合的select语句之间假设Union语句即可，例子如下：
假设我们有2个表，`current_employees`（当前员工）和 `former_employees`（前任员工）

**`current_employees` 表**

| id   | name    | department  |
| ---- | ------- | ----------- |
| 1    | Alice   | Engineering |
| 2    | Bob     | Marketing   |
| 3    | Charlie | Engineering |

**`former_employees` 表**

| id   | name  | department |
| ---- | ----- | ---------- |
| 4    | David | HR         |
| 5    | Eva   | Marketing  |
| 6    | Frank | Sales      |

创建一个联结视图，显示员工姓名、部门名称和工资。

```sql
-- 合并员工信息
SELECT id, name, department
FROM current_employees
UNION
SELECT id, name, department
FROM former_employees;

```
注意：如果两个表中存在完全相同的记录，`UNION` 会自动去除重复项。如果你希望包括所有重复记录，可以使用 `UNION ALL`。

# 视图
视图的应用和表操作差不多，但是它是作为一个虚拟表存在的，它的作用就相当于是把select查询后的结果，生成到一张我们可以使用的虚拟表上，这样大大提高了语句的复用，还有着保护真表的数据的作用。
视图的作用挺多的，但是也有不少限制：
- **不支持索引**：视图本身不能创建索引，但基础表可以有索引。
- **复杂视图限制**：包含聚合函数、联结多个表、子查询等复杂逻辑的视图可能不支持更新操作。
- **性能考虑**：过多或过于复杂的视图可能影响查询性能，因为每次查询视图时都会执行其定义的SQL语句。
我们接下来简单介绍一下视图的基本操作：
## **创建视图**
使用 `CREATE VIEW` 语句可以创建一个视图。以下是创建视图的基本语法和示例。
### **基本语法**
```sql
CREATE VIEW 视图名称 AS
SELECT 列1, 列2, ...
FROM 表名
WHERE 条件;
```

**示例:**
假设有一个 `employees` 表，包含以下字段：`id`, `name`, `department`, `salary`。我们希望创建一个只显示员工姓名和部门的视图。
```sql
CREATE VIEW employee_view AS
SELECT name, department
FROM employees;
```
###  **带条件的视图**

创建一个只显示工资高于50000的员工的视图。
```sql
CREATE VIEW high_salary_employees AS
SELECT name, department, salary
FROM employees
WHERE salary > 50000;
```
---
## **查询视图**

视图可以像普通表一样进行查询。
### **查询所有数据**
```sql
SELECT * FROM employee_view;
```
### **条件查询**
```sql
SELECT * FROM high_salary_employees
WHERE department = 'Engineering';
```
###  联结视图与其他表
假设有另一个表 `departments`，可以将视图与该表联结。
```sql
SELECT ev.name, d.department_name
FROM employee_view ev
JOIN departments d ON ev.department = d.id;
```
---
## **更新视图**
视图在某些情况下可以用于更新数据，但有一些限制。通常，视图可以执行 `INSERT`、`UPDATE` 和 `DELETE` 操作，前提是视图基于单个表，并且不包含复杂的计算或聚合。
### **更新视图中的数据**
假设 `employee_view` 只包含 `name` 和 `department`，可以更新 `department`。
```sql
UPDATE employee_view
SET department = 'Marketing'
WHERE name = 'Alice';
```

**注意**：并非所有视图都支持更新操作，具体取决于视图的定义。

---
## **修改和删除视图**

### **修改视图**
使用 `CREATE OR REPLACE VIEW` 语句可以修改已有的视图。
```sql
CREATE OR REPLACE VIEW employee_view AS
SELECT name, department, salary
FROM employees;
```
### **删除视图**
使用 `DROP VIEW` 语句可以删除视图。
```sql
DROP VIEW IF EXISTS employee_view;
```
#  存储过程
**存储过程（Stored Procedure）** 是一段预编译的 SQL 代码，保存在数据库中，可以重复调用。你可以将存储过程理解为一个可以执行的 SQL 脚本，适用于需要重复使用或复杂的业务逻辑操作。

本身就是用来封装一段SQL语句，进行复用，我的理解就是这个是可以没有return的函数（但是这只是粗浅的理解，实际上存储过程和函数区别和使用场景有着挺大的不同的）

可以包含多种 SQL 语句，适合更复杂的操作，包括插入、更新、删除和事务控制。存储过程可以处理数据修改、事务管理等复杂

- **存储过程**：可以接受三种类型的参数：
    - `IN`：传入参数，存储过程内部只能读取。
    - `OUT`：输出参数，存储过程内部修改后返回给调用者。
    - `INOUT`：既可以传入，又可以返回值。

**基础语法**
```sql
CREATE PROCEDURE procedure_name ([IN | OUT | INOUT] param_name datatype)
BEGIN
    -- SQL 语句
END;
```
- `procedure_name`：存储过程的名称。
- `IN`、`OUT`、`INOUT`：参数的类型，分别表示输入、输出、输入输出参数。
- `parameter_name`：参数的名称。
- `datatype`：参数的数据类型（如 `INT`、`VARCHAR` 等）。
- `BEGIN ... END`：存储过程的主体，包含具体的 SQL 语句。
**简单示例**：
```sql
DELIMITER $$  -- 改变分隔符以支持存储过程的创建

CREATE PROCEDURE say_hello()
BEGIN
    SELECT 'Hello, World!';
END $$

DELIMITER ;  -- 恢复默认的分隔符

```
使用 `call say_hello()`调用：
![Clip_2024-10-18_09-11-02.png](https://gitee.com/salianbooth/picture/raw/master/202410180911452.png)


**存储过程的优点**：
1. **提高性能**：存储过程在创建时被编译，执行时效率较高；而且通过减少客户端和服务器之间的通信，可以提升执行效率。
2. **代码重用**：存储过程可以多次调用，减少了重复代码。
3. **封装复杂逻辑**：复杂的业务逻辑可以被封装在存储过程内，客户端只需调用存储过程，而无需关心其内部实现。
4. **安全性**：通过存储过程可以控制用户对数据库的访问权限，避免直接执行 SQL 操作。
5. **事务支持**：存储过程内部可以控制事务，确保数据的完整性和一致性。

**缺点**：
1. **调试困难**：相比应用程序中的代码，调试存储过程中的逻辑更加困难。
2. **可移植性差**：不同的数据库管理系统（如 MySQL、PostgreSQL、Oracle）对存储过程的支持存在差异，代码的移植性较差。
3. **维护复杂**：随着存储过程的数量增加，数据库中的逻辑复杂度也会增加，维护变得更加困难。

**适用场景：**
1. **批量处理**：存储过程适合批量更新或操作多个表的场景。
2. **业务逻辑封装**：将业务逻辑集中在数据库端，通过存储过程控制业务流程，如订单处理、库存管理等。
3. **事务处理**：多步数据库操作中需要确保数据一致性时，存储过程可以通过事务控制完成复杂操作。
4. **复杂查询**：需要执行多个查询、插入、更新或删除操作时，可以将这些操作封装在存储过程内。

# 事务处理

在复杂的操作中，存储过程通常会使用事务来确保数据的一致性。

当我们在进行存储过程时，假设这个过程中，数据库出现故障，那会怎么办呢？数据库中有可能会出现我们所不知道的空行或者不完整的表单。如何解决这种问题？这时候就需要使用事务处理。

那么事务处理到底是什么呢？事务处理是一种机制，用来管理必须成批执行的SQL操作。

使用事务处理，可以保证一组操作不会中途停止，它们要么完全执行，要么完全不执行。如果发生错误，则进行回退。

事务有四个重要的特性，称为 **ACID** 特性：

- **原子性（Atomicity）**：事务中的所有操作要么全部完成，要么全部不完成。任何一个操作失败，整个事务就会回滚到事务开始前的状态。
- **一致性（Consistency）**：事务执行前后，数据库的完整性约束没有被破坏。
- **隔离性（Isolation）**：并发执行的事务彼此之间不应相互影响，每个事务的中间状态对其他事务是不可见的。
- **持久性（Durability）**：一旦事务提交，其对数据库的更改是永久性的，即使系统崩溃也不会丢失。


MySQL 提供了一些用于控制事务的语句：

- **`START TRANSACTION` 或 `BEGIN`**：开启一个新的事务。
- **`COMMIT`**：提交事务，将所有操作的更改保存到数据库中。
- **`ROLLBACK`**：回滚事务，撤销自上次 `START TRANSACTION` 以来的所有操作。
- **`SAVEPOINT savepoint_name`**：设置一个事务保存点，可以在回滚时回到该保存点。
- **`ROLLBACK TO SAVEPOINT savepoint_name`**：将事务回滚到指定的保存点。

## 例子
假设我们有两个账户的余额记录在表 `accounts` 中，我们想要执行一次资金转账操作。如果任何一步失败，整个操作应该回滚。

```sql
CREATE TABLE accounts (
    account_id INT PRIMARY KEY,
    account_name VARCHAR(50),
    balance DECIMAL(10, 2)
);

-- 插入一些示例数据
INSERT INTO accounts (account_id, account_name, balance) VALUES
(1, 'Alice', 1000.00),
(2, 'Bob', 500.00);

```

假设我们要从 `Alice` 的账户转账 `200` 给 `Bob`，这个操作包含两步：

1. 从 `Alice` 的余额中扣除 `200`。
2. 向 `Bob` 的余额中增加 `200`。

如果任何一步失败，则应回滚。
```sql
DELIMITER $$

CREATE PROCEDURE transfer_funds(
    IN from_account_id INT,
    IN to_account_id INT,
    IN amount DECIMAL(10, 2)
)
BEGIN
    -- 开始事务
    START TRANSACTION;

    -- 检查来源账户的余额是否足够
    IF (SELECT balance FROM accounts WHERE account_id = from_account_id) < amount THEN
        -- 如果余额不足，则回滚事务并退出
        ROLLBACK;
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Insufficient balance';
    ELSE
        -- 从来源账户中扣除金额
        UPDATE accounts
        SET balance = balance - amount
        WHERE account_id = from_account_id;

        -- 向目标账户增加金额
        UPDATE accounts
        SET balance = balance + amount
        WHERE account_id = to_account_id;

        -- 提交事务
        COMMIT;
    END IF;
END $$

DELIMITER ;

```
调用存储过程：
```sql
CALL transfer_funds(1, 2, 200.00);
```
如果 `Alice` 的账户余额足够（大于等于 `200`），事务会成功提交，并完成转账操作。如果余额不足，事务会回滚，并返回错误信息 "Insufficient balance"。

**PS：**
在 MySQL 中，`SIGNAL` 语句用于手动触发一个错误或异常。当你使用 `SIGNAL` 时，数据库会抛出一个自定义的错误，并可以为该错误设置状态码和消息文本。

`SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Insufficient balance';` 的作用是在存储过程或触发器中**手动触发一个异常**，并且通过设置 `SQLSTATE` 错误代码和自定义错误消息来描述这个异常。

**语句解析**：
- **`SIGNAL`**：这个关键词用于触发错误或异常。
- **`SQLSTATE '45000'`**：`SQLSTATE` 是一个标准的 SQL 错误代码，表示某种错误类型。`'45000'` 是一个通用的自定义错误代码，表示一个用户定义的异常（即不属于标准 SQL 错误）。这是个比较常用的自定义错误状态码。
- **`SET MESSAGE_TEXT = 'Insufficient balance'`**：使用 `SET` 设置触发的错误消息文本。`MESSAGE_TEXT` 是 MySQL 中用于存储错误信息的一个属性。`'Insufficient balance'` 是我们想要返回给用户的自定义错误信息，表示“余额不足”。
## 事务处理的好处

- **数据一致性**：通过原子性和隔离性保证操作不会因失败而导致数据不一致。
- **错误处理**：可以在事务内捕获错误并选择是否回滚。
- **并发控制**：通过隔离级别管理并发操作对数据的影响。

# 游标
在 MySQL 中，**游标**（Cursor）是用于遍历查询结果集的数据库对象。它可以逐行地访问查询的每一行记录，非常适合在存储过程或触发器中处理复杂的逻辑，例如批量操作、逐行处理数据等。

### 游标的特点

1. **逐行处理**：游标允许在存储过程中逐行访问查询结果集，而不是一次性地返回整个结果。
2. **状态管理**：游标有不同的状态（如打开、关闭、结束）来控制对数据的访问。
3. **适合复杂逻辑**：在需要对查询结果进行逐行处理或复杂计算的情况下，游标非常有用。

### 使用游标的基本步骤

1. **声明游标**：定义游标并关联一个查询语句。
2. **打开游标**：打开游标以开始读取数据。
3. **读取数据**：使用 `FETCH` 语句从游标中逐行提取数据。
4. **关闭游标**：完成操作后，关闭游标释放资源。

### 游标的示例

假设有一个表 `employees`，结构如下：

| employee_id | first_name | last_name | salary |
|-------------|------------|-----------|--------|
| 1           | John       | Doe       | 5000   |
| 2           | Jane       | Smith     | 6000   |
| 3           | Bob        | Brown     | 7000   |

我们将编写一个存储过程，逐行遍历这个表的数据，并在控制台输出每个员工的姓名和薪水。

```sql
DELIMITER $$

CREATE PROCEDURE list_employees()
BEGIN
    -- 声明变量用于存储游标提取的数据
    DECLARE v_employee_id INT;
    DECLARE v_first_name VARCHAR(50);
    DECLARE v_last_name VARCHAR(50);
    DECLARE v_salary DECIMAL(10, 2);
    
    -- 声明一个标志，用于检查游标是否到达结果集的末尾
    DECLARE done INT DEFAULT 0;
    
    -- 声明游标
    DECLARE employee_cursor CURSOR FOR
        SELECT employee_id, first_name, last_name, salary FROM employees;
    
    -- 声明当游标读取到结果集末尾时的处理
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;
    
    -- 打开游标
    OPEN employee_cursor;
    
    -- 循环读取数据直到游标结束
    read_loop: LOOP
        -- 从游标中提取数据到变量中
        FETCH employee_cursor INTO v_employee_id, v_first_name, v_last_name, v_salary;
        
        -- 检查是否到达末尾
        IF done = 1 THEN
            LEAVE read_loop;
        END IF;
        
        -- 输出或处理每行数据
        SELECT CONCAT('ID: ', v_employee_id, ', Name: ', v_first_name, ' ', v_last_name, ', Salary: ', v_salary);
    END LOOP;

    -- 关闭游标
    CLOSE employee_cursor;
END $$

DELIMITER ;
```

### 游标的工作流程

1. **声明游标**：在存储过程中定义游标 `employee_cursor`，该游标执行一个 `SELECT` 查询，获取 `employees` 表中的所有记录。
   
2. **声明处理程序**：使用 `DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;` 来声明一个异常处理程序，当游标到达结果集的末尾时，会将 `done` 变量设为 `1`，从而结束循环。
   
3. **打开游标**：通过 `OPEN employee_cursor;` 打开游标，准备逐行读取数据。
   
4. **读取数据并处理**：使用 `FETCH` 语句从游标中提取数据到变量中，直到 `done` 变量被设置为 `1` 时结束循环。
   
5. **关闭游标**：完成读取操作后，通过 `CLOSE employee_cursor;` 关闭游标，释放资源。

### 详细解析

- **`DECLARE employee_cursor CURSOR FOR ...`**：定义一个游标 `employee_cursor`，其结果集为 `SELECT employee_id, first_name, last_name, salary FROM employees;`。
  
- **`DECLARE CONTINUE HANDLER FOR NOT FOUND ...`**：定义一个处理程序，处理游标读取到结果集末尾的情况，设置 `done` 变量为 `1` 以退出循环。

- **`OPEN employee_cursor`**：打开游标，使其可以读取数据。

- **`FETCH employee_cursor INTO ...`**：逐行从游标中读取数据，并将其存入之前声明的变量中。

- **`IF done = 1 THEN LEAVE read_loop;`**：检查 `done` 是否为 `1`，如果是，则退出循环（表示结果集已经读取完毕）。

- **`CLOSE employee_cursor`**：关闭游标释放资源。

### 游标的应用场景

1. **批量更新/插入**：当需要对结果集的每一行进行复杂处理并更新时，例如计算薪水、调整库存等，游标非常有用。
2. **逐行处理逻辑**：一些业务逻辑可能要求对表中的每一行数据进行检查和处理，这些操作很难通过简单的 SQL 语句实现，这时游标就派上用场了。
3. **复杂报告生成**：当需要根据表中的数据生成详细报告时，游标可以逐行访问数据，汇总和计算信息。

### 注意事项

1. **性能问题**：游标逐行处理数据，在大数据集上效率较低。如果可能，尽量使用批量操作和集合操作来代替游标。
2. **资源管理**：游标需要显式地打开和关闭，否则可能会导致资源泄露。

### 总结

- **游标** 是在 MySQL 中遍历查询结果集的一种工具，适用于存储过程中需要逐行处理数据的场景。
- 通过游标，我们可以逐行读取、处理、更新数据，适合复杂的业务逻辑和逐步操作。
- 但由于游标可能带来性能开销，在可能的情况下，应优先考虑批量操作或其他替代方法。