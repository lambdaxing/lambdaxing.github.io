---
title: Database System Concepts - notes
author: lambdaxing
date: 2022-12-05 18:00:00 +0800
categories: [Notes, Database]
tags: [Database]
---

>《Database System Concepts: Sixth Edition》笔记。

# 一. 引言：数据库系统的基本原理

&emsp;&emsp;数据库管理系统（Database-Management System，DBMS）由一个互相关联的数据的集合和一组用于访问这些数据的程序组成。计算机科学家开发了大量的用于有效管理数据的**概念**和**技术**，本书所关注的正是这些概念和技术。

## 1. 数据库系统的目标

&emsp;&emsp;数据库系统为了解决文件处理系统（在操作系统的文件处理系统中存储组织信息）中存在的问题，比如数据冗余、数据不一致、数据访问困难、数据孤立、完整性问题、原子性问题、并发与安全等，提出了许多**概念**和**算法**。

## 2. 数据视图

&emsp;&emsp;数据库系统是一些互相关联的数据以及一组使得用户可以访问和修改这些数据的程序的集合。数据库系统的一个主要目的是给用户提供数据的抽象视图，即数据库系统隐藏关于数据存储和维护的某些细节。

### （1）数据抽象

&emsp;&emsp;**数据抽象**的三个层次：
+ 物理层：描述数据的实际存储，物理层详细描述复杂的底层数据结构。
+ 逻辑层：描述数据库中存储什么数据以及这些数据间存在什么关系。
+ 视图层：最高层次的抽象，描述整个数据库的某个部分。多数用户不需要关心所有的信息，而只需要访问数据库的一部分，系统可以为同一数据库提供多个视图，同时屏蔽逻辑层细节和提供一些安全性机制。

### （2）实例和模式

&emsp;&emsp;**实例**：特定时刻存储在数据库中的信息的集合称作数据库的一个实例（instance）。  
&emsp;&emsp;**模式**：数据库的总体设计称作数据库模式（schema）。  

&emsp;&emsp;根据不同的数据抽象层次，数据库系统的三种不同模式：
+ 物理模式：在物理层描述数据库的设计。
+ 逻辑模式：在逻辑层描述数据库的设计。
+ 视图模式：也叫子模式，描述了数据库的不同视图。

### （3）数据模型

&emsp;&emsp;**数据模型**：数据库结构的基础是数据模型（data model）。数据模型是一个描述数据、数据联系、数据语义以及一致性约束的概念工具的集合。**数据模型提供了一种描述物理层、逻辑层以及视图层数据库设计的方式**。

&emsp;&emsp;四类数据模型：
+ 关系模型（relational model），最广泛使用的将数据存储到数据库中的模型。
+ 实体-联系模型（entity-relationship model，ER），用于数据库设计。
+ 面向对象的数据模型（object-based data model）。
+ 对象-关系数据模型（object-relational data model)，一个将对象数据模型和关系数据模型的特点结合在一起的数据模型。
+ 半结构化数据模型（semistructured data model），允许那些相同类型的数据项有不同的属性集的数据模型。

&emsp;&emsp;网状数据模型（network data model）和层次数据模型（hierarchical data model）先于关系模型出现，它们和底层的实现联系紧密，使数据建模变得复杂。

## 3. 数据库语言

&emsp;&emsp;数据库系统提供 **数据定义语言（data-definition language，DDL）** 来定义数据库模式，以及 **数据库操纵语言（data-manipulation language，DML）** 来表达数据库的查询与更新。

### （1）DML：用户访问或操纵数据的语言，即增删改查

&emsp;&emsp;两类基本的数据操纵语言：
+ 过程化DML（procedural DML)，要求用户指定需要什么数据以及如何获得这些数据。
+ 声明式DML（declarative DML），非过程化DML，仅要求用户指定需要什么数据。

&emsp;&emsp;三个抽象层次不仅可以用于定义或构造数据，还可以用于操纵数据。在物理层，必须定义可高效访问数据的算法；在更高的抽象层次，则强调易用性。例如，数据库系统的查询处理器将DML的查询语句翻译成数据库系统物理层的动作序列。

### （2）DDL：说明/定义数据库模式和数据的其他特征的语言，包括：

+ 数据库系统使用的存储结构和访问方式，即数据库模式的实现细节。
+ 一致性/完整性约束：
     - 域约束（domain constraint）。每个属性都必须对应于一个所有可能的取值构成的域（例如，整型、字符型、日期/时间型），约束它可以取的值。
     - 参照完整性（referential integrity）：一个关系中给定属性集上的取值也在另一关系的某一属性集的取值中出现。
     - 断言（assertion）。一个断言就是数据库需要时刻满足的某一条件，域约束和参照完整性是断言的特殊形式。
     - 授权（authorization）。对用户加以区别，以授权来表达：读权限、插入权限、更新权限、删除权限。

&emsp;&emsp;DDL 以一些指令（语句）作为输入，生成一些输出。DDL 的输出放在**数据字典（data dictionary）**中，数据字典包含了**元数据（metadata）**，元数据是关于数据的数据。可把数据字典看作一种特殊的表，只能由数据库系统本身来访问和修改，在读取和修改实际的数据前，数据库系统先要参考数据字典。

## 4. 关系数据库

&emsp;&emsp;关系数据库基于关系模型，使用一系列表来表达数据以及这些数据之间的联系。关系模型是基于记录的模型，数据库的结构是几种固定格式的记录。每个表包含一种特定类型的记录，每种记录类型定义固定数目的字段或属性，表的列对应记录类型的属性。

## 5. 数据库设计

&emsp;&emsp;数据库设计的主要内容是数据库模式的设计，实体-联系（E-R）数据模型是广泛用于数据库设计的数据模型，它提供了一种方便的图形化方式来观察数据、联系和约束。

### （1）设计过程

