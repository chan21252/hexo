---
title: Mysql基础学习笔记
date: 2019-03-23 09:54:45
tags:
- mysql
category:
- mysql
---

## SQL
### 定义
结构化查询语言(Structured Query Language)简称SQL，是一种特殊目的的编程语言，是一种数据库查询和程序设计语言，用于存取数据以及查询、更新和管理关系数据库系统；同时也是数据库脚本文件的扩展名。

结构化查询语言是高级的非过程化编程语言，允许用户在高层数据结构上工作。它不要求用户指定对数据的存放方法，也不需要用户了解具体的数据存放方式，所以具有完全不同底层结构的不同数据库系统, 可以使用相同的结构化查询语言作为数据输入与管理的接口。结构化查询语言语句可以嵌套，这使它具有极大的灵活性和强大的功能。

<!-- more -->

### 语法
1. SQL语句可以单行或多行书写，以分号结尾。
2. 使用空格和缩进来增加可读性。
3. mysql的SQL语句不区分大小写，但是建议关键字使用大小写。
4. 注释
    - 单行注释 -- 注释内容，# 注释内容
    - 多行注释 /\* 注释内容 \*/

### SQL分类
1. DDL
    数据定义语言，用来定义数据库对象：数据库，表，列等，关键字CRTEATE, DROP, ALTER 等。
2. DML
    数据操作语言，用来对数据表的数据记录进行增删改，关键字INSERT, DELETE, UPDATE 等。
3. DQL
    数据查询语言，用来对数据表的数据记录进行查询，关键字SELECT, WHERE 等。
4. DCL
    数据控制语言，用来定义数据库的访问权限和安全级别，创建用户。关键字GRANT, REVOKE 等。

### DDL：操作数据库表
#### 操作数据库：CURD
1. create
2. retrieve：查询
3. update
4. delete
5. 使用数据库

``` sql
/* 查询数据库 */
show databases;                     # 查询所有数据库名称
SHOW CREATE DATABASE [数据库名称];   # 查询数据库创建语句

/* 创建数据库 */
CREATE DATABASE IF NOT EXISTS [数据库名称];
CREATE DATABASE [数据库名称] CHARACTER SET GBK;
create database [数据库名称] if not exists character set [字符集];

/* 修改数据库 */
alter database [数据库名称] character set utf8;      修改字符集

/* 删除数据库 */
drop database if exists [数据库名称];

/* 使用数据库 */
SELECT DATABASE();      # 查询当前使用的数据库名称
USE [数据库名称];
```

#### 操作数据表

``` sql
/* 查询表 */
show tables;      # 查询数据库中所有的表
desc [表名];      # 查询表结构

/* 创建表 */
create table [表名](
    列名1 数据类型1,
    列名2 数据类型2,
    ...
    列名n 数据类型n
);
# 最后一列不需要加逗号（,）

create table [复制的表名] like [源表名];    # 创建一张和其他表结构一致的表
/* 删除表 */
drop if exists [表名];

/* 修改表 */
# 修改表名
alter table [表名] rename to [新表名];

# 修改表的字符集
show create table [表名];
alter table [表名] character set [utf8];

# 新增一列
alter table [表名] add [列名] [数据类型];

# 修改列名称，数据类型
alter table [表名] change [列名] [新列名] [数据类型];
alter table [表名] modify [列名] [新数据类型];

# 删除列
alert table [表名] drop [列名];
alert table [表名] drop if exists [列名];
```

