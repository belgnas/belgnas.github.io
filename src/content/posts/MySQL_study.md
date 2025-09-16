---
title: MySQL_学习笔记
# 文章标题(必需)
published: 2025-09-16
# 文章发布日期，格式为YYYY-MM-DD
pinned: true
# 是否置顶文章，true表示置顶
description: 这是MySQL的学习笔记，记录了我在学习MySQL过程中的一些知识和经验。
# 文章描述(必需)
tags: [MySQL]
# 文章标签数组，用于标记文章主题
category: 学习笔记
# 文章分类，用于组织文章

licenseName: "Unlicensed"
# 文章许可证名称，如"MIT"、"CC BY 4.0"等
author: qiqimora
# 文章作者姓名
draft: false
# 是否为草稿，true表示草稿，false表示正式发布
date: 2025-09-02
# 文章创建日期
pubDate: 2025-09-16
# 文章发布日期(与published类似)
series: "Markdown"
---

# MySQL

## MySQL启动/停止
```
net start mysql80
net stop mysql80
```

## MySQL客户端连接
```
客户端自带命令行
mysql [-h 127.0.0.1] [-p 3306] -u root -p
```

## SQL语句
### **1. DDL：数据定义语言**
#### 1. 查询数据库（所有/当前）
```
SHOW DATABASES;

SELECT DATABASE();
```
* 执行后，你会看到一列数据库名称，包括系统自带的 `information_schema`, `mysql`, `performance_schema` 等。
* `SELECT DATABASE();` 会显示当前正在使用的数据库。如果你刚登录 MySQL，通常会显示 `NULL`，因为还没有选择任何数据库。
--- 
#### 2. 创建数据库
```
CREATE DATABASE [IF NOT EXISTS] 数据库名称 [DEFAULT CHARSET 字符集] [COLLATE 排序规则];
```
* 执行后，你应该会看到 `Query OK, 1 row affected` 的提示，这表示数据库创建成功了。
* `IF NOT EXISTS` 是一个可选的部分，它的作用是：如果数据库已经存在，就不报错，继续执行后面的命令。
* `DEFAULT CHARSET` 和 `COLLATE` 也是可选的，分别用来指定数据库的默认字符集和排序规则。如果不指定，MySQL 会使用默认设置，通常是 `utf8mb4` 字符集和 `utf8mb4_general_ci` 排序规则。
---
#### 3. 使用数据库
```
USE 数据库名称;
```
* 这个命令告诉 MySQL：从现在开始，我要操作哪个数据库了。
* 执行后，你应该会看到 `Database changed` 的提示。这意味着你已经"进入"了learning_db，接下来所有的操作都将在这个数据库里进行。
---
#### 4. 删除数据库
```
DROP DATABASE [IF EXISTS] 数据库名称;
```
* `IF EXISTS` 是一个可选的部分，它的作用是：如果数据库不存在，就不报错，继续执行后面的命令。
* 执行后，你会看到 `Query OK, 0 rows affected` 的提示，这表示数据库删除成功了。
---
#### 5. 创建数据表
```
CREATE TABLE 表名称 (
    列名称1 数据类型1 [COMMENT '列注释'],
    列名称2 数据类型2 [COMMENT '列注释'],
    ...
); COMMENT '表注释';
```
例子：创建一个 users (用户) 表，用来存放用户信息。这个表将有3个列：id, name, email。
```
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT COMMENT '用户ID',
    name VARCHAR(50) COMMENT '用户名',
    email VARCHAR(100) COMMENT '用户邮箱'
); COMMENT '用户表';
```
* 执行成功后，你会看到 `Query OK, 0 rows affected` 的提示。
*  命令详解 (非常重要)(逐行解释):
* `CREATE TABLE users (...)`: 这是创建表的命令，表名叫 users。括号里是表的结构定义。
* `COMMENT '注释'`: 这是一个可选的部分，它的作用是：给表和列添加注释，方便自己和他人理解。
* `id INT PRIMARY KEY AUTO_INCREMENT`:
    * id INT: 创建一个名为 id 的列，它的数据类型是 INT (整数)。
    * PRIMARY KEY: 把 id 列设置成 主键。主键是每一行数据的唯一标识，不能重复，非常重要！
    * AUTO_INCREMENT: 表示 id 的值会自动增长。当我们插入新数据时，MySQL 会自动给它分配一个不重复的数字 (1, 2, 3, ...)，我们不用自己管。