+ 高层的数据模型说明数据库用户的数据需求，以及怎样构造数据库结构满足这些需求。因此，数据库设计的初始阶段是全面刻画预期的数据库用户的数据需求，制定出用户需求的规格文档。
+ 选定数据模型，将需求转换成一个数据库的概念模式，它描述数据以及数据之间的联系，而不是指定物理的存储细节。关系模型中，概念设计包括数据库中的属性，以及如何将这些属性组织到多个表中，有商业上的决策（前者），也有计算机科学的问题（后者）。后者的两种解决方法：实体-联系模型以及规范化算法。
+ 在功能需求说明中，用户描述数据之上的各种操作（或事务），例如增删改查等。
+ 将抽象数据模型转换到数据库实现。
    - 逻辑设计阶段：设计者将高层的概念模式映射到要使用的数据库系统的实现数据模型上。
    - 物理设计阶段：指定数据库的物理特性，包括文件组织的形式以及内部的存储结构。

### （2）实体-联系模型

&emsp;&emsp;E-R 模型使用一组称为实体的基本对象，以及这些对象间的联系。数据库中实体通过属性集合来描述，联系是几个实体之间的关联。数据库的总体逻辑结构（模式）可以用实体-联系图（E-R 图）进行图形化表示。

### （3）规范化

&emsp;&emsp;规范化的目标是生成一个关系模式集合，使存储信息时没有不必要的冗余，同时又能很轻易地检索数据。这种方法是设计一种符合适当的范式（normal form）的模式。

## 6. 数据存储和查询

&emsp;&emsp;数据库系统划分为不同的模块，每个模块完成整个系统的一个功能。数据库系统的功能部件大致可分为存储管理器和查询处理器。

### （1）存储管理器（storage manager）

&emsp;&emsp;存储管理器负责在数据库中存储底层数据，也是应用程序以及向系统提交的查询之间提供接口的部件。存储管理器负责与文件管理器进行交互，原始数据通过操作系统提供的文件系统存储在磁盘上。存储管理器将各种DML语句翻译为底层文件系统命令。因此，存储管理器负责数据库中数据的存储、检索和更新。  
&emsp;&emsp;存储管理部件包括：
+ 权限及完整性管理器（authorization and integrity manager），检查完整性约束和用户权限。
+ 事务管理器（transaction manager）。
+ 文件管理器（file manager），管理磁盘空间的分配，管理用于表示磁盘上所存储信息的数据结构。
+ 缓冲区管理器（buffer manager），管理内存中的磁盘缓存。
+ 数据文件（data file），存储数据库自身。
+ 数据字典（data dictionary），存储关于数据库结构的元数据，尤其是数据库模式。
+ 索引（index），提供对数据项的快速访问。

### （2）查询处理器（query processor），编译和执行DDL和DML语句。
+ DDL解释器（DDL interpreter），解释DDL语句并将这些定义记录在数据字典中。
+ DML编译器（DML compiler），将DML语句翻译成一个执行方案，包括一系列查询执行引擎能理解的低级指令。DML编译器还进行查询优化。
+ 查询执行引擎（query evaluation engine），执行由DML编译器产生的低级指令。

## 7. 事务管理
+ 原子性（atomicity）：一种要么完成要么不发生的要求。
+ 一致性（consistency）：满足数据库一致性/完整性约束的正确性要求。
+ 持久性（durability）：数据能保持新值的要求。

&emsp;&emsp;事务（transaction）是数据库应用中完成单一逻辑功能的操作集合。每一个事务是一个既具有原子性又具一致性的单元。事务的定义应使之能保持数据库的一致性，这是程序员的职责。原子性和持久性的保证是 **恢复管理器（recovery manager）** 的职责，失败的事务使得数据库系统必须进行故障恢复，即检测系统故障并将数据库恢复到故障发生以前的状态。  
&emsp;&emsp;**并发控制管理器（concurrency control manager）** 控制并发事务间的相互影响，保证数据库一致性。  
&emsp;&emsp;**事务管理器（transaction manager）** 包括并发控制管理器和恢复管理器。

## 8. 数据库体系结构

&emsp;&emsp;数据库系统体系结构受支持其运行的计算机系统的影响很大。数据库系统可以是集中式的/客户-服务器方式的，还可以设计成能充分利用并行计算机系统结构的能力，也能设计成分布式的。

### （1）一个数据库系统各个部分以及它们之间联系的图：

![1_5](/assets/img/2022-12-06-Database-System-Concepts/1_5.png)

# 二. 关系数据库

## 1. 关系模型

### （1）关系数据库的结构

&emsp;&emsp;关系数据库由表（table）的集合构成，每个表有唯一的名字。表中一行代表了一组值之间的一种联系，一个表就是这种联系的一个集合，表这个概念和数学上的**关系**这个概念是密切相关的，这也正是关系数据模型名称的由来。在数学上，n 个值之间的一种联系可以用关于这些值的一个 n 元组（n-tuple）来表示，对应于表中的一行。在关系模型的术语中，关系（relation）用来指代表，元组用来指代行，属性（attribute）指代表中的列。  
&emsp;&emsp;关系实例（relation instance）表示一个关系的特定实例，也就是表中所包含的一组特定的行。  
&emsp;&emsp;对于关系的每个属性，都存在一个允许取值的集合，称为该属性的域（domain）。对所有关系 r 而言，r 的所有属性的域都是原子的，即在数据库中使用域中元素时，域中元素被看作不可再分的单元。  
&emsp;&emsp;空（null）值是一个特殊的值，表示值未知或不存在。空值给数据库的访问和更新带来了很多困难，应尽量避免使用空值。

### （2）数据库模式

&emsp;&emsp;数据库模式是数据库的逻辑设计，数据库实例是给定时刻数据库中数据的一个快照。关系模式的概念相当于程序设计语言中类型定义的概念。一般来说，关系模式由属性序列及各属性对应域组成。关系实例的概念对应于程序设计语言中变量的值的概念。而关系的概念对应的则是变量的概念。关系的模式包括它的属性、属性类型以及关系上的约束。关系的模式是不常变化的。  
&emsp;&emsp;关系 department 的模式是：department（dept_name, building, budget）。属性可以重复，在关系模式中使用相同属性是将不同关系的元组联系起来的一种方法。

