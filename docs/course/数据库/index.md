# 数据库

相关链接：

- [JavaGuide-数据库](https://javaguide.cn/database/)

## 一、数据库基础

### 1.1 **数据库, 数据库管理系统, 数据库系统, 数据库管理员**

这四个概念描述了从数据本身到管理整个体系的不同层次，我们常用一个图书馆的例子来把它们串联起来理解。

- **数据库 (Database - DB)**: 它就像是图书馆里，书架上存放的所有书籍和资料。从技术上讲，数据库就是按照一定数据模型组织、描述和储存起来的、可以被各种用户共享的结构化数据的集合。它就是我们最终要存取的核心——信息本身。
- **数据库管理系统 (Database Management System - DBMS)**: 它就像是整个图书馆的管理系统，包括图书的分类编目规则、借阅归还流程、安全检查系统等等。从技术上讲，DBMS 是一种大型软件，比如我们常用的 MySQL、Oracle、PostgreSQL 软件。它的核心职责是科学地组织和存储数据、高效地获取和维护数据；为我们屏蔽了底层文件操作的复杂性，提供了一套标准接口（如 SQL）来操纵数据，并负责并发控制、事务管理、权限控制等复杂问题。
- **数据库系统 (Database System - DBS)**: 这是一个更大的概念，不仅包括书(DB)和管理系统(DBMS)，还包括了硬件、应用和使用的人。
- **数据库管理员 (Database Administrator - DBA )**: 他就是图书馆的馆长，负责整个数据库系统正常运行。他的职责非常广泛，包括数据库的设计、安装、监控、性能调优、备份与恢复、安全管理等等，确保整个系统的稳定、高效和安全。

### 1.2 **DBMS 的四大核心功能**

1. **数据定义**： 这是 DBMS 的基础。它提供了一套数据定义语言（Data Definition Language - DDL），让我们能够创建、修改和删除数据库中的各种对象。这不仅仅是定义表的结构（比如字段名、数据类型），还包括定义视图、索引、触发器、存储过程等。
2. **数据操作**： 这是我们作为开发者日常使用最多的功能。它提供了一套数据操作语言（Data Manipulation Language - DML），核心就是我们熟悉的增、删、改、查（CRUD）操作。它让我们能够方便地对数据库中的数据进行操作和检索。
3. **数据控制**： 这是保证数据正确、安全、可靠的关键。通常包含并发控制、事务管理、完整性约束、权限控制、安全性限制等功能。
4. **数据库维护**： 这部分功能是为了保障数据库系统的长期稳定运行。它包括了数据的导入导出、数据库的备份与恢复、性能监控与分析、以及系统日志管理等。

### 1.3 **DBMS 的类型**

1. **关系型数据库**：除了我们最常用的关系型数据库（RDBMS），比如 MySQL（开源首选）、PostgreSQL（功能最全）、Oracle（企业级），它们基于严格的表结构和 SQL，非常适合结构化数据和需要事务保证的场景，例如银行交易、订单系统。
2. **NoSQL 数据库**：它们的共同特点是为了极致的性能和水平扩展能力，在某些方面（通常是事务）做了妥协。
    - **键值数据库**：如 Redis，适合缓存、会话存储等。
    - **文档数据库**：如 MongoDB，适合半结构化文档（比如 JSON/BSON）的存储和查询。
    - **列式数据库**：如 Cassandra、HBase，适合大规模数据存储和查询。
    - **图形数据库**：如 Neo4j，适合关系复杂、查询路径的场景。
3. **NewSQL 数据库**：分布式关系型数据库。不仅具有 NoSQL 对海量数据的存储管理能力，还保持了传统数据库支持 ACID 和 SQL 等特性。

### 1.4 **关系型数据库核心概念**

![v-17.svg](images%2Fv-17.svg)

**基础概念**：

* **元组（Tuple）**： 元组是关系数据库中的基本单位，在二维表中对应一行记录。每个元组包含了一个实体的完整信息。例如，在学生表中，每个学生的完整信息（学号、姓名、年龄等）构成一个元组。
* **码（Key）**： 码是能够唯一标识关系中元组的一个或多个属性的集合。码的主要作用是保证数据的唯一性和完整性。

**码的分类**：

* **候选码（Candidate Key）**： 候选码是能够唯一标识元组的最小属性集合，其任何真子集都不能唯一标识元组。一个关系可能有多个候选码。例如，在学生表中，如果"学号"能唯一标识学生，同时"身份证号"也能唯一标识学生，那么{学号}和{身份证号}都是候选码。
* **主码/主键（Primary Key）**： 主码是从候选码中选择的一个，用于唯一标识关系中的元组。每个关系只能有一个主码，但可以有多个候选码。选择主码时通常考虑：简单性、稳定性、无业务含义等因素。
* **外码/外键（Foreign Key）**： 外码是一个关系中的属性或属性组，它对应另一个关系的主码。外码用于建立和维护两个关系之间的联系，是实现参照完整性的重要机制。例如，在选课表中的"学号"如果引用学生表的主码"学号"，则选课表中的"学号"就是外码。

**属性分类**：

* 主属性（Prime Attribute）： 主属性是包含在任何一个候选码中的属性。如果一个关系有多个候选码，那么这些候选码中出现的所有属性都是主属性。例如，工人关系（工号，身份证号，姓名，性别，部门）中，如果{工号}和{身份证号}都是候选码，那么"工号"和"身份证号"都是主属性。
* 非主属性（Non-prime Attribute）： 非主属性是不包含在任何候选码中的属性。这些属性完全依赖于候选码来确定其值。在上述工人关系中，"姓名"、"性别"、"部门"都是非主属性。

### 1.5 **ER 图**

我们做一个项目的时候一定要试着画 ER 图来捋清数据库设计，

**ER 图**：全称是 Entity Relationship Diagram（实体联系图），提供了表示实体类型、属性和联系的方法。

**ER 图的 3 个要素**：

* **实体**：通常是现实世界的业务对象，当然使用一些逻辑对象也可以。比如对于一个校园管理系统，会涉及学生、教师、课程、班级等等实体。在 ER 图中，实体使用矩形框表示。
* **属性**：即某个实体拥有的属性，属性用来描述组成实体的要素，对于产品设计来说可以理解为字段。在 ER 图中，属性使用椭圆形表示。
* **联系**：即实体与实体之间的关系，在 ER 图中用菱形表示，这个关系不仅有业务关联关系，还能通过数字表示实体之间的数量对照关系。例如，一个班级会有多个学生就是一种实体间的联系。

**实体间的关系**：

- 多对多（M: N）
- 1 对 1（1:1）
- 1 对多（1: N）

### 1.6 **数据库范式**

数据库范式有 3 种：

* 1NF(第一范式)：属性不可再分。
* 2NF(第二范式)：1NF 的基础之上，消除了非主属性对于码的部分函数依赖。
* 3NF(第三范式)：3NF 在 2NF 的基础之上，消除了非主属性对于码的传递函数依赖 。

**1NF(第一范式)**

属性（对应于表中的字段）不能再被分割，也就是这个字段只能是一个值，不能再分为多个其他的字段了。

1NF 是所有关系型数据库的最基本要求 ，也就是说关系型数据库中创建的表一定满足第一范式。

**2NF(第二范式)**

2NF 在 1NF 的基础之上，消除了非主属性对于码的**部分函数依赖**。

第二范式在第一范式的基础上增加了一个列，这个列称为主键，非主属性都依赖于主键。

一些重要的概念：

* **函数依赖（functional dependency）**：若在一张表中，在属性（或属性组）X 的值确定的情况下，必定能确定属性 Y 的值，那么就可以说 Y 函数依赖于 X，写作 X → Y。
* **部分函数依赖（partial functional dependency）**：如果 X→Y，并且存在 X 的一个真子集 X0，使得 X0→Y，则称 Y 对 X 部分函数依赖。比如学生基本信息表 R 中（学号，身份证号，姓名）当然学号属性取值是唯一的，在 R 关系中，（学号，身份证号）->（姓名），（学号）->（姓名），（身份证号）->（姓名）；所以姓名部分函数依赖于（学号，身份证号）；
* **完全函数依赖(Full functional dependency)**：在一个关系中，若某个非主属性数据项依赖于全部关键字称之为完全函数依赖。比如学生基本信息表 R（学号，班级，姓名）假设不同的班级学号有相同的，班级内学号不能相同，在 R 关系中，（学号，班级）->（姓名），但是（学号）->(姓名)不成立，（班级）->(姓名)不成立，所以姓名完全函数依赖与（学号，班级）；
* **传递函数依赖**：在关系模式 R(U)中，设 X，Y，Z 是 U 的不同的属性子集，如果 X 确定 Y、Y 确定 Z，且有 X 不包含 Y，Y 不确定 X，（X∪Y）∩Z=空集合，则称 Z 传递函数依赖(transitive functional dependency) 于 X。传递函数依赖会导致数据冗余和异常。传递函数依赖的 Y 和 Z 子集往往同属于某一个事物，因此可将其合并放到一个表中。比如在关系 R(学号 , 姓名, 系名，系主任)中，学号 → 系名，系名 → 系主任，所以存在非主属性系主任对于学号的传递函数依赖。

**3NF(第三范式)**

在 2NF 的基础之上，消除了非主属性对于码的**传递函数依赖**。

符合 3NF 要求的数据库设计，基本上解决了数据冗余过大，插入异常，修改异常，删除异常的问题。

比如在关系 R(学号 , 姓名, 系名，系主任)中，**学号 → 系名，系名 → 系主任**，所以存在非主属性系主任对于学号的**传递函数依赖**，所以该表的设计，不符合 3NF 的要求。

### 1.7 **不推荐使用外键与级联**

* 增加了复杂性
* 增加了额外工作
* 对分库分表不友好
* ……

### 1.8 **不推荐使用存储过程**

* 调试困难
* 移植性差
* 占用数据库资源
* 版本管理困难

### 1.x **数据库设计的步骤**

![v-4.svg](images%2Fv-4.svg)


## 二、NoSQL 基础

### 2.1 **NoSQL 数据库的定义**

NoSQL（Not Only SQL 的缩写）泛指非关系型的数据库，主要针对的是键值、文档以及图形类型数据存储。

并且，NoSQL 数据库天生支持分布式，数据冗余和数据分片等特性，旨在提供可扩展的高可用高性能数据存储解决方案。

NoSQL 数据库可以存储关系型数据，不过它们与关系型数据库的存储方式不同。

NoSQL 数据库代表：HBase、Cassandra、MongoDB、Redis。

### 2.2 **NoSQL 数据库的优势**

NoSQL 数据库非常适合许多现代应用程序，例如移动、Web 和游戏等应用程序，它们需要**灵活**、**可扩展**、**高性能**和**功能强大**的数据库以提供卓越的用户体验。

### 2.3 **NoSQL 数据库的类型**

* **键值**：键值数据库是一种较简单的数据库，其中每个项目都包含键和值。这是极为灵活的 NoSQL 数据库类型，因为应用可以完全控制 value 字段中存储的内容，没有任何限制。Redis 和 DynanoDB 是两款非常流行的键值数据库。
* **文档**：文档数据库中的数据被存储在类似于 JSON（JavaScript 对象表示法）对象的文档中，非常清晰直观。每个文档包含成对的字段和值。这些值通常可以是各种类型，包括字符串、数字、布尔值、数组或对象等，并且它们的结构通常与开发者在代码中使用的对象保持一致。MongoDB 就是一款非常流行的文档数据库。
* **图形**：图形数据库旨在轻松构建和运行与高度连接的数据集一起使用的应用程序。图形数据库的典型使用案例包括社交网络、推荐引擎、欺诈检测和知识图形。Neo4j 和 Giraph 是两款非常流行的图形数据库。
* **宽列**：宽列存储数据库非常适合需要存储大量的数据。Cassandra 和 HBase 是两款非常流行的宽列存储数据库。

![NoSQL数据库的类型.png](images%2FNoSQL%E6%95%B0%E6%8D%AE%E5%BA%93%E7%9A%84%E7%B1%BB%E5%9E%8B.png)

## 三、SQL 语法

### 3.1 **数据库术语**

* **数据库（database）** - 保存有组织的数据的容器（通常是一个文件或一组文件）。
* **数据表（table）** - 某种特定类型数据的结构化清单。
* **模式（schema）** - 关于数据库和表的布局及特性的信息。模式定义了数据在表中如何存储，包含存储什么样的数据，数据如何分解，各部分信息如何命名等信息。数据库和表都有模式。
* **列（column）** - 表中的一个字段。所有表都是由一个或多个列组成的。
* **行（row）** - 表中的一个记录。
* **主键（primary key）** - 一列（或一组列），其值能够唯一标识表中每一行。

### 3.2 **SQL 语法**

**SQL 语法结构**：

* **子句**：是语句和查询的组成成分。（在某些情况下，这些都是可选的。）
* **表达式**：可以产生任何标量值，或由列和行的数据库表
* **谓词**：给需要评估的 SQL 三值逻辑（3VL）（true/false/unknown）或布尔真值指定条件，并限制语句和查询的效果，或改变程序流程。
* **查询**：基于特定条件检索数据。这是 SQL 的一个重要组成部分。
* **语句**：可以持久地影响纲要和数据，也可以控制数据库事务、程序流程、连接、会话或诊断。

**SQL 语法要点**：

- **SQL 语句不区分大小写**，但是数据库表名、列名和值是否区分，依赖于具体的 DBMS 以及配置。例如：`SELECT` 与 `select`、`Select` 是相同的。
- 多条 SQL 语句必须**以分号（;）分隔**。
- 处理 SQL 语句时，所有空格都被忽略。

SQL 语句可以写成一行，也可以分写为多行：

```sql
-- 一行 SQL 语句

UPDATE user SET username='robot', password='robot' WHERE username = 'root';

-- 多行 SQL 语句
UPDATE user
SET username='robot', password='robot'
WHERE username = 'root';
```

SQL 支持三种注释：

```sql
## 注释1
-- 注释2
/* 注释3 */
```

### 3.3 **SQL 分类**

**3.3.1 数据定义语言（DDL）**

数据定义语言（Data Definition Language，DDL）是 SQL 语言集中负责数据结构定义与数据库对象定义的语言。

DDL 的主要功能是**定义数据库对象**。

DDL 的核心指令是 `CREATE`、`ALTER`、`DROP`

**3.3.2 数据操纵语言（DML）**

数据操纵语言（Data Manipulation Language, DML）是用于**数据库操作**，对数据库其中的对象和数据运行访问工作的编程语句。

DML 的主要功能是 访问数据，因此其语法都是以读写数据库为主。

DML 的核心指令是 `INSERT`、`UPDATE`、`DELETE`、`SELECT`。这四个指令合称 CRUD(Create, Read, Update, Delete)，即增删改查。

**3.3.3 事务控制语言（TCL）**

事务控制语言 (Transaction Control Language, TCL) 用于**管理数据库中的事务**。

用于管理由 DML 语句所做的更改。它还允许将语句分组为逻辑事务。

TCL 的核心指令是 `COMMIT`、`ROLLBACK`

**3.3.4 数据控制语言（DCL）**

数据控制语言 (Data Control Language, DCL) 是一种可对数据访问权进行控制的指令，它可以控制特定用户账户对数据表、查看表、预存程序、用户自定义函数等数据库对象的控制权。

DCL 的核心指令是 `GRANT`、`REVOKE`。

DCL 以**控制用户的访问权限**为主，因此其指令作法并不复杂，可利用 DCL 控制的权限有：`CONNECT`、`SELECT`、`INSERT`、`UPDATE`、`DELETE`、`EXECUTE`、`USAGE`、`REFERENCES`。

根据不同的 DBMS 以及不同的安全性实体，其支持的权限控制也有所不同。


### 3.4 **增删改查**

我们先来介绍 DML 语句用法。 DML 的主要功能是读写数据库实现增删改查。

**3.4.1 插入数据**

`INSERT INTO` 语句用于向表中插入新记录。

```sql
# 插入一行
INSERT INTO user
VALUES (10, 'root', 'root', 'xxxx@163.com');

# 插入多行
INSERT INTO user
VALUES (10, 'root', 'root', 'xxxx@163.com'), (12, 'user1', 'user1', 'xxxx@163.com'), (18, 'user2', 'user2', 'xxxx@163.com');

# 插入行的一部分
INSERT INTO user(username, password, email)
VALUES ('admin', 'admin', 'xxxx@163.com');

# 插入查询出来的数据
INSERT INTO user(username)
SELECT name
FROM account;
```

**3.4.2 更新数据**

`UPDATE` 语句用于更新表中的记录。

```sql
UPDATE user
SET username='robot', password='robot'
WHERE username = 'root';
```

**3.4.3 删除数据**

`DELETE` 语句用于删除表中的记录。

`TRUNCATE TABLE` 可以清空表，也就是删除所有行。说明：`TRUNCATE` 语句不属于 DML 语法而是 DDL 语法。

```sql
# 删除表中的指定数据
DELETE FROM user
WHERE username = 'robot';

# 清空表中的数据
TRUNCATE TABLE user;
```

**3.4.4 查询数据**

`SELECT` 语句用于从数据库中查询数据。

`DISTINCT` 用于返回唯一不同的值。它作用于要 `SELECT` 的所有列，也就是说所有列的值都相同才算相同。`DISTINCT` 通常用于去重。

`LIMIT` 限制返回的行数。可以有两个参数，第一个参数为起始行，从 0 开始；第二个参数为返回的总行数。

* ASC：升序（默认）
* DESC：降序

```sql
# 查询单列
SELECT prod_name
FROM products;

# 查询多列
SELECT prod_id, prod_name, prod_price
FROM products;

# 查询所有列
SELECT *
FROM products;

# 查询不同的值
SELECT DISTINCT vend_id
FROM products;

# 限制查询结果
-- 返回前 5 行
SELECT * FROM mytable LIMIT 5;
SELECT * FROM mytable LIMIT 0, 5;
-- 返回第 3 ~ 5 行
SELECT * FROM mytable LIMIT 2, 3;
```

### 3.5 **排序**

`order by` 用于对结果集按照一个列或者多个列进行排序。默认按照升序对记录进行排序，如果需要按照降序对记录进行排序，可以使用 `DESC` 关键字。

`order by` 对多列排序的时候，先排序的列放前面，后排序的列放后面。并且，不同的列可以有不同的排序规则。

```sql
SELECT * FROM products
ORDER BY prod_price DESC, prod_name ASC;
```

### 3.6 **分组**

**`group by`**：

* `group by` 子句将记录分组到汇总行中。
* `group by` 为每个组返回一个记录。
* `group by` 通常还涉及聚合 `count`，`max`，`sum`，`avg` 等。
* `group by` 可以按一列或多列进行分组。
* `group by` 按分组字段进行排序后，`order by` 可以以汇总字段来进行排序。

```sql
# 分组
SELECT cust_name, COUNT(cust_address) AS addr_num
FROM Customers GROUP BY cust_name;

# 分组后排序
SELECT cust_name, COUNT(cust_address) AS addr_num
FROM Customers GROUP BY cust_name
ORDER BY cust_name DESC;
```

**`having`**：

* `having` 用于对汇总的 `group by` 结果进行过滤。
* `having` 一般都是和 `group by` 连用。
* `where` 和 `having` 可以在相同的查询中。

使用 WHERE 和 HAVING 过滤数据：

```sql
SELECT cust_name, COUNT(*) AS NumberOfOrders
FROM Customers
WHERE cust_email IS NOT NULL
GROUP BY cust_name
HAVING COUNT(*) > 1;
```

**`having` vs `where`**：

- `where`：过滤指定的行，后面不能加聚合函数（分组函数）。`where` 在 `group by` 前。
- `having`：过滤分组，一般都是和 `group by` 连用，不能单独使用。`having` 在 `group by` 之后

### 3.7 **子查询**

子查询是嵌套在较大查询中的 SQL 查询，也称内部查询或内部选择，包含子查询的语句也称为外部查询或外部选择。

简单来说，子查询就是指将一个 `select` 查询（子查询）的结果作为另一个 SQL 语句（主查询）的数据来源或者判断条件。

子查询可以嵌入 `SELECT`、`INSERT`、`UPDATE` 和 `DELETE` 语句中，也可以和 `=`、`<`、`>`、`IN`、`BETWEEN`、`EXISTS` 等运算符一起使用。

子查询常用在 `WHERE` 子句和 `FROM` 子句后边：

* 当用于 `WHERE` 子句时，根据不同的运算符，子查询可以返回单行单列、多行单列、单行多列数据。子查询就是要返回能够作为 `WHERE` 子句查询条件的值。
* 当用于 `FROM` 子句时，一般返回多行多列数据，相当于返回一张临时表，这样才符合 `FROM` 后面是表的规则。这种做法能够实现多表联合查询。

**用于 `WHERE` 子句的子查询**：

```sql
select column_name [, column_name ]
from   table1 [, table2 ]
where  column_name operator
    (select column_name [, column_name ]
    from table1 [, table2 ]
    [where])
```

* 子查询需要放在括号`( )`内。
* `operator` 表示用于 `where` 子句的运算符。

**用于 `FROM` 子句的子查询**：

```sql
select column_name [, column_name ]
from (select column_name [, column_name ]
      from table1 [, table2 ]
      [where]) as temp_table_name
where  condition
```

用于 `FROM` 的子查询返回的结果相当于一张临时表，所以需要使用 `as` 关键字为该临时表起一个名字。

**子查询的子查询**：

```sql
SELECT cust_name, cust_contact
FROM customers
WHERE cust_id IN (SELECT cust_id
                  FROM orders
                  WHERE order_num IN (SELECT order_num
                                      FROM orderitems
                                      WHERE prod_id = 'RGAN01'));
```

内部查询首先在其父查询之前执行，以便可以将内部查询的结果传递给外部查询。

### 3.8 **WHERE**

* WHERE 子句用于过滤记录，即缩小访问数据的范围。
* WHERE 后跟一个返回 true 或 false 的条件。
* WHERE 可以与 SELECT，UPDATE 和 DELETE 一起使用。 
* 可以在 WHERE 子句中使用的操作符：`=`、`<>`、`>`、`<`、`>=`、`<=`、`BETWEEN`、`LIKE`、`IN`

**`SELECT` 语句中的 `WHERE` 子句**：

```sql
SELECT * FROM Customers
WHERE cust_name = 'Kids Place';
```

**`UPDATE` 语句中的 `WHERE` 子句**：

```sql
UPDATE Customers
SET cust_name = 'Jack Jones'
WHERE cust_name = 'Kids Place';
```

**`DELETE` 语句中的 `WHERE` 子句**：

```sql
DELETE FROM Customers
WHERE cust_name = 'Kids Place';
```

### 3.9 **IN 和 BETWEEN**

* `IN` 操作符在 `WHERE` 子句中使用，作用是在指定的几个特定值中任选一个值。
* `BETWEEN` 操作符在 `WHERE` 子句中使用，作用是选取介于某个范围内的值。

```sql
# IN 操作符
SELECT *
FROM products
WHERE vend_id IN ('DLL01', 'BRS01');

# BETWEEN 操作符
SELECT *
FROM products
WHERE prod_price BETWEEN 3 AND 5;
```

### 3.10 **AND、OR、NOT**

* `AND`、`OR`、`NOT` 是用于对过滤条件的逻辑处理指令。
* `AND` 优先级高于 `OR`，为了明确处理顺序，可以使用 `()`。
* `AND` 操作符表示左右条件都要满足。
* `OR` 操作符表示左右条件满足任意一个即可。
* `NOT` 操作符用于否定一个条件。

```sql
# AND 操作符
SELECT prod_id, prod_name, prod_price
FROM products
WHERE vend_id = 'DLL01' AND prod_price <= 4;

# OR 操作符
SELECT prod_id, prod_name, prod_price
FROM products
WHERE vend_id = 'DLL01' OR vend_id = 'BRS01';

# NOT 操作符
SELECT *
FROM products
WHERE prod_price NOT BETWEEN 3 AND 5;
```

### 3.11 **LIKE**

* `LIKE` 操作符在 `WHERE` 子句中使用，作用是确定字符串是否匹配模式。
* 只有字段是文本值时才使用 `LIKE`。
* `LIKE` 支持两个通配符匹配选项：`%` 和 `_`。
* 不要滥用通配符，通配符位于开头处匹配会非常慢。
* `%` 表示任何字符出现任意次数。
* `_` 表示任何字符出现一次。

```sql
# % 通配符
SELECT prod_id, prod_name, prod_price
FROM products
WHERE prod_name LIKE '%bean bag%';

# _ 通配符
SELECT prod_id, prod_name, prod_price
FROM products
WHERE prod_name LIKE '__ inch teddy bear';
```

### 3.12 **连接**

JOIN 是“连接”的意思，顾名思义，SQL JOIN 子句用于将两个或者多个表联合起来进行查询。

连接表时需要在每个表中选择一个字段，并对这些字段的值进行比较，值相同的两条记录将合并为一条。

连接表的本质就是将不同表的记录合并起来，形成一张新表。当然，这张新表只是临时的，它仅存在于本次查询期间。

```sql
select table1.column1, table2.column2...
from table1
join table2
on table1.common_column1 = table2.common_column2;
```

`table1.common_column1 = table2.common_column2` 是连接条件，只有满足此条件的记录才会合并为一行。

另外，如果两张表的关联字段名相同，也可以使用 `USING` 子句来代替 `ON`，举个例子：

```sql
SELECT table1.column1, table2.column2...
FROM table1
JOIN table2
USING (common_column);
```

**`ON` 和 `WHERE` 的区别**：

* 连接表时，SQL 会根据连接条件生成一张新的临时表。`ON` 就是连接条件，它决定临时表的生成。
* `WHERE` 是在临时表生成以后，再对临时表中的数据进行过滤，生成最终的结果集，这个时候已经没有 `JOIN-ON` 了。

SQL 允许在 JOIN 左边加上一些修饰性的关键词，从而形成不同类型的连接（默认为 `INNER JOIN`）：

- INNER JOIN 内连接
- LEFT JOIN / LEFT OUTER JOIN 左(外)连接
- RIGHT JOIN / RIGHT OUTER JOIN 右(外)连接
- FULL JOIN / FULL OUTER JOIN 全(外)连接
- SELF JOIN
- CROSS JOIN

对于 `INNER JOIN` 来说，还有一种隐式的写法，称为 “隐式内连接”，也就是没有 `INNER JOIN` 关键字，使用 `WHERE` 语句实现内连接的功能

```sql
# 隐式内连接
select c.cust_name, o.order_num
from Customers c, Orders o
where c.cust_id = o.cust_id
order by c.cust_name;

# 显式内连接
select c.cust_name, o.order_num
from Customers c inner join Orders o
using(cust_id)
order by c.cust_name;
```

### 3.13 **组合**

`UNION` 运算符将两个或更多查询的结果组合起来，并生成一个结果集，其中包含来自 `UNION` 中参与查询的提取行。

**`UNION` 基本规则**：

* 所有查询的列数和列顺序必须相同。
* 每个查询中涉及表的列的数据类型必须相同或兼容。
* 通常返回的列名取自第一个查询。

默认地，`UNION` 操作符选取不同的值。如果允许重复的值，请使用 `UNION ALL`。

```sql
SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2;
```

`UNION` 结果集中的列名总是等于 `UNION` 中第一个 `SELECT` 语句中的列名。

**`JOIN` vs `UNION`**：

- `JOIN` 中连接表的列可能不同，但在 `UNION` 中，所有查询的列数和列顺序必须相同。
- `UNION` 将查询之后的行放在一起（垂直放置），但 `JOIN` 将查询之后的列放在一起（水平放置），即它构成一个笛卡尔积。

### 3.13 **函数**



### 3.14 **数据定义**

接下来，我们来介绍 DDL 语句用法。DDL 的主要功能是定义数据库对象（如：数据库、数据表、视图、索引等）

**3.14.1 数据库（DATABASE）**

**创建数据库**

```sql
CREATE DATABASE test;
```

**删除数据库**

```sql
DROP DATABASE test;
```

**选择数据库**

```sql
USE test;
```


**3.14.2 数据表（TABLE）**

**创建数据表**

```sql
# 普通创建
CREATE TABLE user (
  id int(10) unsigned NOT NULL COMMENT 'Id',
  username varchar(64) NOT NULL DEFAULT 'default' COMMENT '用户名',
  password varchar(64) NOT NULL DEFAULT 'default' COMMENT '密码',
  email varchar(64) NOT NULL DEFAULT 'default' COMMENT '邮箱'
) COMMENT='用户表';

# 根据已有的表创建新表
CREATE TABLE vip_user AS
SELECT * FROM user;
```

**删除数据表**

```sql
DROP TABLE user;
```

**修改数据表**

```sql
# 添加列
ALTER TABLE user
ADD age int(3);

# 删除列
ALTER TABLE user
DROP COLUMN age;

# 修改列
ALTER TABLE user
MODIFY COLUMN age tinyint;

# 添加主键
ALTER TABLE user
ADD PRIMARY KEY (id);

# 删除主键
ALTER TABLE user
DROP PRIMARY KEY;
```

**3.14.3 视图（VIEW）**

**定义**：

* 视图是基于 SQL 语句的结果集的可视化的表。
* 视图是虚拟的表，本身不包含数据，也就不能对其进行索引操作。对视图的操作和对普通表的操作一样。

**作用**：

* 简化复杂的 SQL 操作，比如复杂的联结；
* 只使用实际表的一部分数据；
* 通过只给用户访问视图的权限，保证数据的安全性；
* 更改数据格式和表示。

**创建视图**

```sql
CREATE VIEW top_10_user_view AS
SELECT id, username
FROM user
WHERE id < 10;
```

**删除视图**

```sql
DROP VIEW top_10_user_view;
```

**3.14.4 索引（INDEX）**

索引是一种用于快速查询和检索数据的数据结构，其本质可以看成是一种排序好的数据结构。

优点：

* 使用索引可以大大加快 数据的检索速度（大大减少检索的数据量）, 这也是创建索引的最主要的原因。
* 通过创建唯一性索引，可以保证数据库表中每一行数据的唯一性。

缺点：

* 创建索引和维护索引需要耗费许多时间。当对表中的数据进行增删改的时候，如果数据有索引，那么索引也需要动态的修改，会降低 SQL 执行效率。
* 索引需要使用物理文件存储，也会耗费一定空间。

大多数情况下，索引查询都是比全表扫描要快的。但是如果数据库的数据量不大，那么使用索引也不一定能够带来很大提升。

**创建索引**

```sql
CREATE INDEX user_index
ON user (id);
```

**创建唯一索引**

```sql
CREATE UNIQUE INDEX user_index
ON user (id);
```

**添加索引**

```sql
ALTER TABLE user
ADD INDEX user_index(id);
```

**删除索引**

```
ALTER TABLE user
DROP INDEX user_index;
```

**3.14.5 约束**

SQL 约束用于规定表中的数据规则。

如果存在违反约束的数据行为，行为会被约束终止。

约束可以在创建表时规定（通过 `CREATE TABLE` 语句），或者在表创建之后规定（通过 `ALTER TABLE` 语句）。

约束类型：

* `NOT NULL` - 指示某列不能存储 NULL 值。
* `UNIQUE` - 保证某列的每行必须有唯一的值。
* `PRIMARY KEY` - NOT NULL 和 UNIQUE 的结合。确保某列（或两个列多个列的结合）有唯一标识，有助于更容易更快速地找到表中的一个特定的记录。
* `FOREIGN KEY` - 保证一个表中的数据匹配另一个表中的值的参照完整性。
* `CHECK` - 保证列中的值符合指定的条件。
* `DEFAULT` - 规定没有给列赋值时的默认值

创建表时使用约束条件：

```sql
CREATE TABLE Users (
  Id INT(10) UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '自增Id',
  Username VARCHAR(64) NOT NULL UNIQUE DEFAULT 'default' COMMENT '用户名',
  Password VARCHAR(64) NOT NULL DEFAULT 'default' COMMENT '密码',
  Email VARCHAR(64) NOT NULL DEFAULT 'default' COMMENT '邮箱地址',
  Enabled TINYINT(4) DEFAULT NULL COMMENT '是否有效',
  PRIMARY KEY (Id)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COMMENT='用户表';
```


### 3.15 **事务处理**

接下来，我们来介绍 TCL 语句用法。TCL 的主要功能是管理数据库中的事务。

不能回退 `SELECT` 语句；也不能回退 `CREATE` 和 `DROP` 语句。

**MySQL 默认是隐式提交**，每执行一条语句就把这条语句当成一个事务然后进行提交。当出现 `START TRANSACTION` 语句时，会关闭隐式提交；当 `COMMIT` 或 `ROLLBACK` 语句执行后，事务会自动关闭，重新恢复隐式提交。

通过 `set autocommit=0` 可以取消自动提交，直到 `set autocommit=1` 才会提交；`autocommit` 标记是针对每个连接而不是针对服务器的。

指令：

* `START TRANSACTION` - 指令用于标记事务的起始点。
* `SAVEPOINT` - 指令用于创建保留点。
* `ROLLBACK TO` - 指令用于回滚到指定的保留点；如果没有设置保留点，则回退到 `START TRANSACTION` 语句处。
* `COMMIT` - 提交事务。

```sql
-- 开始事务
START TRANSACTION;

-- 插入操作 A
INSERT INTO `user`
VALUES (1, 'root1', 'root1', 'xxxx@163.com');

-- 创建保留点 updateA
SAVEPOINT updateA;

-- 插入操作 B
INSERT INTO `user`
VALUES (2, 'root2', 'root2', 'xxxx@163.com');

-- 回滚到保留点 updateA
ROLLBACK TO updateA;

-- 提交事务，只有操作 A 生效
COMMIT;
```

### 3.16 **权限控制**

接下来，我们来介绍 DCL 语句用法。DCL 的主要功能是控制用户的访问权限。

要授予用户帐户权限，可以用 `GRANT` 命令。要撤销用户的权限，可以用 `REVOKE` 命令。

### 3.17 **存储过程**

存储过程可以看成是对一系列 SQL 操作的批处理。存储过程可以由触发器，其他存储过程以及 Java， Python，PHP 等应用程序调用。

使用存储过程的好处：

* 代码封装，保证了一定的安全性；
* 代码复用；
* 由于是预先编译，因此具有很高的性能。

需要注意的是：**阿里巴巴《Java 开发手册》强制禁止使用存储过程。因为存储过程难以调试和扩展，更没有移植性**。

### 3.18 **游标**

### 3.19 **触发器**