* `name VARCHAR(50)`:  
    * 创建一个名为 name 的列，数据类型是 VARCHAR(50)。VARCHAR 表示可变长度的字符串，50
        代表这个名字最长不能超过50个字符。
    * COMMENT '用户名': 给 name 列添加注释，说明它的作用是存储用户名。
* `email VARCHAR(100)`:
    * 同理，创建一个 email 列，最长100个字符。
    * COMMENT '用户邮箱': 给 email 列添加注释，说明它的作用是存储用户邮箱。
* `);`: 结束整个 CREATE TABLE 命令。
---
#### 6. 查询数据表(所有)
```
SHOW TABLES;
```
* 执行后，你会看到一列数据表名称，包括我们刚刚创建的 users。
---
#### 7. 查看数据表结构
```
DESCRIBE 表名称;
```
* `DESCRIBE` (可以简写为 DESC) 命令会显示表的列、数据类型等信息，非常有用。
---
#### 8. 查询指定表的建表语句
```
SHOW CREATE TABLE 表名称;
```
* 执行后，你会看到一个 SQL 语句，它就是用来创建这个表的。
* 这个语句包含了表的名称、列的定义、主键、索引等信息。
* 你可以把这个语句复制下来，用来备份表结构，或者在其他数据库中创建相同的表。
---
#### 9. 添加表结构
```
ALTER TABLE 表名称 ADD 列名称 数据类型(长度) [COMMENT '列注释'] [约束];
```
例子：向 users 表添加一个 age 列，用来存储用户年龄。
```
ALTER TABLE users ADD age INT(3) COMMENT '用户年龄';
```
* 执行成功后，你会看到 `Query OK, 0 rows affected` 的提示。
*  命令详解:
* `ALTER TABLE users`: 告诉 MySQL 我们要操作 users 表。
* `ADD age INT`: 添加一个名为 age 的列，数据类型是 INT (整数)。
* `INT(3)`: 表示 age 列的数据类型是 INT (整数)，长度为3。
    * 长度为3 意味着 age 列最多只能存储3位数字，比如 123, 999。
    * 超出长度的数字会被截断，比如 1234 会变成 123。
* `COMMENT '用户年龄'`: 给 age 列添加注释，说明它的作用是存储用户年龄。
  * [约束] (可选)：
    * 约束是用来限制列中数据的规则，确保数据的完整性和一致性。
    * 比如：NOT NULL (不能为空)、UNIQUE (唯一)、PRIMARY KEY (主键)、FOREIGN KEY (外键) 等。
    * `NOT NULL`: 表示这个列的值不能为空。
    * `UNIQUE`: 表示这个列的值必须是唯一的，不能重复。
    * `PRIMARY KEY`: 表示这个列是主键，唯一标识每一行数据。
    * `FOREIGN KEY`: 表示这个列是外键，引用了其他表的主键。
---
#### 10. 修改表结构
修改数据类型
```
ALTER TABLE 表名称 MODIFY 列名称 数据类型(长度);
```
例子：修改 users 表的 age 列，将数据类型改为 INT(4)
```
ALTER TABLE users MODIFY age INT(4);
```
修改列名和数据类型
```
ALTER TABLE 表名称 CHANGE 旧列名称 新列名称 数据类型(长度) [COMMENT '列注释'] [约束];
```
例子：修改 users 表的 age 列，将列名改为 age_new，数据类型改为 INT(4)
```
ALTER TABLE users CHANGE age age_new INT(4);
```
* 命令详解:
    * `ALTER TABLE users`: 告诉 MySQL 我们要操作 users 表。
    * `MODIFY age INT(4)`: 修改 age 列的数据类型为 INT(4)。
    * `COMMENT '用户年龄'`: 给 age 列添加注释，说明它的作用是存储用户年龄。