### （3）码

&emsp;&emsp;一个关系中没有两个元组在所有属性上的取值都相同。超码（superkey）是一个或多个属性的集合，这些属性的组合可以在一个关系中唯一地标识一个元组。如果 K 是超码，那么 K 的任意超集也是超码。最小的超码，即任意真子集都不是超码的超码，被称为候选码（candidate key）。主码（primary key）表示被数据库设计者选中的、主要用来在一个关系中区分不同元组的候选码。码是整个关系的一种性质，而非某个元组的性质。码的指定代表了被建模的事物在现实世界中的约束。  
&emsp;&emsp;一个关系模式（如r1）可能在它的属性中包含另一个关系模式（如r2）的主码。这个属性在 r1 上称作参照 r2 的外码（foreign key）。关系 r1 也称为外码依赖的参照关系（referencing relation），r2 叫做外码的被参照关系（referenced relation）。  
&emsp;&emsp;参照完整性（referential integrity constraint）：在 referencing relation 中任意元组在特定属性上的取值必然等于 referenced relation 中某个元组在特定属性上的取值。

### （4）关系运算

&emsp;&emsp;关系运算要么施加于单个关系上，要么施加于一对关系上，运算结果总是单个的关系。  
&emsp;&emsp;几种常用的关系运算：
+ 从单个关系中选出满足一些特定谓词的特殊元组，其结果是一个新关系，它是原始关系的一个子集。
+ 从一个关系中选出特定的属性（列），其结果是一个只包含被选择属性的新关系。
+ 连接运算，把分别来自两个关系的元组对合并成单个元组。
    - 自然连接运算：匹配两个关系在共有的所有属性上的取值相同的元组。
    - 笛卡儿积运算：匹配两个关系的所有元组，其结果包含来自两个关系元组的所有对，无论它们的属性值是否匹配。
+ 集合运算。关系是集合，可以在关系上施加标准的集合运算：交、并、差。

## 2. SQL的基本结构和概念

### （1）SQL查询语言概览

&emsp;&emsp;结构化查询语言（SQL） 是最为广泛使用的关系数据库查询语言。  
&emsp;&emsp;SQL 语言的几个部分：
+ 数据定义语言（Data-Definition Language, DDL）：SQL DDL 提供定义关系模式、删除关系以及修改关系模式的命令。
+ 数据操纵语言（DML）： SQL DML 提供查询信息、插入元组、删除元组、修改元组的能力。
+ 完整性（integrity）：SQL DDL 包括定义完整性约束的命令。
+ 视图定义（view definition）：SQL DDL 包括定义视图的命令。
+ 事务控制（transaction control）：SQL 包括定义事物的开始和结束的命令。
+ 嵌入式 SQL 和动态 SQL（embedded SQL and dynamic SQL）：定义SQL语句如何嵌入到通用编程语言。
+ 授权（authorization）：SQL DDL 包括定义对关系和视图的访问权限的命令。

### （2）SQL 的基本 DML 和 DDL 特征概述

#### a. SQL 数据定义

&emsp;&emsp;数据库中的关系集合必须由 DDL 指定给系统，SQL 的 DDL 能够定义关系以及每个关系的信息，包括：
+ 每个关系的模式。
+ 每个属性的取值类型。
+ 完整性约束。
+ 每个关系维护的索引集合。
+ 每个关系的安全性和权限信息。
+ 每个关系在磁盘上的物理存储结构。

&emsp;&emsp;SQL 支持的基本固有类型：
+ `char(n)`：固定长度的字符串，用户指定长度 n，全称 character。固定长度的字符串会追加空格使其达到固定的串长度。
+ `varchar(n)`：可变长度的字符串，用户指定最大长度 n，全称 character varying。
+ `int`：整数类型，全称 integer。
+ `smallint`：小整数类型。
+ `numeric(p, d)`：定点数，精度由用户指定。这个数有 p 位数（加上一个符号位），其中 d 位数字在小数点右边。
+ `real, double precision`：浮点数与双精度浮点数，精度与机器相关。
+ `float(n)`：精度至少为 n 位的浮点数。

&emsp;&emsp; SQL 也提供 `nvarchar` 类型存放 Unicode 数据，很多数据库允许在 varchar 类型中存放 Unicode（采用 UTF-8）。

&emsp;&emsp;SQL 的基本模式定义：create table 命令定义 SQL 关系。create table 命令的通用形式是：
```sql
    create table r ( A1  D1, 
                     A2  D2, 
                     ..., 
                     An  Dn, 
                     <完整性约束1>,
                     ...,
                     <完整性约束k> );
```
&emsp;&emsp;其中 r 是关系名，Ai 是关系 r 模式中的一个属性名，Di 是属性 Ai 的域。SQL 支持的几个完整性约束：
+ `primary key (Aj1, Aj2, ..., Ajm)` ：primary-key 声明表示属性 Aj1、Aj2、...、Ajm 构成关系的主码。主码属性必须非空且唯一。
+ `foreign key (Ak1, Ak2, ..., Akn) references s` ：foreign key声明表示关系中任意元组在属性（Ak1,Ak2,...,Akn）上的取值必须对应于关系 s 中某元组在主码属性上的取值。
+ `not null`：一个属性上的 not null 约束表明在该属性上不允许空值。

&emsp;&emsp;大学数据库的部分SQL数据定义：

![3_1](/assets/img/2022-12-06-Database-System-Concepts/3_1.png)

&emsp;&emsp;SQL禁止破坏完整性约束的任何数据库更新。  
&emsp;&emsp;命令 `drop table r;` 从数据库中删除关系 r。`alter table r add A D;` 为已有的关系 r 增加域为 D 的属性 A，关系 r 中所有元组在新属性 A 上的取值将被设为 null。命令 `alter table r drop A;` 从关系 r 中去掉属性 A。