创建表时用到的SQL数据类型：[http://www.w3school.com.cn/sql/sql_datatypes.asp](http://www.w3school.com.cn/sql/sql_datatypes.asp)

``` sql
-- 练习 --
USE db1;

CREATE TABLE student(
	id INT PRIMARY KEY NOT NULL UNIQUE,
	stu_name VARCHAR(20) NOT NULL,
	age INT NOT NULL DEFAULT 18,
	sex VARCHAR(4) NOT NULL
);

CREATE TABLE student1 LIKE student;
SHOW TABLES;
SHOW CREATE TABLE student;
DESC student;

ALTER TABLE student1 RENAME TO student2;
ALTER TABLE student CHARACTER SET utf8;
ALTER TABLE student CHANGE sex gender BOOLEAN;
ALTER TABLE student MODIFY gender VARCHAR(4);
ALTER TABLE student ADD address VARCHAR(255);
ALTER TABLE student DROP address;

DROP TABLE IF EXISTS student2;
```

### DML：增删改数据记录

``` sql
--- 添加记录 ---
INSERT INTO [表名] (列1, 列2, ..., 列n) VALUES (值1, 值2, ..., 值n);

--- 删除记录 ---
DROP FROM [表名] WHERE 列名=值;      # 如果不加WHERE条件，则删除表中所有数据！！！
truncate [表名];                    #删除表，并创建一张一样的表

--- 修改记录 ---
UPDATE [表名] SET 列名1=值1, 列名2=值2 WHERE 列名=值;  # 如果不加WHERE条件，则修改表中所有数据！！！
```

``` sql
--- 练习 ---
INSERT INTO student (id, stu_name, age, gender) VALUES (1, '张无忌', 18, '男');
INSERT INTO student (id, stu_name, age, gender) VALUES (2, '赵敏', 18, '女');
INSERT INTO student (id, stu_name, age, gender) VALUES (3, '宋青书', 18, '男');
SELECT * FROM student;
DELETE FROM student WHERE id=3;
UPDATE student SET age=15 WHERE id=2;
TRUNCATE student;
```

### DQL：查询数据记录
#### 语法：
``` sql
    select
        字段列表
    from
        表名列表
    where
        条件列表
    group by
        分组字段
    having
        分组之后的条件限定
    order by
        排序
    limit
        分页限定
```

#### 基础查询

``` sql
SELECT * FROM student;
SELECT name, address FROM student;
SELECT distinct address FROM student;   -- 去除重复查询结果
SELECT distinct name, address FROM student;
SELECT math + english FROM student;     -- 计算列
SELECT english as en FROM student;      -- 起别名
```
#### 条件查询
1. where 后面跟查询条件
2. 条件运算符
    - \>、<、>=、<=、=、<>
    - BETWEEN...AND...
    - IN (集合)
    - LIKE：模糊查询
        - 占位符：
            - _：单个任意字符
            - %：多个任意字符
    - IS NULL/IS NOT NULL
    - AND 或 &&
    - OR 或 ||
    - NOT 或 !


#### 高级查询
1. 排序查询
    语法：ORDER BY 排序字段1 排序方式1, 排序字段2 排序方式2, 排序字段3 排序方式3, ...;
    升序 ASC,降序 DESC，缺省 ASC。
    如果有多个排序条件，前面的值一样才会比较下一个值。
2. 聚合函数
    - COUNT
    - MAX
    - MIN
    - SUM
    - AVG
    聚合函数计算会排除null值； 解决方案：IFNULL函数。
3. 分组查询
    语法：GROUP BY 分组字段;
    分组查询的字段：分组字段、聚合函数。
    where和having的区别：
        - where在分组前限定，不满足不参与分组。having在分组后限定，不满足不被查询。
        - where后不可以跟聚合函数，having可以。
4. 分页查询
    语法：LIMIT 开始的索引，每页显示的条数。
    公式：每页开始的索引 = (当前页码 - 1) * 每页的条数。
    不同的数据库对分页操作的实现可能不同，LIMIT适用于mysql。

``` sql
--- 练习 ---
drop table if exists student;
create table student(
	id int,
	name varchar(20),
	sex varchar(20),
	age int,
	address VARCHAR(20),
	math int,
	english int
);

INSERT INTO `student` (`id`, `name`, `sex`, `age`, `address`, `math`, `english`) VALUES('1','赵云','男','18','蜀','99','98');
INSERT INTO `student` (`id`, `name`, `sex`, `age`, `address`, `math`, `english`) VALUES('2','诸葛亮','男','22','蜀','100','88');
INSERT INTO `student` (`id`, `name`, `sex`, `age`, `address`, `math`, `english`) VALUES('3','周瑜','男','18','吴','95','90');
INSERT INTO `student` (`id`, `name`, `sex`, `age`, `address`, `math`, `english`) VALUES('4','孙尚香','女','15','吴','88','90');
INSERT INTO `student` (`id`, `name`, `sex`, `age`, `address`, `math`, `english`) VALUES('5','典韦','男','17','魏','84','63');
INSERT INTO `student` (`id`, `name`, `sex`, `age`, `address`, `math`, `english`) VALUES('6','貂蝉','女','14','汉朝','72','100');

SELECT * FROM student;
SELECT * FROM student ORDER BY age DESC, math, english;
SELECT COUNT(id) FROM student WHERE address='蜀';
SELECT COUNT(id) count_id, AVG(english) avg_en, address FROM student WHERE english>70 GROUP BY address HAVING COUNT(id)>1;

# 每页显示三条记录
SELECT * FROM student LIMIT 0,3;    -- 第1页
SELECT * FROM student LIMIT 3,3;    -- 第2页

```

### 表的约束

概念：对表中的数据进行限定，保证数据的正确性，有效性，完整性。

分类：
1. 主键约束：primary key
2. 非空约束：not null
3. 唯一约束：unique
4. 外键约束：foreign key

非空约束：
- 创建表时候添加：``字段名 数据类型 NOT NULL``
- 创建表后添加：``alter table [表名] modify [字段名] [数据类型] NOT NULL;``
- 删除：``alter table [表名] modify [字段名] [数据类型];``

唯一约束：
- 创建表时候添加：``字段名 数据类型 UNIQUE``
- 创建表后添加：``alter table [表名] modify [字段名] [数据类型] UNIQUE;``
- 删除：``alter table [表名] modify [字段名] [数据类型];``
- \*UNIQUE约束的字段可以有多个NULL值。

主键约束：
- 含义：表中记录的唯一标识，非空且唯一，一张表只有一个主键。
- 创建表时添加：`` 字段名 数据类型 PRIMARY KEY ``
- 创建表后添加：``alter table [表名] modify [字段名] [数据类型] primary key;``
- 删除主键：`` alter table [表名] drop primary key; ``
- 自动增长：``id INT PRIMARY KEY AUTO_INCREMENT``
- 删除自动增长：``alter table [表名] modify [字段名] [数据类型];``

外键约束：
- 创建表时添加外键：`` 外键列,constraint 外键名 foreign key (外键列名) references 主表名(主表列名)``
- 创建表后添加：`` ALTER TABLE 表名 ADD CONSTRAINT 外键名 FOREIGN KEY (外键列名) REFERENCES 主表名(主表列名); ``
- 删除外键：`` ALTER TABLE 表名 DROP FOREIGN KEY 外键名; ``
- \*级联操作，级联更新，级联删除：`` ALTER TABLE 表名 ADD CONSTRAINT 外键名 FOREIGN KEY (外键列名) REFERENCES 主表名(主表列名) ON UPDATE CASCADE ON DELETE CASCADE;``

``` sql
--- 练习 ---
DROP TABLE IF EXISTS department;
DROP TABLE IF EXISTS employee;

CREATE TABLE department(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(20) NOT NULL UNIQUE,
	location VARCHAR(20) NOT NULL
);
CREATE TABLE employee(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(20) NOT NULL UNIQUE,
	age INT NOT NULL,
	dep_id INT,
	CONSTRAINT emp_dept_fk FOREIGN KEY (dep_id) REFERENCES department(id)
);

INSERT INTO department VALUES(NULL, '研发部', '广州'),(NULL, '销售部', '深圳');
INSERT INTO employee(NAME, age, dep_id) VALUES ('张三', 20, 1);
INSERT INTO employee(NAME, age, dep_id) VALUES ('李四', 21, 1);
INSERT INTO employee(NAME, age, dep_id) VALUES ('王五', 20, 1);
INSERT INTO employee(NAME, age, dep_id) VALUES ('老王', 20, 2);
INSERT INTO employee(NAME, age, dep_id) VALUES ('大王', 22, 2);
INSERT INTO employee(NAME, age, dep_id) VALUES ('小王', 18, 2);

SELECT * FROM employee;
SELECT * FROM department;

DROP TABLE department;  --错误

INSERT INTO employee(NAME, age, dep_id) VALUES ('王炸', 18, 5);   --错误

ALTER TABLE employee DROP FOREIGN KEY emp_dept_fk;
ALTER TABLE employee ADD CONSTRAINT emp_dept_fk FOREIGN KEY (dep_id) REFERENCES department(id) ON UPDATE CASCADE ON DELETE CASCADE;
```

## 数据库的设计
### 多表之间的关系
1. 一对一：
    举例：人和身份
    分析：一个人只有一个身份证，一个身份证只对应一个人。
2. 一对多：
    举例：部门和员工
    分析：一个部门有多个员工，一个员工只对应一个部门。
3. 多对多：
    举例学生和课程
    分析：一个学生可以学多门课，一门课可以多个学生学。

#### 关系实现
1. 一对多（多对一）：
    如：部门和员工
    实现方式：在多的一方建立外键，指向一的主键。
2. 多对多：
    如：学生和课程
    实现方式：建立中间表，至少有两个字段作为外键，分别指向两张表的主键。
3. 一对一：
    如：人和身份
    实现方式：在任意一方添加唯一外键，指向另一方的主键。

### 数据库设计范式
概念：设计关系数据库时，遵从不同的规范要求，设计出合理的关系型数据库，这些不同的规范要求被称为不同的范式，各种范式呈递次规范，越高的范式数据库冗余越小。
目前关系数据库有六种范式：第一范式（1NF）、第二范式（2NF）、第三范式（3NF）、巴斯-科德范式（BCNF）、第四范式(4NF）和第五范式（5NF，又称完美范式）。

#### 分类
1. 第一范式（1NF）：每一列都是不可分割的原子数据项。
2. 第二范式（2NF）：在1NF的基础上，非码属性必须完全依赖于候选码（在1NF基础上消除非主属性对主码的部分函数依赖）。
    1. 函数依赖：A-->B，如果通过A属性（属性组）可以确定唯一B属性的值，则B依赖于A。
        比如：学号-->姓名，（学号，课程）-->分数
    2. 完全函数依赖：如果A是一个属性组，B属性值的确定需要依赖于A属性组中所有的属性值。
        比如：（学号，课程）-->分数
    3. 部分函数依赖：如果A是一个属性组，B属性值的确定需要依赖于A属性组中部分属性值。
        比如：（学号，课程）-->姓名
    4. 传递函数依赖：如果B完全依赖于A，C完全依赖于B，则C传递依赖于A
        比如：学号-->系名，系名-->系主任
    5. 码：如果在一张表中，一个属性（属性组）被其他所有属性完全依赖，则称这个属性（属性组）为该表的码。
    6. 主属性：属于码的属性
    7. 非主属性：码以外的属性
3. 第三范式（3NF）：在2NF基础上，任何非主属性不依赖于其它非主属性（在2NF基础上消除传递依赖）。

#### 案例
学生信息：学号，姓名，年龄，系，系主任，课程名，分数

1NF
（学号，姓名，年龄，系，系主任，课程名，分数）

2NF
（学号）-->（姓名、年龄、性别、系、系主任）
（学号，课程名称）-->（分数）

3NF
（学号）-->（姓名、年龄、性别、系）
（系）-->（系主任）
（学号，课程名称）-->（分数）

## 数据库的备份和还原
1. 命令行
    备份：`` mysqldump -u 用户名 -p 备份数据库名称 > 保存路径/备份文件名.sql ``
    还原：
        1. 登录mysql
        2. 创建数据库
        3. 使用数据库
        4. 执行文件。source 备份文件
2. 使用图形工具

## 多表查询
### 内连接查询
1. 显式内连接：
    语法：`` select 字段 from 表1 inner join 表2 on 条件; ``
    含义：获取两表条件相匹配的记录
2. 隐式内连接：用where条件消除无用数据

### 外连接查询
1. 左外连接
    语法：`` select 字段列表 from 表1 left [outer] join 表2 on 条件; ``
    含义：获取左表所有记录，即使右表没有对应匹配的记录
2. 右外连接
    语法：`` select 字段列表 from 表1 right [outer] join 表2 on 条件; ``
    含义：获取右表所有记录，即使左表没有对应匹配的记录

### 子查询
概念：查询中嵌套查询，嵌套查询称为子查询
子查询的不同情况：
1. 子查询结果是单行单列的，子查询作为条件，使用运算符：\> >= < <= =
2. 子查询结果是多行单列的，子查询作为条件，使用运算符：in
3. 子查询结果是多行多列的，子查询可以作为一张虚拟表

### 练习
``` sql
-- 准备
-- 创建数据库
DROP DATABASE IF EXISTS db2;
CREATE DATABASE db2 CHARACTER SET utf8;

USE db2;

-- 创建表
DROP TABLE IF EXISTS department;
DROP TABLE IF EXISTS employee;

CREATE TABLE department(
	id INT PRIMARY KEY,
	NAME VARCHAR(20) NOT NULL,
	location VARCHAR(20) NOT NULL
);

CREATE TABLE employee(
	id INT PRIMARY KEY,
	NAME VARCHAR(20) NOT NULL,
	salary DOUBLE NOT NULL,
	join_date DATE NOT NULL,
	dep_id INT,
	CONSTRAINT emp_dep_fk FOREIGN KEY (dep_id) REFERENCES department(id)
);

-- 插入数据
INSERT INTO department VALUES(1, '研发部', '广州'), (2, '销售部', '广州'), (3, '财务部', '深圳');

INSERT INTO employee (NAME, salary, join_date, dep_id) VALUES ('孙悟空', 7200, '2015-10-01', 1);
INSERT INTO employee (NAME, salary, join_date, dep_id) VALUES ('猪八戒', 6000, '2016-03-01', 1);
INSERT INTO employee (NAME, salary, join_date, dep_id) VALUES ('唐僧', 9500, '2014-05-01', 2);
INSERT INTO employee (NAME, salary, join_date, dep_id) VALUES ('沙悟净', 5200, '2016-12-01', 2);
INSERT INTO employee (NAME, salary, join_date, dep_id) VALUES ('白龙马', 4500, '2015-11-01', 3);

-- 使得两表主键自增
ALTER TABLE employee DROP FOREIGN KEY emp_dep_fk;
ALTER TABLE department MODIFY id INT AUTO_INCREMENT;
ALTER TABLE employee ADD CONSTRAINT emp_dep_fk FOREIGN KEY (dep_id) REFERENCES department(id) ON UPDATE CASCADE ON DELETE CASCADE;

ALTER TABLE employee MODIFY id INT AUTO_INCREMENT;

-- 内连接查询
-- 查询所有员工的部门信息
-- 隐式
SELECT 
	t1.`name`, t2.`name`
FROM
	employee t1, department t2
WHERE
	t1.`dep_id` = t2.`id`;
	
-- 显式
SELECT
	t1.`name`, t2.`name`
FROM
	employee t1 INNER JOIN department t2
ON
	t1.`dep_id` = t2.`id`;

-- 外连接查询
-- 新来一个员工，但是还没有部门
INSERT INTO employee (NAME, salary, join_date) VALUES ('白骨精', 7000, '2019-03-24');

-- 查询所有员工的信息以及部门信息
SELECT
	t1.*, t2.`name`
FROM
	employee t1 LEFT OUTER JOIN department t2
ON
	t1.`dep_id` = t2.`id`;

-- 子查询
-- 查询工资最高员工的信息
SELECT * FROM employee WHERE salary = (SELECT MAX(salary) FROM employee);

-- 查询财务部和研发部所有员工的信息
SELECT
	*
FROM
	employee
WHERE
	dep_id IN (SELECT id FROM department WHERE NAME IN ('财务部', '研发部'));

-- 查询工资高于平均工资员工的名字,工资和部门
SELECT
	t1.`name`, t1.`salary`, t2.`name`
FROM
	employee t1 LEFT JOIN department t2
ON
	t1.`dep_id` = t2.`id`
WHERE
	salary > (SELECT AVG(salary) FROM employee);
	
-- 查询入职日期是2017年以前员工的信息(包括部门信息)\
-- 找出2017年以前入职员工的信息，再和department表内连接
SELECT
	t_d.`name`, t_e.*
FROM
	department t_d, (SELECT * FROM employee WHERE join_date < '2017-01-01') t_e
WHERE
	t_d.`id` = t_e.`dep_id`;
	
-- 先内连接再where判断
```

## 事务
### 事务的基本介绍
概念：如果一个包含多个步骤的业务操作，被一个事务管理，要么同时成功，要么同时失败。
举例：张三转账500元给李四，1.张三余额>500；2.张三金额-500；3.李四金额+500
操作：
1. 开启事务
2. 提交
3. 回滚

``` sql
DROP DATABASE IF EXISTS db3;
CREATE DATABASE IF NOT EXISTS db3 DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
USE db3;

CREATE TABLE account(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(20) NOT NULL,
	balance DOUBLE NOT NULL
);

INSERT INTO account (NAME, balance) VALUES('张三', 1000);
INSERT INTO account (NAME, balance) VALUES('李四', 1000);

UPDATE account SET balance = 1000 WHERE NAME = '张三';
UPDATE account SET balance = 1000 WHERE NAME = '李四';

SELECT * FROM account;

-- 张三转500元给李四
-- 0. 开启事务
START TRANSACTION;

-- 1. 业务语句
UPDATE account SET balance = balance - 500 WHERE NAME = '张三';
UPDATE account SET balance = balance + 500 WHERE NAME = '李四';

-- 2. 提交事务
COMMIT;

-- 3. 失败回滚
ROLLBACK;
```

mysql中事务默认自动提交。
1. 自动提交：在mysql中，一条DML（增删改）语句默认提交一次事务。
2. 手动提交：需要先开启事务，再提交（commit），开启事务不commit，DML执行回滚。
3. 查看事务的默认提交方式：`` SELECT @@autocommit; `` -- 1 自动提交，0 手动提交
4. 修改事务的默认提交方式：`` SET @@autocommit = 0; ``

### 事务的四大特征(ACID)
1. 原子性：是不可分割的最小操作单位，要么同时成功，要么同时失败。
2. 持久性：当事务提交或回滚后，数据库会持久化保存数据。
3. 隔离性：多个事务之间，相互独立。
4. 一致性：事务操作前后，数据总量不变。

### 事务的隔离级别
概念：多个事务之间隔离的，但是多个事务操作同一批数据，会引发一些问题，设置不同的隔离级别可以解决问题。

存在的问题：
1. 脏读：一个事务，读取到另一个事务中没有提交的数据。
2. 不可重复读：同一个事务中，两次读取到的数据不同。
3. 幻读：一个事务操作（DML）数据表中所有记录，另一个事务添加了一条数据，第一个事务则查询不到自己的修改

隔离级别：
1. read uncommitted：读未提交
    产生的问题：脏读、不可重复读、幻读
2. read committed：读已提交（Oracle默认）
    产生的问题：不可重复读、幻读
3. repeatable read：可重复读（mysql默认）
    产生的问题：幻读
4. serializable：串行化
    可以解决所有的问题

\* 注意：隔离级别从小到大安全性越来越高，但是效率越来越低。

查询隔离级别：
`` select @@tx_isolation; ``

设置隔离级别：
`` SET GLOBAL TRANSACTION ISOLATION LEVEL READ COMMITTED; ``

## 用户权限管理DCL
### 管理用户

``` sql
-- 查询用户
USE msyql;
SELECT * FROM USER;

-- 添加用户
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';

-- 删除用户
DROP USER '用户名'@'主机名';

-- 修改密码
UPDATE USER SET PASSWORD=PASSWORD('新密码') WHERE USER='用户名';
SET PASSWORD FOR '用户名'@'主机名' = PASSWORD('新密码');

/*
忘记root密码？
1. 停止mysql服务
2. 使用无验证方式启动mysql
3. 无密码登录mysql，修改密码
4. 重启mysql服务，以新密码登录
*/
msyqld --skip-grant-tables
mysql

-- 查询用户权限
SHOW GRANTS FOR '用户名'@'主机名';

-- 授予权限
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION;

-- 撤销用户权限
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';

```