---
#### 11. 删除表
```
ALTER TABLE 表名称 DROP 列名称;
```
例子：删除 users 表的 age_new 列
```
ALTER TABLE users DROP age_new;
```
* 命令详解:
    * `ALTER TABLE users`: 告诉 MySQL 我们要操作 users 表。
    * `DROP age_new`: 删除 age_new 列。
---
#### 12. 删除指定表，并重建该表
```
TRUNCATE TABLE 表名称;
```
例子：删除 users 表的所有数据
```
TRUNCATE TABLE users;
```
* 命令详解:
    * `TRUNCATE TABLE users`: 告诉 MySQL 我们要删除 users 表的所有数据。
    * 这会删除表中的所有数据，但是表结构不会改变。
---

### **2. DML：数据操作语言**
#### 13. 插入数据
```
1. 给指定的列插入数据
INSERT INTO 表名称 (列1, 列2, ...) VALUES (值1, 值2, ...);

2. 给所有列插入数据
INSERT INTO 表名称 VALUES (值1, 值2, ...);

3. 批量添加数据
INSERT INTO 表名称 (列1, 列2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), ...;
或
INSERT INTO 表名称 VALUES (值1, 值2, ...), (值1, 值2, ...), ...;
```
例子：向 users 表插入一条用户数据（姓名，邮箱）
```
INSERT INTO users (name, email) VALUES ('Alice', 'alice@example.com');
```
* 成功后，你会看到 `Query OK, 1 row affected` 的提示。
*  命令详解:
* `INSERT INTO users`: 告诉 MySQL 我们要在 users 表中插入数据。
* `(name, email)`: 指定我们要为哪些列提供值。
* `VALUES ('Alice', 'alice@example.com')`: 提供与前面列对应的具体值。
    * 字符串（如名字和邮箱）在 SQL 中需要用 单引号 ' ' 括起来。
    * 值的顺序必须和列的顺序一一对应 (name -> 'Alice', email -> 'alice@example.com')。

    思考题： 注意到我们没有插入 id 吗？这是为什么呢？  
    答：因为我们在创建表时把 id 设置成了 AUTO_INCREMENT，MySQL 会自动为我们生成一个独一无二的id！
---
#### 14. 更新数据 (UPDATE)
 * !! 超级重要警告 !!  
   UPDATE 是一个“危险”的命令！如果你在 UPDATE 时忘记使用 WHERE 子句，它会把 整个表 的所有记录都更新掉！所以，使用 UPDATE 时，WHERE 几乎总是必需的。
 ```
 1. 给指定的列更新数据
 UPDATE 表名称 SET 列1 = 值1, 列2 = 值2, ... [WHERE 条件];

 2. 对所有列更新数据
 UPDATE 表名称 SET 列1 = 值1, 列2 = 值2, ...;
 ```
 例子： id 为 4 的那个 Alice，想把她的邮箱 alice_2@work.com 改成 new_alice@example.com。
 ```
 UPDATE users SET email = 'new_alice@example.com' WHERE id = 4;
 ```
 * 命令详解:
    * `UPDATE users`: 告诉 MySQL 我们要更新 users 表。
    * `SET email = 'new_alice@example.com'`: 把 email 这一列的值，设置 为 'new_alice@example.com'。如果要同时更新多列，可以用逗号隔开，例如 `SET name = 'new_name', email = 'new_email'`。
    * `WHERE id = 4`: 只 对 id 等于 4 的那一行执行 SET 操作。
 ---
#### 15. 删除数据 (DELETE)
  * !!! 超级重要警告 !!!  
    比 `UPDATE` 更危险的警告：  
    DELETE 命令是 不可逆 的！如果你执行 DELETE 时忘记了 WHERE 子句，它会 删除表中的所有数据，而且通常无法恢复！所以，请务必、一定、时刻带着 WHERE 使用 DELETE！
  ```
  1. 给指定的列删除数据
  DELETE FROM 表名称 [WHERE 条件];

  2. 对所有列删除数据
  DELETE FROM 表名称;
  ```
  例子：删除 name 为 'Charlie' 的用户
  ```
  DELETE FROM users WHERE name = 'Charlie';
  ```
  * 命令详解:
  * `DELETE FROM users`: 告诉 MySQL 我们要从 users 表中删除数据。
  * `WHERE name = 'Charlie'`: 只 删除 name 等于 'Charlie' 的那一行。