#### b. SQL 查询的基本结构

&emsp;&emsp;在关系模型的形式化数学定义中，关系是一个集合。然而在实践中，去除重复是相当费时的，所以SQL允许在关系以及SQL表达式结果中出现重复。  
&emsp;&emsp;SQL查询的基本结构由三个子句构成：select、from 和 where。查询的输入是在 from 子句中列出的关系，where 和 select 子句指定运算，查询的结果元组构成一个关系。可在 select 子句后加入关键字 distinct 强行删除重复，使用关键字 all 显式指明不删除重复，不过保留重复元组是默认的。select 子句可带含有数学运算符的算术表达式，运算对象可以是常数或元组的属性。where 子句在 from 子句的结果关系中选出满足特定谓词的元组，可以在 where 子句中使用逻辑连词 and、or、not，也可包含比较运算符来比较字符串、算术表达式以及特殊类型，比如日期。  
&emsp;&emsp;select 子句列出查询结果中所需要的属性；from 子句列出查询求值中所需要访问的关系列表，where 子句则列出作用在 from 子句中关系的属性上的谓词。理解查询所代表运算最容易的方式是以运算的顺序来考察各子句：首先是 from，然后是 where，最后是 select。from 子句定义了一个在该子句中所列出关系上的笛卡儿积。where 子句中的谓词用来限制笛卡儿积所建立的组合，只留下那些对所需答案有意义的组合。  
&emsp;&emsp;一个SQL查询的含义理解：
+ 为from子句中列出的关系产生笛卡儿积。
+ 在上一步的结果上应用 where 子句中指定的谓词。
+ 对于上一步结果中的每个元组，输出 select 子句中指定的属性（或表达式的结果）。

&emsp;&emsp;SQL的实际实现会尽可能地只产生满足 where 子句谓词的笛卡儿积元素来进行优化执行，因为笛卡儿积是一个巨大的关系。利用笛卡儿积和where子句谓词可以连接来自多个关系的信息。SQL还支持自然连接运算，自然连接（natural join）运算作用于两个关系，并产生一个关系作为结果。不同于两个关系上的笛卡儿积，它将第一个关系的每个元组与第二个关系的所有元组都进行连接；自然连接只考虑那些在两个关系模式中都出现的属性上取值相同的元组对。

```sql
-- 自然连接的形式：
        select A1, A2, ..., An
        from r1 natural join r2 natural join ... natural join rm
        where P;
-- 更为一般地，from 子句可以为如下形式：
        from E1, E2, ..., En
-- 其中每个Ei可以是单个关系或一个包含自然连接的表达式。一个例子：
        select name, title
        from instructor natural join teaches, course
        where teaches.course_id = course.course_id;
-- 先计算 instructor 和 teaches 的自然连接，
-- 计算该结果和 course 的笛卡儿积，
-- where 子句从笛卡儿积结果里提取 course_id 属性相匹配的元组。
-- SQL提供了一种自然连接的构造形式，允许用户指定需要哪些列相等，而不需要在所有共有属性上的相等匹配：
        select name, title
        from (instructor natural join teaches) join course using (course_id);
-- join...using 运算中需要给定一个属性名列表，其两个输入中都必须具有指定名称的属性。
```

#### c. SQL支持的几种附加的基本运算
+ 更名运算，SQL 提供了一个重命名结果关系中属性的方法：`old-name as new-name`。as 子句既可以出现在 select 子句中，也可出现在 from 子句中，用来重命名关系，例如把一个长的关系名替换成短的，在查询的其他地方使用就更为方便。重命名关系的另一个原因是为了适用于需要比较同一个关系中的元组的情况。
+ 字符串运算。
    - SQL 使用一对单引号来标示字符串。
    - 在SQL标准中，字符串上的相等运算符是大小写敏感的，具体的数据库系统可以在其特性中设置默认的比较方式是否大小写敏感。
    - 字符串上可以使用 like 操作符来实现模式匹配。% 匹配任意字串，下划线（_）匹配任意一个字符。模式是大小写敏感的。
    - SQL 允许定义转义字符，转义字符放在特殊字符的前面，表示该特殊字符被当成普通字符：
        + `like 'ab\%cd%' escape '\'` 匹配所有以 ab%cd 开头的字符串。
        + `like 'ab\\cd%' escape '\'` 匹配所有以 ab\cd 开头的字符串。
    - SQL 允许使用 not like 比较运算符搜寻不匹配项。
    - 在 SQL:1999 中提供了 similar to 操作，类似 UNIX 中的正则表达式。
+ 形如 `select * ` 的子句表示 from 子句结果关系中的所有属性都被选中。
+ SQL 提供对关系中元组显示次序的控制，order by 子句让查询结果中元组按指定的属性列顺序显示，默认使用升序。可以用 desc 表示降序，asc 表示升序。排序可以在多个属性上进行。
+ between and 比较运算符在 where 子句中说明一个值是小于或等于某个值，同时大于或等于另一个值的。类似地，还有 not between 比较运算符。
+ where 子句中可以按元组形式运用比较运算符，按字典顺序进行比较运算。

#### d. 集合运算

&emsp;&emsp;SQL 作用在关系上的 union 、intersect 和 except 运算对应于数学集合论中的 U、∩、- 运算。 union 运算自动去除重复，想保留重复需用 union all 代替 union 。intersect 同 union，不同的是，union all 重复元组数是两个关系中的重复元组数的和，而 intersect all 的重复元组数等于两个关系中出现的重复次数里最少的那个。except 运算从其第一个输入中输出所有不出现在第二个输入中的元组，即执行集差操作，此操作自动去除输入中的重复。except all 结果中的重复元组数等于第一个输入元组数减去第二个输入元组数。在 Oracle 中，使用关键字 minus 代替 except。

#### e. 空值