---

### **3. DQL：数据查询语言**
#### ps：DQL语法总览
```
SELECT 列名称 FROM 表名称 WHERE 条件 GROUP BY 分组后列名称 HAVING 分组后条件 ORDER BY 排序 LIMIT 分类参数
```
#### 16. 查询数据(SELECT)
```
1. 查询多个字段
SELECT 列名称1, 列名称2... FROM 表名;
SELECT * FROM 表名称;

2. 设置别名查询
SELECT 列名称1 [AS '别名1'], 列名称2 [AS '别名2']... FROM 表名;
ps：在这里面"AS"关键字是可以省略的

3. 去除重复记录查询
SELECT DISTINCT 列名称 FROM 表名;

```
例子： 查询 users 表中的所有数据
```
SELECT * FROM users;
```
* SELECT 是 SQL 的灵魂，我们用它来从表中“选择”我们想看的数据。
* 最简单的查询就是查看一个表里的 所有 数据。我们将使用 * (星号)，它是一个通配符，代表“所有列”。
*  命令详解:
* `SELECT *`: 选择所有列。
* `FROM users`: 从 users 表中。
---
#### 17. 带条件的查询 (WHERE 子句)
```
SELECT * FROM 表名称 WHERE 条件;
```
例子：查询 users 表中 name 是 'Bob' 的用户
```
SELECT * FROM users WHERE name = 'Bob';
```
* WHERE 子句让我们可以添加过滤条件，只返回符合条件的行。
* WHERE后可填入的条件：
  * 等于：`=`
  * 不等于：`!=` 或 `<>`
  * 大于：`>`
  * 小于：`<`
  * 大于等于：`>=`
  * 小于等于：`<=`
  * 范围：`BETWEEN 值1 AND 值2`
  * 包含：`IN (值1, 值2, ...)`
  * 不包含：`NOT IN (值1, 值2, ...)`
  * 空值：`IS NULL` 或 `IS NOT NULL`
  * 模糊查询：`LIKE`
    * 通配符：
      * `%`：匹配任意字符（包括零个字符）
      * `_`：匹配单个字符
    * 示例：
      * `%a%`：匹配包含 'a' 的任意字符串
      * `_a%`：匹配第二个字符是 'a' 的字符串
      * `a__`：匹配前两个字符是 'a' 的字符串
  * 逻辑运算符：
    * `AND`或`&&`：并且（多个条件同时满足）
    * `OR`或`||`：或者（多个条件满足任意一个）
    * `NOT`或`!`：非，不是（取反）
    * 
* 命令详解:
  * `SELECT * FROM users`: 这部分你已经很熟了，就是从 users 表中选择所有列。
  * `WHERE name = 'Bob'`: 这就是过滤条件。它告诉 MySQL：“只把那些 name 列的值 等于 'Bob' 的行给我”。
    * 注意，在 SQL 中，判断相等用的是单个等号 =。

---
#### 18. 聚合查询
ps: 聚合 就是将一列数据作为一个整体，进行纵向计算。
```
SELECT 聚合函数(列名称) FROM 表名称;
```
例子：查询 users 表中所有用户的平均年龄
```
SELECT AVG(age) FROM users;
```
* 常见的聚合函数有：
  * `COUNT()`：计算行数
  * `SUM()`：计算总和
  * `AVG()`：计算平均值
  * `MIN()`：计算最小值
  * `MAX()`：计算最大值
* 命令详解:
  * `SELECT AVG(age) FROM users`: 这部分你已经很熟了，就是从 users 表中选择 age 这一列。
  * `AVG(age)`: 这就是聚合函数。它告诉 MySQL：“请计算 age 这一列的平均值”。
* 注意：
  * 聚合函数会忽略 NULL 值。
---
#### 19. 分组查询(GROUP BY)
```
SELECT 列名称 FROM 表名称 [WHERE 条件] GROUP BY 列名称 [HAVING 分组后过滤的条件];
```
例子：查询 users 表中每个 age 分组的用户数量
```
SELECT age, COUNT(*) FROM users GROUP BY age HAVING COUNT(*) > 1;
```
* where 和 having 的区别
  * 执行时机不同：where 是在分组之前进行过滤，不满足 where 条件，不参与分组，而 having 是在分组之后对结果进行过滤。
  * 判断条件不同：where 不能对聚合函数进行过滤，而 having 可以。
---
#### 20. 排序查询(ORDER BY)
```
SELECT 列名称列表 FROM 表名称 ORDER BY 列名称1 排序方式1, 列名称2 排序方式2...;
```
例子：按名字的字母顺序，查询所有用户
```
SELECT name, age FROM users ORDER BY name ASC;
```
* `ORDER BY` 子句让我们可以对查询结果进行排序。
* `ASC` 表示升序 (从A到Z, 从小到大)，是默认选项，可以省略。
* `DESC` 表示降序 (从Z到A, 从大到小)，不是默认选项，不可以省略。
---
#### 21. 分页查询(LIMIT)
```
SELECT 列名称列表 FROM 表名称 LIMIT 起始索引, 查询记录数;
```
例子：查询 users 表中前 5 条数据
```
SELECT * FROM users LIMIT 5;
```
* `LIMIT` 子句让我们可以限制查询结果的数量。
* 命令详解:
  * `SELECT * FROM users`: 这部分你已经很熟了，就是从 users 表中选择所有列。
  * `LIMIT 5`: 这就是分页参数。它告诉 MySQL：“只返回前 5 条记录”。
* 注意：
  * `LIMIT` 子句的参数是从 0 开始的，所以 `LIMIT 5` 表示返回第 0 条到第 4 条记录。
  * 你可以使用 `LIMIT` 子句来实现分页功能。`LIMIT 偏移量, 数量`
  * 偏移量：从第几条记录开始返回
  * 数量：返回几条记录
  * 例如：`LIMIT 10, 5` 表示返回第 10 条到第 14 条记录

### **4. DCL：数据控制语言**
#### 22. 用户管理
1. 查询用户
```
USE mysql;
SELECT * FROM user;
```
2. 创建用户
```
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
```
3. 修改用户密码
```
ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码';
```
4. 删除用户
```
DROP USER '用户名'@'主机名';
```
例子：
```
-- 创建用户
CREATE USER 'belgnas'@'localhost' IDENTIFIED BY '123456';
-- 修改用户密码
ALTER USER 'belgnas'@'localhost' IDENTIFIED WITH mysql_native_password BY '654321';
-- 删除用户
DROP USER 'belgnas'@'localhost';
```
---
#### 23. 权限管理
1. 查询权限
```
SHOW GRANTS FOR '用户名'@'主机名';
```
2. 授予权限
```
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';
```
3. 撤销权限
```
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
```
* 常用权限：
  * `ALL`/`ALL PRIVILEGES`：所有权限
  * `SELECT`：查询权限
  * `INSERT`：插入权限
  * `UPDATE`：更新权限
  * `DELETE`：删除权限
  * `CREATE`：创建权限（数据库、表）
  * `DROP`：删除权限（数据库、表、视图）
  * `ALTER`：修改权限（表）


✦ 祝贺你！你已经成功完成了 SQL 的入门速成课，并且亲手实践了所有核心操作！

  你从一个完全的初学者，到现在已经可以独立完成对数据的增、删、改、查，表现得非常出色。

  ---

  我们来回顾一下你已掌握的技能：

  核心概念:
   * 理解了数据库 (Database), 表 (Table), 列 (Column), 行 (Row) 的关系。
   * 知道了 PRIMARY KEY (主键) 和 AUTO_INCREMENT (自增) 的重要性。

  SQL 命令:
   * 数据库层面:
       * `SHOW DATABASES`; - 查看所有数据库
       * `CREATE DATABASE ...`; - 创建新数据库
       * `USE ...`; - 选择要操作的数据库
   * 表层面:
       * `CREATE TABLE ...`; - 按规则创建新表
       * `DESCRIBE ...`; - 查看表结构
   * 数据层面 (CRUD):
       * `INSERT INTO ... VALUES ...`; - 增加新数据 (单行和多行)
       * `SELECT ... FROM ...`; - 查询数据
       * `UPDATE ... SET ... WHERE ...`; - 改动现有数据
       * `DELETE FROM ... WHERE ...`; - 删除数据
   * 查询逻辑:
       * `WHERE` - 添加过滤条件
       * `AND` / `OR` - 组合多个过滤条件