+ 如果算术表达式的任一输入为空，则该算术表达式（+、-、*或/）结果为空。
+ SQL 将涉及空值的任何比较运算的结果视为 unknown（既不是谓词 is null，也不是 is not null），这创建了除 true 和 false 之外的第三个逻辑值。
+ and、or 和 not 也可以处理 unknown 值（遇到 unknown 为 unknown，短路则为短路值）：
    - and: true and unknown == unknown; false and unknown == false; unknown and unknown == unknown
    - or: true or unknown == true; false or unknown == unknown; unknown or unknown == unknown
    - not: not unknown == unknown
+ SQL 在谓词中使用特殊的关键词 null 测试空值（is null 以及 is not null）。
+ 某些 SQL 实现允许使用 is unknown 和 is not unknown 测试一个表达式的结果是否为 unknown ，而不是 true 或 false。
+ 如果元组在所有属性上的取值相等，即使某些相等的值同时为空，它们也被当作相同元组。因为只有在谓词中 null=null 会返回 unknown，其他需要比较的情况（比如 distinct 以及集合的并、交和差运算）可以当作相等，也就是结果为 true 看待。

#### f. 聚集函数

&emsp;&emsp;聚集函数是以值的一个集合（集或多重集）为输入，返回单个值的函数。SQL 提供了五个固有聚集函数：
+ 平均值：avg。
+ 最小值：min。
+ 最大值：max。
+ 总和：sum。
+ 计数：count。

&emsp;&emsp;sum 和 avg 的输入必须是数字集。聚集时保留重复元素是很重要的（默认的），但也可在聚集函数中使用关键字 distinct 去除重复然后再进行聚集操作。

```sql
-- 找出2010年春季学期至少讲授了一门课程的教师总数：
        select count(distinct ID)
        from teaches
        where semester = 'Spring' and year = 2010;
-- 由于在 ID 前面有关键字 distinct，所以即使某位教师教了不止一门课程，在结果中也仅被计算一次。
-- 找出 course 关系中的元组数：
        select count(*)
        from course;
-- SQL 不允许在用 count(*) 时使用 distinct。
```

分组聚集：聚集函数除了作用在单个元组集上，还可以作用到一组元组集上（使用 group by 分组）。
+ group by 子句中给出的一个或多个属性是用来构造分组的。在 group by 子句中的所有属性上取值相同的元组将被分在一个组中，且在结果中，每个分组只输出一个元组。因此，在SQL查询中使用分组时，需要保证出现在 select 语句中但没有被聚集的属性只能是出现在 group by 子句中的那些属性。
+ having 子句用于对 group by 子句构成的分组限定条件，而不是对元组限定条件（where子句）。having 子句中的谓词在形成分组后才起作用，因此可以使用聚集函数。任何出现在 having 子句中但没有被聚集的属性必须出现在 group by 子句中，这与 select 子句的情况类似。

&emsp;&emsp;对包含聚集、group by 或 having 子句的查询的操作序列的理解：
+ 根据 from 子句计算出一个关系。
+ 将 where 子句的谓词应用到 from 子句的结果关系上。
+ 满足 where 谓词的元组通过 group by 子句形成分组。如果没有 group by 子句，满足 where 谓词的整个元组集被当作一个分组。
+ 将 having 子句的谓词应用到每个分组上；不满足 having 子句谓词的分组将被抛弃。
+ select 子句利用剩下的分组产生查询结果中的元组，一个分组产生一个元组，即在每个分组上应用聚集函数来得到单个结果元组。

```sql
-- 找出教师平均工资超过 42000 美元的系的名称和平均工资：
        select dept_name, avg(salary) as avg_salary
        from instructor
        group by dept_name
        having avg(salary) > 42000;
-- 找出 2009 年讲授的至少有2名学生选的课程信息以及它们的所有选修学生的总学分的平均值：
        select course_id, semester, year, sec_id, avg(tot_cred)
        from takes natural join student
        where year = 2009
        group by course_id, semester, year, sec_id
        having count(ID) >= 2;
```

&emsp;&emsp;除了 count(*) 外的所有聚集函数都忽略输入集合中的空值。规定空集的 count 运算值为 0，其他所有聚集运算在输入为空集的情况下返回一个空值。  
&emsp;&emsp;some 和 every 两个聚集函数可用来处理布尔值（false、true、unknown）的集合。

#### g. 嵌套子查询
&emsp;&emsp;SQL 提供嵌套子查询，子查询是嵌套在另一个查询中的 select-from-where 表达式。
+ 子查询嵌套在 where 子句中，通常用于对集合的成员资格、集合的比较以及集合的基数进行检查。
    - 成员资格：连接词 in 测试元组是否是集合（由 select 子句产生的一组值构成）中的成员；连接词 not in 测试元组是否不是集合中的成员。in 和 not in 操作符也能用于枚举集合，也能在多属性关系中测试成员资格。
    - 集合的比较。some 关键字表示某一个，all 关键字表示所有的。= some 等价于 in，<> all 等价于 not in。关键字 any 与 some 同义。

    ```sql
    -- 找出工资比生物系教师的所有工资集合中的某一成员高的教师名字：
            select name
            from instructor
            where salary > some (select salary
                                 from instructor
                                 where dept_name = 'Biology');
    -- 找出工资比生物系教师的所有工资集合中的每一成员都高的教师名字：
            select name
            from instructor
            where salary > all (select salary
                                from instructor
                                where dept_name = 'Biology');

    -- <some, <=some, >=some, =some 和 <>some 以及 <all, <=all, >=all, =all 和 <>all 都是允许的。
    ```
    
    - exists 测试在作为参数的子查询结果中是否存在元组；not exists 测试子查询结果集中是否不存在元组。满足相应条件时，返回 true。
    - unique 测试在作为参数的子查询结果中是否存在重复元组。not unique 测试在子查询结果集中是否不存在重复元组。满足相应条件时，返回 true。