---

## 函数
### 1. 字符串函数
通用语法：SELECT 函数名(参数列表)

-- 字符串拼接，将S1，S2，..Sn拼接成一个字符串
```
CONCAT(S1,S2....Sn)
```
例子：
```
SELECT CONCAT('hello', 'mysql');
```
输出结果：hello mysql

-- 将字符串str全部转为小写
```
LOWER(str)
```
例子：
```
SELECT LOWER('HELLO');
```
输出结果：hello

-- 将字符串str全部转为大写
```
UPPER(str)
```
例子：
```
SELECT UPPER('hello');
```
输出结果：HELLO

-- 左填充，用字符串pad对str的左边进行填充，达到n个字符串长度
```
LPAD(str,n,pad)
```
例子：
```
SELECT LPAD('hello', 10, '*');
```
输出结果：****hello

-- 右填充，用字符串pad对str的右边进行填充，达到n个字符串长度
```
RPAD(str,n,pad)
```
例子：
```
SELECT RPAD('hello', 10, '*');
```
输出结果：hello****

-- 去掉字符串头部和尾部的空格
```
TRIM(str)
```
例子：
```
SELECT TRIM('  hello mysql  ');
```
输出结果：hello mysql

-- 返回从字符串str从start位置起的len个长度的字符串
```
SUBSTRING(str,start,len)

```
* ps:字符串的索引`从1开始`
例子：
```
SELECT SUBSTRING('hello', 1, 3);
```
输出结果：hel

-- 替换字符串str中的子字符串 substr1 为 substr2 
```
REPLACE(str,substr1,substr2)
```
例子：
```
SELECT REPLACE('hello', 'l', 'L');
```
输出结果：heLLo

---

### 2. 数值函数
-- 向上取整
```
CEILING(x)
```
例子：
```
SELECT CEILING(2.3);
```
输出结果：3

-- 向下取整
```
FLOOR(x)
```
例子：
```
SELECT FLOOR(2.3);
```
输出结果：2

-- 返回x/y的模
```
MOD(x,y)
```
例子：
```
SELECT MOD(10, 3);
```
输出结果：1

-- 返回0~1的随机数
```
RAND()
```
例子：
```
SELECT RAND();
```
输出结果：0.5577222222222222（随机数）

-- 四舍五入
```
ROUND(x)
```
例子：
```
SELECT ROUND(2.3);
```
输出结果：2

-- 取绝对值
```
ABS(x)
```
例子：
```
SELECT ABS(-2);
```
输出结果：2

-- 取平方根
```
SQRT(x)
```
例子：
```
SELECT SQRT(9);
```
输出结果：3

-- 取x的n次方
```
POWER(x,n)
```
例子：
```
SELECT POWER(2, 3);
```
输出结果：8

案例实现：生成一个六位数的随机验证码

```
SELECT LPAD(ROUND(RAND() * 1000000), 6, '0');
```
输出结果：123456（随机数）
* 解释：
  * `ROUND(RAND() * 1000000)`：生成一个0到1000000之间的随机数，然后四舍五入取整。
  * `LPAD(..., 6, '0')`：将生成的随机数左填充0，确保总长度为6位。
---

### 3. 日期函数
常见日期函数：

-- 返回当前日期
```
CURDATE()
```
例子：
```
SELECT CURDATE();
```
输出结果：2025-09-16（当前日期）

-- 返回当前时间
```
CURTIME()
```
例子：
```
SELECT CURTIME();
```
输出结果：00:37:56（当前时间）

-- 返回当前日期和时间
```
NOW()
```
例子：
```
SELECT NOW();
```
输出结果：2025-09-16 00:37:56（当前日期和时间）

-- 获取指定date的年份
```
YEAR(date)
```
例子：
```
SELECT YEAR('2025-09-16');
```
输出结果：2025

-- 获取指定date的月份
```
MONTH(date)
```
例子：
```
SELECT MONTH('2025-09-16');
```
输出结果：9

-- 获取指定date的日期
```
DAY(date)
```
例子：
```
SELECT DAY('2025-09-16');
```
输出结果：16

-- 返回一个日期/时间值加上指定的时间间隔expr后的时间值
```
DATE_ADD(date,INTERVAL expr type)
```
例子：
```
SELECT DATE_ADD('2025-09-16', INTERVAL 1 DAY);
```
输出结果：2025-09-17（当前日期加1天）

-- 计算两个日期（起始时间date1和结束时间date2）之间的天数
```
DATEDIFF(date1,date2)
```
例子：
```
SELECT DATEDIFF('2025-09-16', '2025-09-15');
```
输出结果：1（相差1天）

案例：
-- 计算员工入职时间到当前时间的天数
```
SELECT DATEDIFF(NOW(), '2025-09-16');
```
输出结果：1（相差1天）
* 解释：
  * `NOW()`：返回当前日期和时间。
  * `'2025-09-16'`：员工入职时间。
  * `DATEDIFF(NOW(), '2025-09-16')`：计算当前日期和时间与员工入职时间之间的天数差。
--- 

### 4. 流程函数
流程函数可以在SQL语句中实现条件筛选，从而提高语句的效率

-- 如果value为true，返回t，否则返回f
```
IF(value, t, f)
```
例子：
```
SELECT IF(1 > 0, '是', '否');
```
输出结果：是

* 解释：
  * `IF(1 > 0, '是', '否')`：如果条件为真（1 > 0），则返回表达式1（'是'）；否则返回表达式2（'否'）。

-- 如果value1为不为空，返回value1，否则返回value2
```
IFNULL(value1, value2)
```
例子：
```
SELECT IFNULL(NULL, '默认值');
```
输出结果：默认值
* 解释：
  * `IFNULL(NULL, '默认值')`：如果value为NULL，则返回表达式2（'默认值'）；否则返回value。

-- 如果value1为true，返回value1，……，否则返回default默认值
```
CASE
WHEN [value1] THEN [result1]
WHEN [value2] THEN [result2]
ELSE [default]
END
```
* 解释：
  * `CASE`：用于条件判断，类似于`IF`语句。
  * `WHEN [value1] THEN [result1]`：如果条件为真（value1），则返回结果（result1）。
  * `WHEN [value2] THEN [result2]`：如果条件为真（value2），则返回结果（result2）。
  * `ELSE [default]`：如果以上条件都不满足，则返回默认值（default）。
  * `END`：结束`CASE`语句。

-- 如果expr的值等于value1，返回result1，……，否则返回default默认值
```
CASE [expr]
WHEN [value1] THEN [result1]
WHEN [value2] THEN [result2]
ELSE [default]
END
```
* 解释：
  * `CASE [expr]`：根据表达式（expr）的值进行判断。
  * `WHEN [value1] THEN [result1]`：如果表达式的值等于value1，则返回结果（result1）。
  * `WHEN [value2] THEN [result2]`：如果表达式的值等于value2，则返回结果（result2）。
  * `ELSE [default]`：如果以上条件都不满足，则返回默认值（default）。
  * `END`：结束`CASE`语句。


-- 案例：根据员工的年龄，返回不同的等级（假定已经有一个数据库）
```
SELECT
    name,
    age,
    CASE
        WHEN age < 18 THEN '未成年'
        WHEN age >= 18 AND age < 30 THEN '青年'
        WHEN age >= 30 AND age < 50 THEN '中年'
        ELSE '老年'
    END AS age_group
FROM
    employees;
```
* 解释：
  * `CASE`：用于条件判断，类似于`IF`语句。
  * `WHEN [age] < 18 THEN '未成年'`：如果年龄小于18岁，则返回'未成年'。
  * `WHEN [age] >= 18 AND [age] < 30 THEN '青年'`：如果年龄大于等于18岁且小于30岁，则返回'青年'。
  * `WHEN [age] >= 30 AND [age] < 50 THEN '中年'`：如果年龄大于等于30岁且小于50岁，则返回'中年'。
  * `ELSE '老年'`：如果以上条件都不满足，则返回'老年'。
  * `END AS age_group`：结束`CASE`语句，并将结果命名为age_group。
---

## 约束