+ SQL 允许在 from 子句中使用子查询表达式。因为子查询表达式返回的结果都是关系。可以用 as 子句给子查询的结果关系起个名字，并对属性进行重命名。在 from 子句的子查询用关键字 lateral 作为前缀可以访问 from 子句中在它前面的表或子查询中的属性。

    ```sql
    -- 找出系平均工资超过42000美元的那些系的教师平均工资：
            select dept_name, avg_salary
            from (select dept_name, avg(salary) as avg_salary
                  from instructor
                  group by dept_name)
                  as dept_avg (dept_name, avg_salary)
            where avg_salary > 42000;

    -- 找出在所有系中工资总额最大的系的工资：
            select max(tot_salary)
            from (select dept_name, sum(salary)
                  from instructor
                  group by dept_name) as dept_total (dept_name, tot_salary);

    -- 打印每位教师的姓名，以及他们的工资和所在系的平均工资：
            select name, salary, avg_salary
            from instructor I1, lateral (select avg(salary) as avg_salary
                                         from instructor I2
                                         where I2.dept_name = I1.dept_name);
    ```
+ with 子句提供定义临时关系的方法，这个定义只对包含 with 子句的查询有效。
    ```sql
    -- 找出具有最大预算值的系：
            with max_budget (value) as
                (select max(budget)
                 from department)
            select budget
            from department, max_budget
            where department.budget = max_budget.value;
    -- 查找工资总额大于等于所有系平均工资总额的系：
            with dept_total (dept_name, value) as
                (select dept_name, sum(salary)
                 from instructor
                 group by dept_name),
                 dept_total_avg (value) as
                (select avg(value)
                 from dept_total)
            select dept_name
            from dept_total, dept_total_avg
            where dept_total.value >= dept_total_avg.value;

    ```
+ 标量子查询。SQL 允许子查询出现在返回单个值的表达式能够出现的任何地方，只要该子查询只返回包含单个属性的单个元组，这样的子查询被称为标量子查询（scalar subquery），SQL 会从该结果关系中包含单属性的单元组中取出相应的值，并返回该值。

    ```sql
    -- 列出所有的系以及它们拥有的教师数：
            select dept_name,
                   (select count(*)
                    from instructor
                    where department.dept_name = instructor.dept_name)
                    as num_instructors
            from department;
    ```

#### h. 数据库的修改

+ 删除。

    ```sql
    -- 其中 P 代表一个谓词，r 代表一个关系，
    -- delete 语句从 r 中找出所有使 P(t) 为真的元组 t，然后把它们从 r 中删除:
            delete from r
            where P;
    -- 删除 instructor 关系中的所有元组：
            delete from instructor;
    -- 删除Finance系的教师元组：
            delete from instructor
            where dept_name = 'Finance';
    -- 删除位于 Watson 大楼的系的教师元组：
            delete from instructor
            where dept_name in (select dept_name
                                from department
                                where building = 'Watson');
    -- 删除工资低于平均工资的教师记录：
            delete from instructor
            where salary < (select avg(salary)
                            from instructor);
    -- 在执行任何删除前都会先进行每一个元组的是否删除的谓词测试检查，
    -- 然后再是删除每一个符合条件的元组，从而以防结果依赖于元组被处理的顺序。
    ```

+ 插入

    ```sql
    -- 指定待插入的元组：
            insert into course
                value ('CS-437', 'Database Systems', 'Comp. Sci', 4);
            insert into course (course_id, title, dept_name, credits)
                value ('CS-437', 'Database Systems', 'Comp. Sci', 4);
    -- 写一条查询语句生成待插入的元组集合:
            insert into instructor
                select ID, name, dept_name, 18000
                from student
                where dept_name = 'Music' and tot_cred > 144;
    ```

+ 更新

    ```sql
            update instructor
            set salary = salary * 1.05;

            update instructor
            set salary = salary * 1.05
            where salary < 7000;

            update instructor
            set salary = salary * 1.05
            where salary < (select avg(salary)
                            from instructor);
            
            update instructor
            set salary = case
                        when salary <= 100000 then salary * 1.05
                        else salary * 1.03
                    end
    ```

&emsp;&emsp;case 语句的一般格式如下：

```sql
            case
                when pred1 then result1
                when pred2 then result2
                ...
                when predn then resultn
                else result0
            end
```

&emsp;&emsp;当 predi 第一个满足时，就会返回 resulti。如果没有一个谓词可以满足，则返回 result0。case 语句可以用在任何应该出现值的地方。

- 标量子查询在 SQL 更新语句中：

    ```sql
    -- 计算学生的总学分并更新，总学分即他成功学完的课程学分的总和：
            update student S
            set tot_cred = (
                select case
                            when sum(credits) is not null then sum(credits)
                            else 0
                        end
                from takes natural join course
                where S.ID = takes.ID and
                    takes.grade <> 'F' and
                    takes.grade is not null
            );
    ```

## 3. 中级 SQL

### （1）连接表达式

#### a. 连接条件
&emsp;&emsp;join ... using 子句是一种自然连接的形式，需要在指定属性上的取值匹配。SQL 支持指定任意的连接条件，on 条件允许在参与连接的关系上设置通用的谓词，在写法上与 where 子句类似。与 using 条件一样，on 条件出现在连接表达式的末尾。  
&emsp;&emsp;注意，在自然连接的结果关系中，共有属性只会出现在结果关系中一次，而笛卡儿积的结果尽管指定了 where 条件或 on 条件，共有属性也会在结果中出现两次。

```sql
            select * from student join takes on student.ID = takes.ID;
            -- 等价于：
            select * from student, takes where student.ID = takes.ID;
            -- 结果中，ID 属性出现两次，分别表示为 student.ID, takes.ID
            -- 只显示一次 ID 值：
            select student.ID as ID, name, dept_name, tot_cred, course_id,
                sec_id, semester, year, grade
            from student join takes on student.ID = takes.ID;
```

&emsp;&emsp;on 条件可以表示任何 SQL 谓词，从而使用 on 条件的连接表达式就可以表示比自然连接更为丰富的连接条件。on 子句中的谓词可以移到 where 子句中，但在被称作外连接的这类连接中 on 条件的表现与 where 条件是不同的。通常，在 on 子句中指定连接条件，而在 where 子句中指定其余条件，这样的 SQL 查询更容易让人读懂。

#### b. 外连接

&emsp;&emsp;在自然连接中的两个关系中，没有匹配的元组将不会出现在结果关系中。因此，在参与连接的任何一个或两个关系中的某些元组可能会因无匹配而丢失。外连接（outer join）运算通过在结果中创建包含空值元组的方式，保留了那些在连接中丢失（无匹配）的元组。实际上有三种形式的外连接：

+ 左外连接（left outer join）只保留出现在左外连接运算之前（左边）的关系中的元组，这些元组的右边关系属性被赋为空值。
+ 右外连接（right outer join）只保留出现在右外连接运算之后（右边）的关系中的元组，这些元组的左边关系属性被赋为空值。
+ 全外连接（full outer join）保留出现在两个关系中的元组，这些元组无匹配的关系属性被赋为空值。

&emsp;&emsp;为了与外连接运算相区分，此前的**不保留未匹配元组**的连接运算被称作内连接（inner join）运算。

```sql
            select * from student natural left outer join takes;
            -- 左外连接和右外连接是对称的：
            select * from takes natural right outer join student;
            -- 这两个的结果是一样的，只是结果中属性出现的顺序不同。

            -- 显示 Comp.Sci. 系的所有学生和他们在2009年春季的选修的所有课程段列表，为选秀的课程也显示出来
            select * from (select * 
                           from student 
                           where dept_name = 'Comp.Sci.')
                     natural full outer join
                        (select *
                         from takes
                         where semester = 'Spring' and year = 2009);
```

&emsp;&emsp;on 子句可以和外连接一起使用。on 是连接条件，作用于连接过程，产生 from 的结果集。where 是作用于 from 结果集的谓词。

```sql
            select *
            from student left outer join takes on student.ID = takes.ID;
            -- student 中无匹配的元组会出现在结果集中。
            -- 在生成笛卡儿积的过程中，on 条件因 takes.ID 不存在不满足时，左外连接加入空元组。
            select *
            from student left outer join takes on true
            where student.ID = takes.ID;
            -- student 中无匹配的元组不会出现在结果集中。
            -- 在生成笛卡儿积的过程中，on 条件一直满足，生成了完整的笛卡儿积，
            -- 在 where 谓词筛选时，不满足的元组被排除，包括 student 中的无匹配的那些笛卡儿积结果。

```

#### c. 连接类型和条件
&emsp;&emsp;SQL 中把常规连接称作内连接，关键字 inner 是可选的，没有 outer 前缀的 join 默认是 inner join。  
&emsp;&emsp;任意的连接类型（内连接、左外连接、右外连接、全外连接）可以和任意的连接条件（自然连接、using 条件连接或 on 条件连接）进行组合：

![4_6](/assets/img/2022-12-06-Database-System-Concepts/4_6.png)

### （2）视图

&emsp;&emsp;让所有用户都看到整个逻辑模型是不合适的，有时我们还希望创建一个比逻辑模型更符合特定用户的直觉的个人化的关系集合。SQL 允许通过查询定义“虚关系”，它在概念上包含查询的结果，但并不预先计算并存储，而是在使用虚关系的时候才通过执行查询被计算出来。任何像这种不是逻辑模型的一部分，但作为虚关系对用户可见的关系称为**视图（view）**。在任何给定的实际关系集合上能够支持大量视图。

#### 视图定义：

```sql
            create view v as <query expression>;
        -- v 是视图名称，<query expression> 是计算视图的查询，可以是任何合法的查询表达式。

            create view faculty as
            select ID, name, dept_name
            from instructor;

            create view physics_fall_2009 as
            select course.course_id, sec_id, buiding, room_number
            from course, section
            where course.course_id = section.course_id
                and course.dept_name = 'Physics'
                and section.semester = 'Fall'
                and section.year = 2009;
```

&emsp;&emsp;视图关系在概念上包含查询结果中的元组，但并不进行预计算和存储。数据库系统存储与视图关系相关联的查询表达式，当视图关系被访问时，其中的元组才被计算出来。即，视图关系是在需要的时候才被创建的。

#### SQL 查询中使用视图

```sql
        -- 在查询中，视图名可以出现在关系名可以出现的任何地方：
            select course_id
            from physics_fall_2009
            where buiding = 'Wastson';
        -- 视图的属性名可以按下述方式显式指定：
            create view departments_total_salary(dept_name, total_salary) as
                select dept_name, sum(salary)
                from instructor
                group by dept_name;
        -- 一个视图可以被用到定义另一个视图的表达式中：
            create view physics_fall_2009_watson as
                select course_id, room_number
                from physics_fall_2009
                where buiding = 'Watson';
```

#### 物化视图

&emsp;&emsp;特定数据库系统允许存储视图关系，但它们保证当定义视图的实际关系改变，视图也跟着修改。这样的视图被称为**物化视图（materialized view）**。保持物化视图一直在最新状态的过程称为**物化视图维护（materialized view maintenance）**，或者通常简称**视图维护（view maintenance)**。物化视图查询带来了许多好处，但是也需要与存储代价和更新开销相权衡。

#### 视图更新

&emsp;&emsp;用视图表达的数据库修改必须被翻译为对数据库逻辑模型中实际关系的修改。因此，除了一些有限的情况外，一般不允许对视图关系进行修改。不同的数据库系统指定了不同的条件以允许更新视图关系。一般来说，SQL 视图是可更新的（updatable），即在视图上可以执行插入、更新或删除，需满足下列条件：
+ from 子句中只有一个数据库关系。
+ select 子句中只包含关系的属性名，不包含任何表达式、聚集或 distinct 声明。
+ 任何没有出现在 select 子句中的属性可以取空值，即没有 not null 约束，也不构成主码的一部分。
+ 查询中不含有 group by 或 having 子句。