# 补充篇

## SQL分类
```
DDL: 数据定义语言，用来定义数据库对象（数据库、表、字段）
DML: 数据操作语言，用来对数据库表中的数据进行增删改
DQL: 数据查询语言，用来查询数据库中表的记录
DCL: 数据控制语言，用来创建数据库用户、控制数据库的控制权限
```

## MySQL数值类型

| 分类     | 类型           | 大小  | 有符号范围                                              | 无符号范围                      | 描述                             |
|----------|----------------|-------|---------------------------------------------------------|---------------------------------|----------------------------------|
| 整数类型 | TINYINT        | 1字节 | -128 到 127                                             | 0 到 255                        | 小整数                           |
| 整数类型 | SMALLINT      | 2字节 | -32,768 到 32,767                                       | 0 到 65,535                     | 小整数                           |
| 整数类型 | MEDIUMINT      | 3字节 | -8,388,608 到 8,388,607                                 | 0 到 16,777,215                 | 中等整数                         |
| 整数类型 | INT 或 INTEGER | 4字节 | -2,147,483,648 到 2,147,483,647                         | 0 到 4,294,967,295              | 标准整数                         |
| 整数类型 | BIGINT         | 8字节 | -9,223,372,036,854,775,808 到 9,223,372,036,854,775,807 | 0 到 18,446,744,073,709,551,615 | 大整数                           |
| 浮点类型 | FLOAT          | 4字节 | -3.402823466E+38 到 3.402823466E+38                     | 0 到 3.402823466E+38            | 单精度浮点数                     |
| 浮点类型 | DOUBLE 或 REAL | 8字节 | -1.7976931348623157E+308 到 1.7976931348623157E+308     | 0 到 1.7976931348623157E+308    | 双精度浮点数                     |
| 浮点类型 | DECIMAL(M,D)   | 可变  | -10^M-1 到 10^M-1                                       | 0 到 10^M-1                     | 精确小数，M是总位数，D是小数位数 |

## MySQL字符串类型

| 分类       | 类型       | 大小             | 描述                            |
|------------|------------|------------------|---------------------------------|
| 字符串类型 | CHAR(M)    | 0-255字节        | 固定长度字符串，M是字符数       |
| 字符串类型 | VARCHAR(M) | 0-65535字节      | 可变长度字符串，M是字符数       |
| 字符串类型 | TINYBLOB   | 0-255字节        | 不超过255个字符的短二进制字符串 |
| 字符串类型 | TINYTEXT   | 0-255字节        | 短文本字符串                    |
| 字符串类型 | BLOB       | 0-65535字节      | 长二进制字符串                  |
| 字符串类型 | TEXT       | 0-65535字节      | 长文本字符串                    |
| 字符串类型 | MEDIUMBLOB | 0-16777215字节   | 中等长二进制字符串              |
| 字符串类型 | MEDIUMTEXT | 0-16777215字节   | 中等长文本字符串                |
| 字符串类型 | LONGBLOB   | 0-4294967295字节 | 非常长的二进制字符串            |
| 字符串类型 | LONGTEXT   | 0-4294967295字节 | 非常长的文本字符串              |

## 日期和时间类型

| 分类     | 类型      | 大小  | 范围                                               | 格式                | 描述                    |
|----------|-----------|-------|----------------------------------------------------|---------------------|-------------------------|
| 日期类型 | DATE      | 3字节 | 1000-01-01 到 9999-12-31                           | YYYY-MM-DD          | 日期值                  |
| 日期类型 | TIME      | 3字节 | -838:59:59 到 838:59:59                            | HH:MM:SS            | 时间值或时间间隔        |
| 日期类型 | YEAR      | 1字节 | 1901 到 2155                                       | YYYY                | 年份值                  |
| 日期类型 | DATETIME  | 8字节 | 1000-01-01 00:00:00 到 9999-12-31 23:59:59         | YYYY-MM-DD HH:MM:SS | 混合日期和时间值        |
| 日期类型 | TIMESTAMP | 4字节 | 1970-01-01 00:00:01 UTC 到 2038-01-19 03:14:07 UTC | YYYY-MM-DD HH:MM:SS | 混合日期和时间值 时间戳 |