```sql
            create view history_instructors as
            select *
            from instructor
            where dept_name = 'History';
        -- 可以在视图定义的末尾包含 with check option 子句，
        -- 表明像视图插入/更新一条不满足视图的where子句条件的元组时，数据库将拒绝操作。
        -- 比如向上面的视图插入元组 ('25566', 'Brown', 'Biology', 100000)。
```

### （3）事务

&emsp;&emsp;事务（transaction）由查询和（或）更新语句的序列组成。SQL 标准规定当一条 SQL 语句被执行，就隐式开启了一个事务。下列 SQL 语句之一会结束一个事务：
+ Commit work ：提交当前事务，将该事务所做的更新在数据库中持久保存。在事务被提交后，一个新的事务自动开始。
+ Rollback work ：回滚当前事务，撤销该事务中所有 SQL 语句对数据库的更新。

&emsp;&emsp;关键字 work 在两条语句中都是可选的。一旦某事务执行了 commit work ，它的影响就不能用 rollback work 来撤销了。数据库系统保证在发生诸如某条 SQL 语句错误、断电、系统崩溃这些故障的情况下，影响将被回滚。在断电和系统崩溃的情况下，回滚会在系统重启后执行。  
&emsp;&emsp;一个事务或者在完成所有步骤后提交其行为，或者在不能成功完成其所有动作的情况下回滚其所有动作，通过这种方式数据库提供了对事物具有原子性的抽象，原子性也就是不可分割性。  
&emsp;&emsp;在很多 SQL 实现中，默认方式下每个 SQL 语句自成一个事务，且一执行完就提交。可以关闭单独 SQL 语句的自动提交，从而执行多条SQL语句。作为 SQL:1999 的一部分，允许多条 SQL 语句包含在关键字 begin atomic ... end 之间，所有在关键字之间的语句构成了一个单一事务。

### （4）完整性约束

&emsp;&emsp;完整性约束保证授权用户对数据库所做的修改不会破坏数据的一致性，防止对数据的意外破坏。完整性约束可以是属于数据库的任意谓词（需极小开销的），通常被看成是数据库模式设计过程的一部分，它作为用于创建关系的 create table 命令的一部分被声明。也可以通过使用 alter table *table-name* add *constraint* 命令施加到已有关系上，constraint 可以是关系上的任意约束，前提是关系满足指定的约束。

create table 命令允许的完整性约束包括：
+ `not null` 。空值是所有域的成员，因此默认情况下是每个属性的合法值。可以通过 not null 声明限定属性的域排除空值，从而禁止在该属性上插入空值。SQL 禁止在关系模式的主码中出现空值，因此主码属性默认声明了 not null 。
+ `unique (Aj1, Aj2, ..., Ajm)` 。unique 声明指出属性 Aji,Aj2,...,Ajm 形成了一个候选码，即在关系中没有两个元组能在所有列出的属性上取值相同。然而不同于主码，候选码的属性可以为 null，因此 unique 不会默认声明 not null。空值不等于其他的任何值。
+ `check(<谓词>)` 。check(P) 子句指定一个谓词 P，关系中的每个元组都必须满足谓词 P。通常用 check 子句来保证属性值满足指定的条件，实际上创建了一个强大的类型系统。
+ 参照完整性。不同于外码约束，参照完整性约束通常不要求 referencing relation 中的属性集 K1 是 referenced relation 的主码，其结果是 referenced relation 中可能有不止一个元组在属性 K1 上取值相同。references 子句显式指定 referenced 关系的属性列表，这个指定的属性列表必须声明为 referenced relation 的候选码，要么使用 primary key 约束，要么使用 unique 约束。SQL 也提供了另外的结构用于实现被参照关系的被参照属性不必是候选码的形式。

    ```sql
            -- 一种作为属性定义的一部分声明为外码的简写形式：
                create table course (
                    dept_name varchar(20) references department,
                    ...
                );
            -- 当被参照关系上的删除或更新动作违反了约束，
            -- 可指明系统采取一些步骤修改参照关系中的元组来恢复完整性约束，而不是拒绝这样的动作：
                create table course (...
                    foreign key (dept_name) references department
                        on delete cascade
                        on update cascade,
                ...);
    ```

    - `on delete cascade` 子句，对参照关系做“级联”删除，即删除参照了被删除系的元组。
    - `on update cascade` 子句，对参照关系做“级联”更新，即更新参照了被更新系的元组的系名。
    - `on delete set null` (`on update set null`) 子句，将参照域置为 null。
    - `on delete set default` ，将参照域置为默认值。
    - 如果存在设计多个关系的外码依赖链，则在链一端所做的删除或更新可能传至整个链。
    - 如果一个级联更新或删除导致的对约束的违反不能通过进一步的级联操作解决，则系统中止该事务。

 + 大学数据库的部分SQL数据定义：

    ![4_8](/assets/img/2022-12-06-Database-System-Concepts/4_8.png)

#### 事务中对完整性约束的违反

&emsp;&emsp;SQL 标准允许将 initially deferred 子句加入到约束声明中，使这种完整性约束在事务结束时检查，而不是在事务的中间步骤上检查。一个约束在默认方式下是立即检查约束。对于声明为可延迟的约束，执行 set constraints *constraint-list* deferred 语句作为事务的一部分，会导致对指定约束的检查被延迟到该事务结束时执行。

#### 复杂 check 条件与断言

```sql
        -- check 子句中的谓词可以是包含子查询的任意谓词。
            check (time_slot_id in select time_slot_id from time_slot)
        -- SQL 中的断言为如下形式：
            create assertion <assertion-name> check <predicate>;
        -- 一个断言例子：
        -- 对于 student 关系的每个元组在 tot_cred 上的取值必须等于该生成功修完课程的学分总和：
            create assertion credits_earned_constraint check
                (not exists (select ID
                            from student
                            where tot_cread <> (select sum(credits) 
                                                from takes natural join course
                                                where student.ID = takes.ID
                                                and grade is not null and grad <> 'F')));
```

### （5）SQL 的数据类型与模式

### （6）授权

## 4. 高级 SQL

