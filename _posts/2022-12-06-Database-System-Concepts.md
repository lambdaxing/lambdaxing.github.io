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

            -- 显示 Comp.Sci. 系的所有学生和他们在2009年春季的选修的所有课程段列表，未选修的课程也显示出来
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

&emsp;&emsp;注意，外连接也好，内连接也好，连接过程都要遵循连接条件的要求，即都是在连接条件的要求下进行连接。特别是外连接，需要按连接条件来判定是否加入空元组以及怎样加入空元组。这也是外连接中，on 子句不同于 where 子句的原因。

### （2）视图

&emsp;&emsp;让所有用户都看到整个逻辑模型是不合适的，有时我们还希望创建一个比逻辑模型更符合特定用户的直觉的个人化的关系集合。SQL 允许通过查询定义“虚关系”，它在概念上包含查询的结果，但并不预先计算并存储，而是在使用虚关系的时候才通过执行查询被计算出来。任何像这种不是逻辑模型的一部分，但作为虚关系对用户可见的关系称为**视图（view）**。在任何给定的实际关系集合上能够支持大量视图。

#### a. 视图定义：

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

#### b. SQL 查询中使用视图

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

#### c. 物化视图

&emsp;&emsp;特定数据库系统允许存储视图关系，但它们保证当定义视图的实际关系改变，视图也跟着修改。这样的视图被称为**物化视图（materialized view）**。保持物化视图一直在最新状态的过程称为**物化视图维护（materialized view maintenance）**，或者通常简称**视图维护（view maintenance)**。物化视图查询带来了许多好处，但是也需要与存储代价和更新开销相权衡。

#### d. 视图更新

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
        -- 表明向视图插入/更新一条不满足视图的where子句条件的元组时，数据库将拒绝操作。
        -- 比如向上面的视图插入元组 ('25566', 'Brown', 'Biology', 100000)。
```

### （3）事务

&emsp;&emsp;事务（transaction）由查询和（或）更新语句的序列组成。SQL 标准规定当一条 SQL 语句被执行，就隐式开启了一个事务。下列 SQL 语句之一会结束一个事务：
+ Commit work ：提交当前事务，将该事务所做的更新在数据库中持久保存。在事务被提交后，一个新的事务自动开始。
+ Rollback work ：回滚当前事务，撤销该事务中所有 SQL 语句对数据库的更新。

&emsp;&emsp;关键字 work 在两条语句中都是可选的。一旦某事务执行了 commit work ，它的影响就不能用 rollback work 来撤销了。数据库系统保证在发生诸如某条 SQL 语句错误、断电、系统崩溃这些故障的情况下，影响将被回滚。在断电和系统崩溃的情况下，回滚会在系统重启后执行。  
&emsp;&emsp;一个事务或者在完成所有步骤后提交其行为，或者在不能成功完成其所有动作的情况下回滚其所有动作，通过这种方式数据库提供了对事务具有原子性的抽象，原子性也就是不可分割性。  
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

&emsp;&emsp;SQL 标准所支持的另外一些用于声明完整性约束的结构：

```sql
        -- check 子句中的谓词可以是包含子查询的任意谓词。
            check (time_slot_id in select time_slot_id from time_slot)
        -- 在 time_slot 关系改变时，该约束也需要检查。

        -- 一个断言就是一个谓词，它表达了我们希望数据库总能满足的一个条件。域约束和参照完整性是断言的特殊形式。
        -- SQL 中的断言为如下形式：
            create assertion <assertion-name> check <predicate>;
        -- 一个用 SQL 断言写出的约束示例：
        -- 对于 student 关系的每个元组在 tot_cred 上的取值必须等于该生成功修完课程的学分总和：
            create assertion credits_earned_constraint check
                (not exists (select ID
                            from student
                            where tot_cread <> (select sum(credits) 
                                                from takes natural join course
                                                where student.ID = takes.ID
                                                and grade is not null and grad <> 'F')));
```

&emsp;&emsp;当创建断言时，系统要检测其有效性。有效的断言只有不破坏断言的数据库修改才会被允许。复杂的断言会带来相当大的开销。数据库系统的触发器可以实现等价的功能。

### （5）SQL 的数据类型与模式

#### a. SQL 标准支持的与日期和时间相关的几种类型：
+ date ：日历日期，包括年（四位）、月和日。
+ time ：一天中的时间，包括小时、分和秒。time(p) 表示秒的小数点后的数字位数（默认为0）。time with timezone 把时区信息联通时间一起存储。
+ timestamp :date 和 time 的组合。timestamp(p) 同上（默认值为6），with timezone 同上。

```sql
    -- 日期和时间类型的值可按如下方式说明：
            date '2001-04-25'
            time '09:30:00'
            timestamp '2001-04-25 10:29:01.45'
    -- 可以利用 cast e as t 形式的表达式将一个字符串 e 转换成类型 t，其中 t 可以是 date、time、timestamp 中的一种。
            cast ‘2001-04-25’ as date
    -- 可以利用 extract(field from d) ，从 date 或 time 值 d 中提取出单独的域，
    -- 这里的域可以是 year, month, day, hour, minute, second, timezone_hour, timezone_minute 中的一种。
```

&emsp;&emsp;SQL 定义的获取当前日期和时间的函数：
+ current_date 返回当前日期。
+ current_time 返回当前时间（带有时区）。
+ localtime 返回当前的本地时间（不带时区）。
+ current_timestamp（带有时区），localtimestamp（本地日期和时间，不带时区）返回时间戳。

&emsp;&emsp;SQL 允许在上面的所有类型上运行比较运算，SQL 支持 interval 数据类型（时间间隔类型），允许在日期、时间和时间间隔上进行算术运算和比较运算。

#### b. 默认值

```sql
    -- SQL 允许为属性指定默认值：
            create table student
             ( ID varchar(5),
               name varchar(20) not null,
               dept_name varchar(20),
               tot_cred numeric(3, 0) default 0,
               primary key (ID));
```

#### c. 创建索引

&emsp;&emsp;在关系的属性上所创建的索引（index）是一种数据结构，它允许数据库系统高效地找到关系中那些在索引属性上取给定值的元组，而不用扫描关系中的所有元组。索引也可以建立在一个属性列表上。索引的实现包括一种被称作 B+ 树的索引，它是一种特别的、广泛使用的索引类型。如果用户提交的SQL查询可以从索引的使用中获益，那么SQL查询处理器就会自动使用索引，而不用读取整个关系。

```sql
    -- 很多数据库支持使用如下所示的语法形式来创建索引：
            create index studentID_index on student(ID);
    -- 上述语句在 student 关系的属性 ID 上创建了一个名为 studentID_index 的索引。
```

#### d. 大对象类型

&emsp;&emsp;SQL 提供字符数据的大对象数据类型（clob）和二进制数据的大对象数据类型（blob），这些类型中的“lob“代表“Large OBject"。

```sql
        -- 我们可以声明属性：
                book_review clob (10KB)
                image blob (10MB)
                movie blob (2GB)
```

&emsp;&emsp;一个应用通常用一个SQL查询来检索出一个大对象的“定位器”，然后在宿主语言中用这个定位器来操纵对象，应用本身也是用宿主语言书写的。这很像一个 read 函数调用从操作系统文件中读取数据。

#### e. 用户定义的类型

&emsp;&emsp;SQL 支持两种形式的用户定义数据类型。独特类型（distinct type）和结构化数据类型（structured data type，22 章介绍）。一个好的数据库应该有在概念层的类型系统，而不是仅有物理层的类型系统。因此，SQL 提供了独特类型的概念：

```sql
    -- 可以用 create type 子句来定义新类型：
            create type Dollars as numeric (12, 2) final;
            create type Pounds as numeric (12, 2) final;
    -- 一些系统实现允许忽略 final 关键字。新创建的类型可以用作关系属性的类型。
            create table department
                (dept_name varchar(20),
                 building varchar(15),
                 budget Dollars);
    -- 独特类型拥有强类型检查，不同类型之间的运算将导致编译错误。
    -- 一种类型的值可以被转换到另一个域：
            cast (department.budget to numeric(12, 2))
    -- SQL 提供了 drop type 和 alter type 子句来删除或修改以前创建过的类型。

    -- SQL 还有一个与独特类型相似但稍有不同的概念：域（domain）。
            create domain DDollars as numeric(12, 2) not null;
    -- 然而，类型和域有两个重大的差别：
    -- 1. 在域上可以声明约束，例如 not null，也可以为域类型变量定义默认值。
    -- 2. 域不是强类型。一个域类型的值可以被赋给另一个域类型，只要它们的基本类型是相容的。
    -- check 子句也能应用到域上，指定一个谓词，来自该域的任何变量都必须满足这个谓词。
            create domain YearlySalary numeric(8, 2)
                constraint salary_value_test check(value >= 29000.00);
    -- constraint salary_value_test 子句是可选的，用来给约束命名。
```

&emsp;&emsp;create type 和 create domain 结构是SQL标准的部分，并没被大多数数据库实现完全支持，且不同的数据库实现具有不同的语法和解释。

#### f. create table 的扩展

```sql
    -- SQL 提供创建与现有某个表的模式相同的表的扩展：
            create table temp_instructor like instructor;
    
    -- SQL 提供创建包含查询结果的表，即把查询的结果存储成一个新表：
            create table t1 as
                (select *
                 from instructor
                 where dept_name = 'Music')
            with data;
    -- 默认情况下，列的名称和数据类型都是从查询结果中推导出来的。
    -- 在关系名后面列出列名，可以给列显式指定名字。 
```

&emsp;&emsp;省略 with data 子句会创建表，但不载入数据。几种数据库实现都用不同的语法支持 create table ... like 和 create table ... as 的功能。  
&emsp;&emsp;create table ... as 语句与 create view 语句非常相似，都是用查询来定义，两者的区别在于视图与表的区别。

#### g. 模式、目录与环境

&emsp;&emsp;当代数据库系统提供了三层结构的关系命名机制。目录（catalog）、模式（schema）、关系/视图（SQL对象）。目录类似于操作系统的用户主（home）目录，也有另一术语名词“数据库“。  
&emsp;&emsp;当一个用户连接到数据库时，该连接就设置了该用户的默认目录和模式。一个关系的名字形如 catalog5.univ_schema.course 包含三部分。  
&emsp;&emsp;当有多个目录和模式可用时，不同的应用和不同的用户可以独立工作而不必担心命名冲突。默认目录和模式是为每个连接建立的SQL环境（SQL environment）的一部分，环境还包括用户标识（授权标识符）。通常所有的SQL语句（DDL和DML）都在一个模式的环境中运行。  
&emsp;&emsp;可以用 create schema 和 drop schema 语句来创建和删除模式。

### （6）授权

#### a. 权限的授予与收回：

```sql
    -- grant 语句用来授予权限：
            grant <权限列表>
            on <关系名或视图名>
            to <用户/角色列表>
    -- SQL 标准包括 select、insert、update 和 delete 权限。
            grant select on department to Amit, Satoshi;
            grant update (budget) on department to Amit, Satoshi;
    -- 用户名 public 指系统的所有当前用户和将来的用户。
    -- SQL 授权机制可以对整个关系或一个关系的指定属性授予权限，不允许对一个关系的指定元组授权。

    -- revoke 语句用来收回权限：
            revoke <权限列表>
            on <关系名/视图名>
            from <用户/角色列表>
    -- 收回之前授予的权限：
            revoke select on department from Amit, Satoshi;
            revoke update (budget) on department from Amit, Satoshi;
```

#### b. 角色

&emsp;&emsp;角色（role）的概念对应于人所具有的真实世界角色。在数据库中建立一个角色集，可以给角色授予权限就像给用户授权一样。每个数据库用户被授予一组他有权扮演的角色（也可能是空的）。

```sql
    -- 在 SQL 中创建角色：
            create role instructor;
    -- 给角色授权：
            grant select on takes
            to instructor;
    -- 授予角色给用户或其他角色：
            grant dean to Amit;
            create role dean;
            grant instructor to dean;
            grant dean to Satoshi;
```

&emsp;&emsp;一个用户或一个角色的权限包括：
+ 所有直接授予用户/角色的权限。
+ 所有授予给用户/角色所拥有角色的权限。

&emsp;&emsp;因此，可能存在着一个角色链。当一个用户登录到数据库系统时，在此会话中用户拥有所有直接授予他的权限，以及所有授予（直接或间接）该用户所拥有角色的权限。

#### c. 视图的授权

&emsp;&emsp;创建视图的用户在该视图上的权限是他在定义该视图的关系上所拥有的权限。

#### d. 模式的授权

&emsp;&emsp;只有数据库模式的拥有者才能够执行对模式的任何修改，诸如创建或删除关系，增加或删除关系的属性，以及增加或删除索引。SQL 提供了一种 references 权限，允许用户在创建关系时声明外码。

```sql
    -- 允许用户 Mariano 创建能够参照 department 关系的 dept_name 码的关系：
            grant references(dept_name) on department to Mariano;
```

&emsp;&emsp;因为外码约束限制了被参照关系上的删除和更新操作，外码的定义限制了其他用户将来的行为，因此需要有 references 权限。同理，check 约束有参照某个关系的子查询时，也需要被参照关系的 references 权限，因为参照了另一个关系的 check 约束限制了对该关系可能的更新。

#### d. 权限的转移

&emsp;&emsp;在相应的 grant 命令后面附加 with grant option 子句，可以在授权时允许接受者把得到的权限再传递给其他用户。

```sql
    -- 授予 Amit 在 department 上的 select 权限，并允许 Amit 将该权限授予给其他用户 
            grant select on department to Amit with grant option;
```

&emsp;&emsp;一个对象（关系/视图/角色）的创建者拥有该对象上的所有权限，包括给其他用户授权的权限。指定权限从一个用户到另一个用户的传递可以被表示为授权图（authorization graph），用户具有权限的充分必要条件是：当且仅当存在从授权图的根（即代表数据库管理员的顶点）到代表该用户顶点的路径。

![4_10](/assets/img/2022-12-06-Database-System-Concepts/4_10.png)

#### e. 权限的收回

&emsp;&emsp;从一个用户/角色那里收回权限可能导致其他用户/角色也失去该权限，这一行为称为级联收回。即当一个用户/角色的权限被收回时，这个用户/角色授予给了同一权限的其他用户/角色的这一权限也会被收回。

```sql
    -- revode 语句可以声明 restrict 来防止级联收回：
            revoke select on department from Amit, Satoshi restrict;
    -- 这种情况下，如果存在任何级联收回，系统就会返回错误，且不执行收权动作。
    -- 收回 grant option 权限（授权他人的权限），而不收回 select 权限：
            revoke grant option for select on department from Amit;
```

&emsp;&emsp;级联收回在许多情况下是不合适的。因此，SQL 允许权限由一个角色授予，而不是由用户来授予。默认情况下，当前的会话有一个关联的角色，默认是空的，可以通过执行 set role role_name 来设置当前角色，指定的角色必须已经授予给用户。在授予权限时，在授权语句后面加上 granted by current_role 子句，可以将授权人设置为当前会话所关联的角色，只要当前角色不为空。这样，当从当前用户收回（当前）角色或权限时，不会收回当前用户以当前角色作为授权人所授予的权限，即使这个用户是执行该授权的用户。

## 4. 高级 SQL

### （1）使用程序设计语言访问数据库

+ 动态SQL。
    - JDBC。
    - ODBC。
+ 嵌入式SQL。

### （2）函数和过程

&emsp;&emsp;开发者可以编写自己的函数和过程存储在数据库里并在SQL语句中调用。函数和过程允许“业务逻辑”作为存储过程记录在数据库中，并在数据库中执行。尽管有些业务逻辑能够被写成程序设计语言过程并完全存储在数据库以外，但把它们定义成数据库中的存储过程也有很多优点，比如多应用访问、耦合性增强等。  
&emsp;&emsp;SQL 允许通过其有关过程的组件或外部的程序设计语言来定义函数、过程和方法。

#### a. 声明和调用 SQL 函数和过程

```sql
    -- 声明一个函数：给定一个系的名字，返回该系的教师数目：
            create function dept_count(dept_name varchar(20))
                return integer
                begin
                declare d_count integer;
                    select count(*) into d_count
                    from instructor
                    where instructor.dept_name = dept_name
                return d_count;
                end;
    -- 使用 create or replace 而不是 create，对旧的函数进行替换。
    -- 调用一个函数：查询教师数大于 12 的所有系的名字和预算：
            select dept_name, budget
            from department
            where dept_count(dept_name) > 12;
```

&emsp;&emsp; SQL 支持返回关系作为结果的函数，这种函数称为表函数（table function）。表函数通常被看作**带参数的视图（parameterized view）**，它允许通过参数把视图的概念更加一般化。

```sql
    -- 声明一个函数：给定一个系的名字，返回这个系的所有教师信息的表：
            create function instructor_of(dept_name varchar(20))
                return table (
                    ID varchar(5),
                    name varchar(20),
                    dept_name varchar(20),
                    salary numeric(8, 2)
                )
            return table (
                select ID, name, dept_name, salary
                from instructor
                where instructor.dept_name = instructor_of.dept_name
            );
    -- 注意，在使用函数的参数时，需要加上函数名作为前缀，即上面的 instructor_of.dept_name
    -- 调用一个函数：查询金融系的所有教师：
            select * 
            from table (instructor_of('Finance'));

    -- SQL也支持过程：
            create procedure dept_count_proc(in dept_name varchar(20), out d_count integer)
                begin
                    select count(*) into d_count
                    from instructor
                    where instructor.dept_name = dept_count_proc.dept_name;
                end
    -- 关键字 in 和 end 分别表示待赋值的参数和为返回结果而在过程中设置值的参数。
    -- 可以从一个SQL过程中或者从嵌入式SQL中使用call语句调用过程：
            declare d_count integer;
            call dept_count_proc('Physics', d_count);
    -- SQL 的过程也支持同名，只要同名过程的参数个数不同。名称和参数个数用于标识一个过程。
    -- SQL 的函数也支持同名，只要同名函数的参数个数不同，或者对于相同参数个数的函数，至少有一个参数的类型不同。
```

#### b. 支持过程和函数的语言构造

&emsp;&emsp;SQL 所支持的过程和函数的语言构造被赋予了与通用程序设计语言相当的几乎所有功能。SQL 标准中处理这些构造的部分称为持久存储模块（Persistent Storage Module，PSM）。  
&emsp;&emsp;变量通过 declare 语句声明为任意合法的SQL类型，set 语句赋值。  
&emsp;&emsp;一个复合语句有 begin...end 的形式，在begin和end之间会包含复杂的SQL语句。可以在复合语句中声明局部变量。begin atomic ... end 的复合语句可以确保其中包含的所有语句作为单一的事务来执行。

```sql
    -- SQL 支持 while 和 repeat 语句：
            while <布尔表达式> do
                <语句序列>;
            end while
            repeat
                <语句序列>;
            until <布尔表达式>
            end repeat
    -- for 循环，它允许对查询的所有结果重复执行：
            declare n integer default 0;
            for r as
                    select budget from department
                    where dept_name = 'Music'
            do
                set n = n - r.budget
            end for
    -- 程序每次获取查询结果的一行，并存入for循环变量（r）中。
    -- 语句 leave 可用来退出循环，
    -- 而 iterate 表示跳过剩余语句从循环的开始进入下一个元组。

    -- SQL 支持的条件语句 if-then-else 语句：
            if <布尔表达式>
                then <语句或复合语句>
            elseif <布尔表达式>
                then <语句或复合语句>
            else <语句或复合语句>
            endif
    -- SQL 支持发信号通知异常条件（exception condition），以及声明句柄（handler）来处理异常：
            declare out_of_classroom_seats condition
            declare exit handler for out_of_classroom_seats
            begin
            <语句序列>
            end
    -- 在 begin 和 end 之间可以执行 signal out_of_classroom_seats 来引发一个异常。
    -- 第二句句柄的 exit 说明将会采取动作终止 begin end 中的语句。
    -- exit 的另一可选动作是 continue，它继续从异常语句的下一条语句开始执行。
    -- 除了明确定义的条件（out_of_classroom_seats），
    -- 还有一些预定义的条件：sqlexception、sqlwarning、not found。
```

&emsp;&emsp;一个有关SQL的过程化结构的大型例子：

![5-7](/assets/img/2022-12-06-Database-System-Concepts/5_7.png)

#### c. 外部语言过程

&emsp;&emsp;SQL 的过程化扩展并不被标准的方法所支持，不同的数据库产品都可能有不同的语法和语义。但是，SQL 允许在外部用一种程序设计语言定义函数或过程，然后从SQL查询和触发器的定义中来调用它。  

```sql
    -- 外部过程和函数可以这样指定：
            create procedure dept_count_proc(in dept_name varchar(20), 
                                             out count integer)
            language C
            external name '/usr/avi/bin/dept_count_proc'
            create function dept_count(dept_name varchar(20))
            return integer
            language C
            external name '/usr/avi/bin/dept_count'
```

### （3）触发器

&emsp;&emsp;触发器（trigger）是一条当数据库作修改时系统自动执行的语句。设置触发器机制的要求：
+ 指明执行触发器的条件。
    - 一个引起触发器被检测的事件
    + 一个触发器执行必须满足的条件
+ 指明触发器执行时的动作。

&emsp;&emsp;触发器可以用来实现未被SQL约束机制指定的某些完整性约束，以及当满足特定条件时对用户发警报或自动开始执行某项任务。但是，触发器系统通常不能执行数据库以外的更新。

#### a. SQL 中的触发器

```sql
    -- 使用触发器确保关系section中属性time_slot_id的参照完整性：
            create trigger timeslot_check1 after insert on section
                referencing new row as nrow
                for each row
                when (nrow.time_slot_id not in (
                        select time_slot_id
                        from time_slot))    -- time_slot 中不存在该 time_slot_id
                begin
                    rollback
                end;
                create trigger timeslot_check2 after delete on time_slot
                referencing old row as orow
                for each row
                when(orow.time_slot_id not in ( 
                        select time_slot_id
                        from time_slot)         -- 在 time_slot 中刚刚被删除的 time_slot_id
                and orow.time_slot_id in (
                        select time_slot_id
                        from section))          -- 在 section 仍含有该 time_slot_id 的引用
                begin
                    rollback
                end;
    -- 为了保证参照完整性，还必须为处理 section 和 time_slot 的更新来创建触发器。
            create trigger timeslot_check3 after update of section on (time_slot_id)
                referencing new row as nrow
                for each row
                when (nrow.time_slot_id not in (
                        select time_slot_id
                        from time_slot))
                begin
                    rollback
                end;
            create trigger timeslot_check4 after update of time_slot on (time_slot_id)
                referencing old row as orow
                for each row
                when(orow.time_slot_id not in (
                        select time_slot+id
                        from time_slot)
                and orow.time_slot_id in (
                        select time_slot_id
                        from section))
                begin
                    rollback
                end;
    -- 使用触发器维护 student 里元组的 tot_cred 属性，使其保持实时更新：
            create trigger credits_earned after update of takes on (grade)
                referencing new row as nrow
                referencing old row as orow
                for each row
                when nrow.grade <> 'F' and nrow.grade is not null
                    and (orow.grade = 'F' or orow.grade is null)
                begin atomic
                    update student
                    set tot_cred = tot_cred + 
                        (select credits
                         from course
                         where course.course_id = nrow.course_id)
                    where student.id = nrow.id;
                end;
```

&emsp;&emsp;许多数据库支持各种别的触发器事件。比如用户登录到数据库，或者当系统停止时，或者当系统设置改变的时候。

```sql
    -- 触发器可以在事件（insert、delete、update）之前激发：
    -- 插入分数值为空白时，用 null 代替：
            create trigger setnull before update of takes
                referencing new row as nrow
                for each row
                when (nrow.grade = ' ')
                begin atomic
                    set nrow.grade = null;
                end;
```

&emsp;&emsp;可以对引起插入、删除或更新的SQL语句执行单一动作，而不是对每个被影响的行执行一个动作。只需用 for each statement 子句替代 for each row 子句。可以用子句 referencing old table as 或 referencing new table as 来指向包含所有被影响的行的临时表（成为过渡表）。过渡表不能用于 before 触发器，但是可以用于 after 触发器，无论它们是语句触发还是行触发。  
&emsp;&emsp;触发器可以被设为有效或者无效。命令 alter trigger *trigger_name* disable（disable trigger *trigger_name*）设为无效。命令 drop trigger *trigger_name* 丢弃触发器。  
&emsp;&emsp;每个数据库系统都实现了自己的触发器语法，导致彼此不能兼容。这儿记录的是 SQL:1999 的触发器语法。

#### b. 何时不用触发器

+ 外码约束的 on delete cascade 特性无需触发器来替代。
+ 物化视图无需触发器来维护，数据库系统支持自动维护。
+ 触发器无需用来复制或备份数据库。现代的数据库系统提供了内置的相关工具。
+ 一些数据库系统允许触发器定义为 not for replication，保证触发器不会在数据库备份的时候在备份站点执行，或者提供一个系统变量用于指明该数据库是一个副本，数据库动作在其上是回放，触发器无需执行。这两种方案都不需要显式地将触发器设为失效或有效。

&emsp;&emsp;写触发器时，应特别小心。运行期的一个触发器错误会导致引发该触发器的动作语句失败。而且，一个触发器的动作可以引发另一个触发器，这可能导致一个无限的触发链。  
&emsp;&emsp;触发器是很有用的工具，但是如果有其他的候选方案就最好别用触发器。很多触发器的应用都可以用适当的存储过程来替换。

### （4）递归查询

### （5）高级聚集特性

### （6）OLAP

## 5. 形式化关系查询语言

### （1）关系代数

&emsp;&emsp;关系代数是一种过程化查询语言，它为广泛应用的SQL查询语言打下了基础。关系代数，顾名思义就是将关系看作代数，对其按照代数运算的方式作一些运算。关系代数的基本运算有：选择、投影、并、集合差、笛卡儿积和更名，用基本运算定义的附加运算有集合交、自然连接和赋值。

#### a. 基本运算

+ 选择（select）运算选出满足给定谓词的元组。这里的术语 select 对应于 SQL 中的 where。
+ 投影（project）运算返回作为参数的关系，但把某些属性排除在外。
+ 并运算。必须保证做并运算的关系是相容的，即它们的属性数目相同（同元），且对应属性的域也相同。
+ 集合差（set-difference）运算找出在一个关系中而不在另一个关系中的那些元组。必须保证集合差运算在相容的关系间进行。
+ 笛卡儿积（cartesian-product）运算。
+ 更名运算。
+ 关系代数的形式化定义：
    - 关系代数中的基本表达式有
        + 数据库中一个关系。
        + 一个常数关系。
    - E1 和 E2 是关系代数表达式，则关系代数中的一般表达式有：  
       ![6_1_2](/assets/img/2022-12-06-Database-System-Concepts/6_1_2.png)

#### b. 附加的关系代数运算

+ 集合交（intersection）。r ∩ s = r - (r - s) 。
+ 自然连接运算。
+ 外连接运算。
    - 左外连接
    - 右外连接
    - 全外连接

#### c. 扩展的关系代数运算

+ 广义投影。
+ 聚集。

### （2）元组关系演算

&emsp;&emsp; 元组关系演算和域关系演算是非过程化语言，代表了关系查询语言所需的基本能力。

### （3）域关系演算

### （4）术语回顾

![6_5](/assets/img/2022-12-06-Database-System-Concepts/6_5.png)

# 三. 数据库设计

## 1. 数据库设计和 E-R 模型

&emsp;&emsp;如何设计一个数据库模式？实体-联系数据模型提供了一个找出在数据库中表示的实体以及实体间如何关联的方法。

### （1）设计过程概览

+ 完整刻画未来数据库用户的数据需求，形成用户需求规格说明。
+ 选择数据模型，采用所选数据模型的概念将需求转换为数据库的概念模式。这一概念设计阶段用实体-联系模型设计，概念模式定义了数据库中表示的实体、实体的属性、实体之间的联系，以及实体和联系上的约束。概念设计阶段构建的 E-R 图提供了对模式的图形化描述。这个阶段关注的是描述数据及其联系，而不是定义物理存储细节。
+ 检查所设计的模式，确保满足功能需求规格说明（其中，用户描述将在数据上进行的各类操作/事务）。
+ 将抽象数据模型转换到数据库实现。
    - 逻辑设计阶段将高层概念模式映射到将使用的数据库系统的实现数据模型上，即将以实体-联系模型定义的概念模式映射到关系模式。
    - 物理设计阶段，在逻辑设计阶段得到的特定系统的数据库模式中指明数据库的物理特征。

### （2）实体-联系模型

&emsp;&emsp;实体-联系（entity-relationship，E-R）数据模型旨在方便数据库设计，它将现实世界企业含义和交互映射到概念模式上。E-R 模型采用了三个基本概念：实体集、联系集和属性。

#### a. 实体集
&emsp;&emsp;实体（entity）是现实世界中可区别于所有其他对象的一个“事物”或“对象”。实体集（entity set）是相同类型即具有相同性质（或属性）的一个实体集合。实体通过一组属性（attribute）来表示，属性是实体集中每个成员所拥有的描述性性质，每个实体在每个属性上都有各自的值。因此，数据库包括一组实体集，每个实体集包括任意数量的相同类型的实体。

#### b. 联系集
&emsp;&emsp;联系（relationshi）是指多个实体间的相互关联。联系集（relationship set）是相同类型联系的集合。例如，联系集 takes 表示实体集 student 和 section 之间的关联。  
&emsp;&emsp;实体集之间的关联称为参与，实体集 E1,E2,...,E3 参与（participate）联系集 R。E-R 模式中的一个联系实例（relationship instance）表示在所建模的现实世界中两个命名实体间的一个关联。  
&emsp;&emsp;实体在联系中扮演的功能称为实体的角色（role）。当参与联系集的实体集并非互异时，联系的含义就需要角色来解释。比如，course 实体的有序对建模的联系集 prereq，它描述一门课程是另一门课程的先修课。两个课程具有不同的角色。  
&emsp;&emsp;联系也可以具有描述性属性（descriptive attribute）。比如，实体集 student 和 section 参与的一个联系集 takes 的一个描述性属性 grade，记录学生在这门课中取得的成绩。  
&emsp;&emsp;给定的联系集中的一个联系实例必须是由其参与实体唯一标识的，而不必使用描述属性。  
&emsp;&emsp;相同的几个实体集可能会参与到多于一个联系集中。  
&emsp;&emsp;涉及两个实体集的联系集称为二元（binary）联系集。参与联系集的实体集的数目称为联系集的度（degree）。二元联系集的度为2。

#### c. 属性
&emsp;&emsp;每个属性都有一个可取值的集合，称为该属性的域。实体集的属性是将实体集映射到域的函数。用来描述实体的属性值构成存储在数据库中的数据的一个重要部分。  
&emsp;&emsp;E-R 模型中的属性类型划分：
+ 简单（simple）和复合（composite）属性。简单属性不能划分为更小的部分，复合属性可以再划分为更小的部分（即其他属性）。在设计模式中使用复合属性，可以在一些场景中引用完整的属性，而在另外的场景中仅引用属性的一部分。  
  ![7_4](/assets/img/2022-12-06-Database-System-Concepts/7_4.png)
+ 单值（single-valued）和多值（multivalued）属性。单值属性对一个特定实体都只有单独的一个值，而一个多值属性对某个特定实体可能对应于一组值。比如 instructor 实体集的 phone_number 属性，每个教师可以有零个、一个或多个电话号码。在适当的情况下，可以对一个多值属性的取值数目设置上、下界。
+ 派生（derived）属性。这类属性的值可以从别的相关属性或实体派生出来。比如属性 age 的值可以从当前的日期和属性 date_of_birth 计算出来。派生属性的值不存储，而是在需要时计算出来。
+ 空值。可能表示不存在值、值未知、值缺失、值不知道。

### （3）约束

&emsp;&emsp;E-R 可以定义一些数据库中的数据必须要满足的约束，包括映射基数以及参与约束。

#### a. 映射基数
&emsp;&emsp;映射基数（mapping cardinality），或基数比率，表示一个实体通过一个联系集能关联的实体的个数。  
&emsp;&emsp;对于实体集A和B之间的二元联系集R来说，映射基数有：
+ 一对一（one-to-one）。
+ 一对多（one-to-many）。
+ 多对一（many-to-one）。
+ 多对多（many-to-many）。

  ![7_6](/assets/img/2022-12-06-Database-System-Concepts/7_6.png)

&emsp;&emsp;显然，一个特定联系集的适当的映射基数依赖于该联系集所建模的现实世界的情况。  

#### b. 参与约束
&emsp;&emsp;如果实体集E中的每个实体都参与到联系集R的至少一个联系中，实体集E在联系集R中的参与称为全部（total）的。如果E中只有部分实体参与到R的联系中，实体集E到联系集R的参与称为部分（partial）的。如果一个学生必须拥有一个导师，而导师可以拥有零个或多个学生，则学生的参与是全部的，而导师的参与是部分的。

#### c. 码
&emsp;&emsp;实体的码是一个足以区分每个实体的属性集。码同样用于唯一地标识联系，从而将联系互相区分开来。

![7_3_3](/assets/img/2022-12-06-Database-System-Concepts/7_3_3.png)

### （4）从实体集中删除冗余属性
&emsp;&emsp;E-R 模型设计数据库，首先确定那些应当包含的实体集，然后挑选适当的属性，要包含哪些属性的选择决定于了解企业结构的设计者。一旦选择好实体和它们相应的属性，不同实体间的联系集就建立起来了。这些联系有可能会导致不同实体集中的属性冗余，并需要将其从原始实体集中删除。

![7_4_1](/assets/img/2022-12-06-Database-System-Concepts/7_4_1.png)
![7_4_2](/assets/img/2022-12-06-Database-System-Concepts/7_4_2.png)

### （5）实体-联系图

#### a. 基本结构
&emsp;&emsp;E-R 图可以图形化表示数据库的全局逻辑结构。E-R 图的基本结构包括以下几个主要部件：
+ **分成两部分的矩形**代表实体集，包括有阴影的第一部分包含实体集的名字，第二部分包含实体集中所有属性的名字。
+ **菱形**代表联系集。
+ **未分割的矩形**代表联系集的属性。构成主码的属性以下划线标明。
+ **线段**将实体集连接到联系集。
+ **虚线**将联系集属性连接到联系集。
+ **双线**显示实体在联系集中的参与度（全部或者部分）。
+ **双菱形**代表连接到弱实体集的标志性联系集。

#### b. 映射基数：

&emsp;&emsp;![7_9](/assets/img/2022-12-06-Database-System-Concepts/7_9.png)

&emsp;&emsp;E-R 图提供一个描述每个实体参与联系集中的联系的次数的更复杂的约束的方法。实体集和二元联系集之间的一条边可以有一个关联的最大和最小的映射基数，用 l..h 表示，其中 l 代表最小的映射基数，h 代表最大的映射基数。  
&emsp;&emsp;![7_10](/assets/img/2022-12-06-Database-System-Concepts/7_10.png)

#### c. 复合属性：  
&emsp;&emsp;![7_11](/assets/img/2022-12-06-Database-System-Concepts/7_11.png)

#### d. 角色
![7_12](/assets/img/2022-12-06-Database-System-Concepts/7_12.png)

#### e. 非二元的联系集
![7_13](/assets/img/2022-12-06-Database-System-Concepts/7_13.png)

#### f. 弱实体集
![7_5_6](/assets/img/2022-12-06-Database-System-Concepts/7_5_6.png)

#### g. 大学的 E-R 图
![7_5_7](/assets/img/2022-12-06-Database-System-Concepts/7_5_7.png)
![7_15](/assets/img/2022-12-06-Database-System-Concepts/7_15.png)

### （6）转换为关系模式

#### a. 一些基本的概念转换：
+ 实体-联系数据模型 》》》 关系数据模型
+ 实体 》》》元组
+ 实体集/联系集 》》》 建立相应外码约束的关系模式
+ 实体集的主码 》》》关系模式的主码
+ 复合属性 》》》为子属性创建单独的属性
+ 多值属性 》》》创建新的关系模式，并建立外码约束
+ 派生属性 》》》无
+ 弱实体集 》》》创建包含所依赖的强实体集的主码属性的关系模式，并建立外码约束。

#### b. 冗余
+ 连接弱实体集与其所依赖的强实体集的联系集的模式是冗余的，在基于 E-R 图的关系数据库设计中不必给出。
+ 实体集 A 到实体集 B 的一个多对一的联系集 AB ，且 A 在 AB 的参与是全部的，则可将 A 和 AB 模式合并成单个包含两个模式所有属性的并集的模式。
+ 一对一的联系，联系集的关系模式可以跟参与联系的任何一个实体集的模式进行合并。即使参与是部分的，也可以通过使用空值来进行模式的合并。

### （7）实体-联系设计问题

#### a. 用实体集还是用属性

#### b. 用实体集还是用联系集

#### c. 二元还是 n 元联系集

### （8）扩展的 E-R 特性

### （9）数据建模的其他表示法

#### a. E-R 图

#### b. UML

### （10）数据库设计的其他方面

## 2. 关系数据库设计
&emsp;&emsp;关系数据库设计的目标是生成一组关系模式，使我们存储信息时避免不必要的冗余，同时可以很方便地获取信息，通过设计满足适当范式（normal form）的模式可以实现这些目标，同时避免信息重复和不能表达某些信息这些数据库设计中易犯的错误。  
### （1）好的关系设计的特点
&emsp;&emsp;如果信息冗余、数据不一致以及表达信息不完整都不是问题的话，用一个更大的模式代替（合并）两个小的模式是一种设计选择。比如，用 instructor 和 department 的自然连接的结果 inst_dept(ID, name, salary, dept_name, buiding, budget) 代替 instructor 和 department。当然从大学的实际情况来看，这不是一个好的选择。  
&emsp;&emsp;如果信息冗余、数据不一致以及表达信息不完整是问题的话，用两个更小的模式代替（分解）一个大的模式是一种设计选择。考虑模式 inst_dept，存在上述问题，因此用 instructor 和 department 代替。  
&emsp;&emsp;考虑模式 inst_dept，dept_name 的每个特定的值对应至多一个budget，即如果存在模式 (dept_name, budget)，则 dept_name 可以作为主码，这种规则被定义为函数依赖（functional dependency）：dept_name -> budget。有了这种规则，就有足够的信息来发现 inst_dept 模式的问题了。由于 dept_name 不能是 inst_dept 的主码，因此 budget 可能会重复。  
&emsp;&emsp;函数依赖让数据库设计者可以发现一个模式应拆分或分解成两个或多个模式的情况。对于具有大量属性和多个依赖的模式，找到正确的分解要难得多，这需要依靠后面的规范方法。  
&emsp;&emsp;不是所有的模式分解都是有益的。无法表达某些重要事实的有问题的分解称为有损分解（lossy decomposition），反之则称为无损分解（lossless decomposition）。  

### （2）原子域和第一范式

&emsp;&emsp;E-R 模型允许实体集和联系集的属性具有某些程度的子结构，比如多值属性、组合属性。创建表时，这些子结构都有办法消除。  
&emsp;&emsp;在关系模型中，一个域是原子的，该域的元素被认为是不可分的单元，即属性不具有任何子结构。一个关系模式 R 属于第一范式（First Normal Form，1NF），R 的所有属性的域都是原子的。重要的问题不是域本身是什么，而是在数据库中如何使用域元素。比如，形如“CS-101“的课程标识号，其中”CS“表示计算机科学系，这意味着课程标识号的域不是原子的，然而只要数据库应用没有将标识号拆开并将标识号的一部分解析为系的缩写，它仍然将该域视为原子的。  
&emsp;&emsp;使用非原子值的类型可能很有用。在许多含有复杂结构的实体域中，强制使用第一范式会给应用程序员造成不必要的负担。大学模式可以被认为属于第一范式。

### （3）使用函数依赖进行分解

&emsp;&emsp;一个基于码和函数依赖的规范方法可以判断一个关系模式是否应该分解。

#### a. 码和函数依赖

&emsp;&emsp;一个数据库对现实世界中的一组实体和联系建模。在现实世界中，数据上面通常存在各种约束（规则）。一个关系的满足所有这种现实世界约束的实例，称为关系的合法实例（legal instance）。几种最常用的现实世界约束可以形式化地表示为码（超码、候选码以及主码），或者下面所定义的函数依赖。注意，下面的 r 表示关系，R 表示属性集，K 表示超码。

![8_3_1](/assets/img/2022-12-06-Database-System-Concepts/8_3_1.png)

&emsp;&emsp;我们将以两种方式使用函数依赖：
+ 判定关系的实例是否满足给定函数依赖集F。
+ 说明合法关系集上的约束。关系模式 R 满足函数依赖集 F ，则说 F 在 r(R) 上成立。

&emsp;&emsp;注意，函数依赖是单向的，A -> B 并不意味着 B -> A，这是两个不同的函数依赖。  
&emsp;&emsp;在所有关系中都满足的函数依赖称为平凡的（trivial）。一般地，如果 B 属于 A，则 A -> B 的函数依赖是平凡的。比如，AB -> B 以及 A -> A 都是平凡的。  
&emsp;&emsp;函数依赖是现实世界的约束，而非关系实例的数据偶然，一个关系实例可能满足某些函数依赖，但不需要在关系的模式上成立，因为它们并非现实世界的约束。  
&emsp;&emsp;给定模式 r(A, B, C)，如果函数依赖 A -> B 和 B -> C 在 r 上成立，可以推出 A -> C 在 r 上也成立。使用符号 F+（符号 + 在 F 的右上角）表示函数依赖集 F 的闭包（closure），即能够从给定 F 集合推导出的所有函数依赖的集合。显然，F+ 包含 F 中所有的函数依赖。

#### b. Boyce-Codd 范式

&emsp;&emsp;Boyce-Codd 范式（Boyce-Codd Normal Form, BCNF）消除所有基于函数依赖发现的冗余。

![8_3_2](/assets/img/2022-12-06-Database-System-Concepts/8_3_2.png)

![8_3_2_2](/assets/img/2022-12-06-Database-System-Concepts/8_3_2_2.png)

&emsp;&emsp;当我们分解不属于 BCNF 的模式时，产生的模式中可能有一个或多个不属于 BCNF，这种情况需要进一步分解，其最终结果是一个 BCNF 模式集合。

#### c. BCNF 和保持依赖

&emsp;&emsp;如果设计使得函数依赖的强制实施在计算上很困难，则称该设计不是保持依赖的（dependency preserving）。比如，属性在任何一个模式中都不完全出现的依赖，但是这种依赖可能由于存在逻辑上蕴含该依赖的其他依赖而被隐含地强制实施。    
&emsp;&emsp;由于常常需要保持依赖，因此我们考虑另外一种比 BCNF 弱的范式，它允许我们保持依赖。该范式称为第三范式。

#### d. 第三范式

![8_3_4](/assets/img/2022-12-06-Database-System-Concepts/8_3_4.png)

### （4）函数依赖理论

#### a. 函数依赖集的闭包

&emsp;&emsp;给定关系模式 r(R)，如果 r(R) 的每一个满足 F 的实例也满足 f，则 R 上的函数依赖 f 被 r 上的函数依赖集 F 逻辑蕴涵（logically imply）。F 的闭包是被 F 逻辑蕴涵的所有函数依赖的集合，记作 F+。

![8_4_1](/assets/img/2022-12-06-Database-System-Concepts/8_4_1.png)

#### b. 属性集的闭包

&emsp;&emsp;如果 A -> B，我们称属性 B 被 A 函数确定（functionally determine）。  

![8_4_2_1](/assets/img/2022-12-06-Database-System-Concepts/8_4_2_1.png)

![8_4_2_2](/assets/img/2022-12-06-Database-System-Concepts/8_4_2_2.png)

#### c. 正则覆盖

![8_4_3_1](/assets/img/2022-12-06-Database-System-Concepts/8_4_3_1.png)

![8_4_3_2](/assets/img/2022-12-06-Database-System-Concepts/8_4_3_2.png)

![8_4_3_3](/assets/img/2022-12-06-Database-System-Concepts/8_4_3_3.png)

![8_4_3_4](/assets/img/2022-12-06-Database-System-Concepts/8_4_3_4.png)

&emsp;&emsp;正则覆盖是与给定函数依赖集等价的最小的函数依赖集。  

#### d. 无损分解

&emsp;&emsp;令 r(R) 为一个关系模式，F 为 r(R) 上的函数依赖集。令 R1 和 R2 为 R 的分解。如果用 r1(R1) 和 r2(R2) 替代 r(R) 时没有信息损失，则称该分解是无损分解（lossless decomposition）。同样地，如果我们把 r 投影至 R1 和 R2 上，然后计算投影结果的自然连接，仍然得到一摸一样的 r。不是无损分解的分解称为有损分解（lossy decomposition）。

![8_4_4](/assets/img/2022-12-06-Database-System-Concepts/8_4_4.png)

#### e. 保持依赖

![8_4_5](/assets/img/2022-12-06-Database-System-Concepts/8_4_5.png)

&emsp;&emsp;F'+ = F+ 的意思就是说 F' 逻辑蕴涵的函数依赖集与 F 逻辑蕴涵的函数依赖集一样，那么只要检测 F' 满足，就相当于检测 F 满足，而 F‘ 是分解后的的单个关系模式上的函数依赖集合的并集，F 是分解前的大关系模式上的函数依赖集，可不就说明这次分解是保持依赖的分解嘛。  
&emsp;&emsp;如果分解是保持依赖的，则给定一个数据库更新，所有的函数依赖都可以由单独的关系进行验证，无需计算分解后的关系的连接。

### （5）分解算法

#### a. BCNF 分解

![8_5_1](/assets/img/2022-12-06-Database-System-Concepts/8_5_1.png)

&emsp;&emsp;有一些关系不存在保持依赖的 BCNF 分解。

#### b. 3NF 分解

![8_5_2](/assets/img/2022-12-06-Database-System-Concepts/8_5_2.png)

&emsp;&emsp;我们用正则覆盖将关系分解成 3NF，属于 3NF 的关系也许会含有冗余，但是总存在保持依赖的3NF分解。  

#### c. BCNF 和 3NF 的比较

&emsp;&emsp;我们对应用函数依赖进行数据库设计的目标是：
+ BCNF。
+ 无损。
+ 保持依赖。

&emsp;&emsp;由于不是总能达到所有这三个目标，因此我们也许不得不在 BCNF 和保持依赖的 3NF 中做出选择。3NF 总是可以在满足无损并保持依赖的前提下得到，但是可能会存在空值表示数据项间的某些有意义的联系以及信息重复的问题。  
&emsp;&emsp;SQL 指定函数依赖的途径只能是通过主码约束或者 unique 约束。目前没有数据库系统支持强制实施函数依赖所需的复杂断言，而且检查断言的开销很大。如果我们使用标准 SQL，我们只能对那些左半部是码的函数依赖进行高效的检查。物化视图上的主码约束可以用来对不能保持依赖的分解做连接再进行检查，然而大多数系统不支持物化视图上的约束。  
&emsp;&emsp;因此，在不能得到保持依赖的 BCNF 分解的情况下，通常我们倾向于选择 BCNF，因为在SQL中检查非主码约束的函数依赖很困难。

### （6）使用多值依赖的分解

&emsp;&emsp;利用多值依赖定义关系模式的一种范式被称为第四范式（Fourth Normal Form，4NF），它比 BCNF 的约束更严格。每个 4NF 模式也都属于 BCNF。  

#### a. 多值依赖

![8_6_1](/assets/img/2022-12-06-Database-System-Concepts/8_6_1.png)

&emsp;&emsp;多值依赖指明了仅用函数依赖无法指明的约束。  

#### b. 第四范式和 4NF 分解

![8_6_2](/assets/img/2022-12-06-Database-System-Concepts/8_6_2.png)

![8_6_3](/assets/img/2022-12-06-Database-System-Concepts/8_6_3.png)

### （7）更多的范式

![8_7](/assets/img/2022-12-06-Database-System-Concepts/8_7.png)

### （8）数据库设计过程

&emsp;&emsp;规范化是如何糅合在整体数据库设计过程中的，给定关系模式 r(R)，并对之进行规范化，我们可以采用以下几种方法得到模式 r(R):
+ r(R) 可以是由 E-R 图向关系模式集进行转换时产生的。
+ r(R) 可以是一个包含所有有意义的属性的单个关系。然后规范化过程中将 R 分解成一些更小的模式。
+ r(R) 可以是关系的即席（即时）设计的结果，然后我们检验它们是否满足一个期望的范式。

&emsp;&emsp;
&emsp;&emsp;
&emsp;&emsp;

# 四. 数据存储和查询

## 1. 存储和文件结构

### （1）物理存储介质

&emsp;&emsp;计算机系统中存在多种数据存储类型，可以根据访问数据的速度、每单位数据的购买成本以及介质的可靠性进行分类。层次越高，成本和速度越高，层次越低，成本和速度越低。

![10_1](/assets/img/2022-12-06-Database-System-Concepts/10_1.png)

&emsp;&emsp;以上层次结构中的存储设备从上到下可两两划分为基本存储（primary storage）、辅助存储（secondary storage）或联机存储（online storage）、三级存储（tertiary storage）或脱机存储（offline storage）。也可划分为易失性存储（volatile storage）和非易失性存储（nonvolatile storage）。

### （2）磁盘和闪存

&emsp;&emsp;每条磁盘约有 1～5 个盘片（platter）。每个盘片的两个表面都覆盖有磁性物质用以记录信息，盘片的表面从逻辑上划分为磁道（track），磁道又划分为扇区（sector）。扇区是从磁盘读出和写入信息的最小单位，扇区大小一般为 512 字节，一个盘片约有 50000～100000 条磁道，外侧磁道比内侧磁道拥有更多的扇区。高容量模式的磁盘拥有更多的磁道和扇区。

![10_2](/assets/img/2022-12-06-Database-System-Concepts/10_2.png)

&emsp;&emsp;在磁盘使用时，驱动马达会以很高的恒定速度旋转。每个盘片的每一面的上方都有一个读写头，读写头通过在盘片上移动来访问不同的磁道，所有磁道的读写头安装在磁盘臂上，并且一起移动，因此同一时刻所有读写头位于的磁道构成了一个柱面（cylinder）。读写头通过反转磁性物质磁化的方向将信息磁化存储到扇区中。

&emsp;&emsp;磁盘控制器（disk controller）是计算机系统和磁盘驱动器硬件之间的接口，在磁盘驱动单元内部实现。它接受高层次的读写扇区的命令然后开始执行，同时为所写的每个扇区附加校验和，并在读取时进行校验和的检测。磁盘通过一个高速互联通道连接到计算机系统：（1）SATA，串行ATA（2）小型计算机系统互联（Small-Computer-System Interconnect，SCSI）（3）SAS（4）光纤通道接口（Fibre Channel Interface）。外部磁盘系统经常使用 USB 接口或 IEEE 1394 火线接口。在存储区域网（Storage Area Network，SAN）体系结构中，大量的磁盘通过高速网络与许多计算机服务器相连，通常磁盘采用独立磁盘冗余阵列（Redundant Array of Independent Disk，RAID）技术进行本地化组织，从而给服务器一个很大且非常可靠的磁盘的逻辑视图。

&emsp;&emsp;磁盘性能的度量指标是容量、访问时间、数据传输率和可靠性。访问时间是从发出读写请求到数据开始传输之间的时间，包括磁盘臂的寻道时间和磁盘的旋转等待时间。平均故障时间（Mean Time To Failure，MTTF）是磁盘可靠性的度量标准，表示系统无故障连续运行的时间量。

&emsp;&emsp;磁盘I/O请求由文件系统和操作系统具有的虚拟内存管理器产生，每个请求指定了要访问的磁盘地址，这个地址以块号的形式提供。一个块（block）是一个逻辑单元，它包含固定数目的连续扇区。块大小在512字节到几KB之间。数据在磁盘和主存储器之间以块为单位传输。术语页（page）常用来指块。为了提高访问块的速度，产生了许多技术：
+ 缓冲（buffering）。从磁盘读取的块暂时存储在内存缓冲区中，以满足将来的需要。缓冲通过操作系统和数据库共同运作。
+ 预读（read-ahead）。当一个磁盘块被访问时，相同磁道的连续块也被读入内存缓冲区。
+ 调度（scheduling）。通常使用的磁盘臂调度算法是电梯算法。磁盘控制器通常对读请求进行重新排序以提高性能，因为它最清楚磁盘块的组织、磁盘盘片的旋转位置和磁盘臂的位置。
+ 文件组织（file organization)。可以按照与预期的数据访问方式最接近的方式来组织磁盘上的块。操作系统一次为一个文件分配多个连续的块（一个区 extent），文件的顺序访问只需每个区寻道一次，而不是每个块寻道一次。
+ 非易失性写缓冲区。关于数据库更新的信息需要记录到磁盘上，才能在系统崩溃时得以保存，因而更新操作密集的数据库应用的性能高度依赖于磁盘的写操作的速度。可以使用非易失性随机访问存储器（NV-RAM），当数据库系统（或操作系统）请求往磁盘上写一个块时，磁盘控制器将这个块写入 NV-RAM 缓冲区中，然后立即通知操作系统写操作完成。NV-RAM 更多应用于 “RAID控制器”。
+ 日志磁盘。一种专门用于写顺序日志的磁盘，对日志磁盘的所有访问都是顺序的，数据库系统不需要等待写操作完成，日志磁盘会在以后在磁盘上的实际位置完成写操作。支持日志磁盘的文件系统称为日志文件系统。大多数现代文件系统都实现了日志，并在写入内部文件系统的信息时使用日志磁盘。但是由应用程序执行的写操作一般不写入日志磁盘，数据库系统实现它们自己形式的日志（后面研究）。

&emsp;&emsp;NOR 快闪允许随机访问闪存中的单个字，NAND 快闪则读取整个数据页。NAND 快闪构建的存储系统提供与磁盘存储器相同的面向块的接口。闪存的页面不能直接覆盖，必须先擦除然后再重写，对一个闪存页的擦除次数也存在限制。闪存系统通过映射逻辑页码到物理页码，限制了慢擦除速度和更新限制的影响。当一个逻辑页更新时，它可以重新映射到任何已擦除的物理页，它原来的位置可以随后擦除。闪存控制器也透明执行在物理块中均匀分布擦除操作的损耗均衡原则。这些操作都在闪存转换层的软件层完成，在这一层之上，闪存存储器看起来和磁盘存储器一样，都提供面向页/扇区的接口。因此，文件系统和数据库存储结构可以看到相同的底层存储结构逻辑视图，无论是闪存存储器或磁盘存储器。

### （3）RAID

&emsp;&emsp;为了提高性能和可靠性，人们提出了通称为独立磁盘冗余阵列（Redundant Array of Independent Disk，RAID）的多种磁盘组织技术。使用 RAID 是因为它有更高的可靠性和更高的执行效率，而不再是经济原因，况且它易于管理和操作。

&emsp;&emsp;通过冗余（redundancy）可以提高可靠性。实现冗余的最简单（但最昂贵）的方法是复制每一张磁盘，即镜像（mirroring）技术。通过并行可以提高性能，使用磁盘镜像时，处理读请求的速度将翻倍。每个读操作的传输速率和单一磁盘系统中的传输速率是一样的，但是每单位时间内读操作的数目将翻倍。通过在多张磁盘上进行数据拆分（striping data）可以提高传输速率。比特级拆分将每个字节的每一位写到不同的多个磁盘上，这样，每张磁盘都参与每次的访问（读或写），因此它每秒处理的访问操作的总数和一张磁盘所处理的是一样的，但是相同时间内，它每次访问所读取的数据量是一张磁盘上的多倍。块级拆分将块拆分到多张磁盘，读取大文件时可以同时从 n 张磁盘上读取 n 个块，从而获得对于大的读操作的高数据传输速率。当读取单块数据时，其传输速率与单个磁盘上的数据传输速率相同。总之，磁盘系统中的并行有两个主要的目的：
+ 1.负载平衡多个小的访问操作（块访问），以提高访问操作的吞吐量。
+ 2.并行执行大的访问操作，以减少大访问操作的响应时间。

&emsp;&emsp;镜像提供了高可靠性，但它十分昂贵，拆分提供了高数据传输率，但不能提高可靠性。通过结合奇偶校验位和磁盘拆分思想，从而以较低的代价提供数据冗余，人们提出了若干 RAID 级别，它们具有不同的成本和性能之间的权衡。

+ RAID 0。使用块级拆分无冗余的磁盘阵列。
+ RAID 1。使用块级拆分和磁盘镜像的磁盘阵列。
+ RAID 2。使用字节拆分和内存风格纠错码的磁盘阵列，内存风格纠错码，比如奇偶校验位，用于实现错位检测和纠正。纠错码机制存储两个或更多的附加位，并且如果有一位被破坏，它可以重建数据。如果一张磁盘发生了故障，可以从其他磁盘中读出字节的其余位和相关的纠错位，并用它们来重建被破坏的数据。RAID 2 使用的磁盘开销低于 RAID 1 的磁盘镜像。
+ RAID 3。在 RAID 2 上进行了改进。与内存系统不同，磁盘控制器能够检测一个扇区是否正确，从而可以确定哪个扇区坏了。因此，可以通过一个单一的奇偶校验位，结合其他磁盘上对应扇区的对应位，便可重建破坏的数据。这只需要一个冗余磁盘来存储数据磁盘的对应扇区的对应位组成的奇偶校验位。RAID 3 相比 RAID 1 读取一个块的传输率更高，每秒钟支持的 I/O 操作数更少。
+ RAID 4。使用块级拆分，在一张独立磁盘上为其他 N 张磁盘上对应的块保留一个奇偶校验块。如果一张磁盘发生故障，可以使用奇偶校验块和其他磁盘上对应的块来恢复发生故障的磁盘上的块。RAID 4 对大数据的读取/写入有很高的传输率，但是小的独立的写操作不能并行执行，因为存储奇偶校验位的块需要更新（大的写操作，奇偶校验块可以和写入块并行更新），这需要读出奇偶校验块和数据块的旧值，计算新的奇偶校验块，然后和新数据一起写入。
+ RAID 5。RAID 5 将 RAID 4 中的数据和奇偶校验位都分布到所有的 N+1 张磁盘中，而不是在 N 张磁盘上存储数据并在一张磁盘上存储奇偶校验位。RAID 5 中所有的磁盘都能参与对读请求的服务，RAID 4 中的奇偶校验磁盘不参与读操作。注意奇偶校验位不能和其所对应的数据块存储在同一张磁盘上，这样的话数据将是不可恢复的。

    ![10_3_3](/assets/img/2022-12-06-Database-System-Concepts/10_3_3.png)

+ RAID 6。P+Q 冗余方案。相比 RAID 5 ，RAID 6 存储了额外的冗余信息，以应对多张磁盘发生故障的情况，使用 Reed-Solomon 码之类的纠错码位，它为每 4 位数据存储 2 位的冗余信息，这样系统可以容忍两张磁盘发生故障。

&emsp;&emsp;图中，P表示纠错位，C表示数据的第二个拷贝，该图描绘了4张磁盘的数据，其余磁盘用于存储故障恢复时所使用的冗余信息。

![10_3](/assets/img/2022-12-06-Database-System-Concepts/10_3.png)

&emsp;&emsp;RAID 级别的选择考虑的因素：
+ 所需的额外磁盘存储带来的花费。
+ 在 I/O 操作数量方面的性能需求。
+ 磁盘故障时的性能。
+ 数据重建过程中的性能。

&emsp;&emsp;RAID 1 的数据重建最简单，只需要拷贝即可，对于其他级别，需要访问磁盘阵列中的所有其他磁盘来重建故障磁盘上的数据。RAID 0 用于数据安全性不是很重要的高性能应用。RAID 2 和 RAID 4 分别被 RAID 3 和 RAID 5 所包含。RAID 3 的比特级拆分不如 RAID 5 的块级拆分，块级拆分对于大量数据的传输的效率和 RAID 3 同样好，同时对于小量数据传输使用更少的磁盘。对于小量数据传输，磁盘访问时间占主要地位，所以 RAID 3 的比特拆分对于小量数据的并行读取并没有多少好处，甚至由于磁盘上的相关扇区都读出后传输才算完成，因此采用 RAID 3 的磁盘阵列的每次操作都需要等所有磁盘操作完成，从而使其平均延迟变得非常接近单张磁盘在最坏情况下的延迟，从而抵消了其较高传输速率的长处。RAID 6 比 RAID 5 的可靠性更高，适用于数据安全十分重要的应用。RAID 1 的写操作性能更高，因此在数据库系统日志文件的存储这样的应用中使用广泛。RAID 5 具有较低的存储负载，但写操作需要更高的时间开销，对于经常读操作而很少进行写操作的应用，RAID 5 是首选。RAID 5 增加了写单个逻辑块所需的 I/O 操作数，因而 RAID 1 成为许多具有中等存储需求和高 I/O 需求的应用的选择。

&emsp;&emsp;RAID 可以在不修改硬件层，只修改软件的基础上实现，即软件 RAID（software RAID）。而具有专用硬件支持的系统称为硬件 RAID（hardware RAID）系统具有更多很大的好处。包括：支持非易失性RAM记录写操作、热交换（在不切断电源的情况下将出错磁盘用新的磁盘替换）、备用电源等。

### （4）第三级存储

&emsp;&emsp;在大型数据库系统中，一些数据可能要放置在第三级存储中。两种最常见的第三级存储介质是光盘和磁带。

### （5）文件组织

&emsp;&emsp;一个数据库被映射到多个不同的文件，这些文件由底层的操作系统来维护，永久地存在于磁盘上。一个文件在逻辑上组织成记录的一个序列，文件由操作系统作为一种基本结构支持，数据库系统需要考虑的是用文件表示逻辑数据模型的不同方式。每个文件被分成定长的存储单元 —— 块，块是存储分配和数据传输的基本单元。大多数数据库默认使用 4～8KB 的块大小，许多数据库允许指定块大小。一个块可能包括很多条记录，所包含的确切的记录集合是由使用的物理数据组织形式所决定的。

&emsp;&emsp;假定没有记录比块大，同时没有记录是仅有部分包含于一个块中的。把数据库映射到文件的一种方法是使用多个文件，在任意一个文件中只存储一个固定长度的记录。另一种是构造自己的文件，使之能够容纳多种长度的记录。  

&emsp;&emsp;对于定长记录。一种简单的方式是使用能容纳记录的最大长度作为记录的固定存储长度在文件中依次存储记录，同时采用空闲链表处理文件中的空闲碎片。在文件的开始处，分配一定数量的字节作为文件头（file header），包含有关文件的各种信息，比如空闲列表的链表头地址。在一个块中只分配它能完整容纳下的最大的记录数。如图是包含 instructor 记录的文件：

![10_7](/assets/img/2022-12-06-Database-System-Concepts/10_7.png)

&emsp;&emsp;对于变长记录。一条有变长度属性的记录表示通常具有两个部分：初始部分是定长属性，接下来是变长属性。对于定长属性，分配存储值所需的字节数，对于变长属性，在记录的初始部分中表示为一个（偏移量，长度）对值。在记录的初始定长部分之后，属性的值连续存储，记录初始部分存储有关每个属性的固定长度的信息。  

&emsp;&emsp;一个 instructor 记录，前三个属性 ID、name、dept_name 是变长字符串，第4个属性 salary 是一个大小固定的数值。假设偏移量和长度存储在两个字节中，即每个属性占 4 个字节。

![10_8](/assets/img/2022-12-06-Database-System-Concepts/10_8.png)

&emsp;&emsp;这个图也说明了空位图（null bitma）的使用，它用来表示记录的哪个属性是空值。在一些表示中，空位图存储在记录的开头，并且对于空属性不存储数据（值或偏移量/长度）。

&emsp;&emsp;知道了变长记录的表示，接下来处理在块中存储变长记录的问题。分槽的页结构（slotted-page structure）一般用于在块中组织记录。如图所示，每个块的开始处有一个块头，其中包含以下信息：
+ 块头中记录条目的个数。
+ 块中空闲空间的末尾处。
+ 一个由包含记录位置和大小的记录条目组成的数组。

![10_9](/assets/img/2022-12-06-Database-System-Concepts/10_9.png)

&emsp;&emsp;实际记录从块的尾部开始连续排列。插入一条记录，在空闲空间的尾部给这条记录分配空间，在空闲空间的前面也就是块头的数组尾部添加插入记录的大小和位置的条目。删除一条记录，它的条目被设置成删除状态，并且记录占用空间被释放，块中在被删除记录之前的记录将被移动，使得所有空闲空间仍位于块头数组的最后一个条目和第一条记录之间。移动记录的代价并不高，因为块的大小是有限制的。

&emsp;&emsp;大多数关系数据库限制记录不大于一个块的大小以简化缓冲区管理和空闲空间管理。大对象常常存储到一个特殊文件（或文件的集合）中而不是与记录的其他（短）属性存储在一起。然后一个指向该对象的逻辑指针存储到包含该大对象的记录中。

### （6）文件中记录的组织

&emsp;&emsp;前面讲述了如何在一个文件结构中表示记录。关系是记录的集合，给定一个记录的集合，如何在文件中组织它们？下面是在文件中组织记录的几种可能的方法：
+ 堆文件组织（heap file organization）。一条记录可以放在文件中任何有空间存放的地方。记录是无序的。通常每个关系使用一个单独的文件。
+ 顺序文件组织（sequential file organization）。记录根据其“搜索码”的值顺序存储。搜索码是任何一个属性或者属性的集合，不必是主码或超码。为了快速按搜索码的顺序获取记录，通过指针把记录链接起来，每条记录的指针指向按顺序排列的下一条记录。可以使用空闲链表管理删除，对于插入操作可先在待插入记录之前的那条记录的块中寻找空闲空间或者插入到一个溢出块中，同时调整指针，使其按搜索码的顺序把记录链接在一起。

    ![10_11](/assets/img/2022-12-06-Database-System-Concepts/10_11.png)

+ 散列文件组织（hashing file organization）。在每条记录的某些属性上计算一个散列函数，散列函数的结果确定记录应放到文件的哪个块中。与索引结构密切相关。

&emsp;&emsp;通常，每个关系的记录用一个单独的文件存储。但在 多表聚簇文件组织 中，几个不同关系的记录存储在同一个文件中，甚至是同一个块中，于是一个 I/O 操作可以从所有关系中取到相关的记录。

&emsp;&emsp;仔细设计记录在块中的分配和块自身的组织方式可以获得性能上的好处。很多大型数据库系统在文件管理方面并不直接依赖于下层的操作系统，而是让操作系统分配给数据库系统一个大的操作系统文件。数据库系统把所有关系存储在这个文件中，并且自己管理这个文件。  

&emsp;&emsp;多表聚簇文件组织是一种在每一块中存储两个或者更多个关系的相关记录的文件结构。允许我们一次块的读操作来读取满足连接条件的记录，以便更高效地处理这种特殊的查询。将多个表聚集到一个文件中的使用加速了对特定连接的处理，但是它导致其他类型查询的处理变慢，比如对单个表的查询，与在单一文件中存储单一关系的策略相比，需要访问更多的块。因此，可以使用指针将单个关系的所有记录链接起来。如下图所示是 department（一部分） 和 instructor （一部分）的多表聚簇文件结构：

![10_15](/assets/img/2022-12-06-Database-System-Concepts/10_15.png)

&emsp;&emsp;何时使用多表聚簇依赖于数据库设计者所认为的最频繁的查询类型。多表聚簇的谨慎使用可以在查询处理中产生明显的性能提高。

### （7）数据字典存储

&emsp;&emsp;关于关系的关系模式和其他元数据存储在称为数据字典（data dictionary）或系统目录（system catalog）的结构中。系统必须存储的信息类型有：
+ 关系的名字。
+ 每个关系中属性的名字、域和长度。
+ 定义的视图的名字和这些视图的定义。
+ 完整性约束（例如，码约束）。

&emsp;&emsp;很多系统为系统的用户保存了下列数据：
+ 授权用户的名字。
+ 关于用户的授权和账户信息。
+ 用于认证用户的密码和其他信息。

&emsp;&emsp;数据库中还会存储关于关系的统计数据和描述数据。例如：
+ 每个关系中元组的总数。
+ 每个关系所使用的存储方法（例如，聚簇或非聚簇）。

&emsp;&emsp;数据字典也会记录关系的存储组织（顺序、散列或堆），和每个关系的存储位置：
+ 包含每个关系的文件名。
+ 如果数据库把所有的关系存储在一个文件中，数据字典可能将包含每个关系中记录的块记在如链表这样的数据结构中。

&emsp;&emsp;存储关于每个关系的每个索引的信息：
+ 索引的名字。
+ 被索引的关系的名字。
+ 在其上定义索引的属性。
+ 构造的索引类型。

&emsp;&emsp;实际上，所有这些元数据信息构成了一个微型数据库。一些数据库系统使用专用的数据结构和代码来存储这些信息。通常人们更倾向于在数据库中存储关于数据库本身的数据。使用数据库存储系统数据，简化了系统的总体结构，并且允许使用数据库的全部能力来对系统数据进行快速访问。

&emsp;&emsp;关于如何使用关系来表示系统元数据的确切选择必须由系统设计者来决定。下图是一种可选的表示，数据字典通常存储成非规范化的形式，以便进行快速的存取。

![10_16](/assets/img/2022-12-06-Database-System-Concepts/10_16.png)

&emsp;&emsp;当数据库系统从关系中查找记录时，它必须首先通过 Relation_metadata 关系来查找关系的位置和存储组织。但是必须先知道 Relation_metadata 关系自身的存储组织和位置，这一信息记录在数据库自身的代码段或者数据库中的一个固定位置中。

### （8）数据库缓冲区

&emsp;&emsp;数据库系统的一个主要目标是尽量减少磁盘和存储器之间传输的块数目。缓冲区是主存储器中用于存储磁盘块的拷贝的那一部分，负责缓冲区空间分配的子系统称为缓冲区管理器（buffer manager）。

&emsp;&emsp;缓冲区管理器几乎和大多数操作系统中的虚拟内存管理器是一样的，只是数据库的大小会比机器的硬件地址空间大的多，因此存储器地址不足以对所有磁盘块进行寻址。此外，缓冲区管理器必须使用比典型的虚拟存储器管理策略更加复杂的技术：
+ 缓冲区替换策略（buffer replacement strategy）。立即丢弃（loss-immediate）策略、最近最少使用（Least Recently Used，LRU）策略、最近最常使用（Most Recently Used，MRU）策略。
+ 被钉住的块（pinned block）。不允许写回磁盘的块被称为被钉住的（pinned）块，例如当一个块上的更新操作正在进行时，大多数恢复系统不允许将该块写回磁盘，这也会影响到块替换策略。
+ 块的强制写出（forced output of block）。尽管不需要一个块所占用的缓冲区空间，但必须把这个块写回磁盘，这样的写操作称为块的强制写出（forced output）。

&emsp;&emsp;数据库系统能够比操作系统更准确地预测未来的访问模式，通过查看执行用户请求的每一步操作，与依赖过去而预测将来的操作系统不同，数据库系统至少可以得到短期内的将来信息。但是理想的数据库块替换策略需要数据库操作的知识（包括正在执行的和将来会执行的操作），没有哪个策略能够很好地处理所有可能的情况。绝大多数数据库系统都使用 LRU 策略。缓冲区管理器应尽量不把数据字典从主存储器中移除，一般不应把索引块从主存储器中移除。

&emsp;&emsp;除了块被再次访问的时间以外，并发控制子系统和崩溃-恢复子系统都是影响块替换策略的因素。

## 2. 索引与散列

### （1）基本概念

&emsp;&emsp;许多查询只涉及文件中的少量记录，系统读取整个关系中的每一个元组进行比对的操作方式是低效的。理想情况下，系统应能够直接定位记录，通过设计与文件相关联的附加的结构可以实现这种访问方式。

&emsp;&emsp;数据库系统中的索引与书本中的索引所起的作用一样，为了检索一条记录，数据库系统首先会查找索引，找到相应记录所在的磁盘块，然后取出该磁盘块，得到所需的记录。可以使用几种复杂的技术代替效果不好的排序索引：
+ 顺序索引。基于值的顺序排序。
+ 散列索引。基于将值平均分布到若干散列桶中，通过散列函数（hash function）绝对一个值所属的散列桶。

&emsp;&emsp;用于顺序索引和散列的技术有好几种，没有最好的技术，只有对特定的数据库应用最适合的技术。对每种技术的评价基于下面这些因素：
+ 访问类型（access type）：能有效支持的访问类型。比如精准查找、范围查找等。
+ 访问时间（access time）：在查询中找到一个特定数据项或数据项集所需的时间。
+ 插入时间：插入新数据项到正确位置以及更新索引结构所需的时间。
+ 删除时间：找到待删除项所需的时间以及更新索引结构所需的时间。
+ 空间开销：索引结构所占用的额外存储空间。

&emsp;&emsp;通常需要在一个文件上建立多个索引。例如，可能按作者、主题或者书名来查找一本书。用于在文件中查找记录的属性或属性集称为搜索码（search key）。一个文件上有多个索引，就有多个搜索码。

### （2）顺序索引

&emsp;&emsp;索引结构是为了快速随机访问文件中的记录。每个索引结构与一个特定的搜索码相关联，顺序索引按顺序存储搜索码的值，并将每个搜索码与包含该搜索码的记录关联起来。

&emsp;&emsp;一个文件可以有多个索引，分别基于不同的搜索码。被索引文件中的记录自身也可以按照某种排序顺序存储，包含记录的文件按照某个搜索码指定的顺序排序，则该搜索码对应的索引称为聚集索引（clustering index）。聚集索引可以称为主索引（primary index），它可以建立在任何搜索码上，只是常常建立在主码上。搜索码指定的顺序与文件中记录的物理顺序不同的索引称为非聚集索引（nonclustering index）或辅助索引（secondary index）。在搜索码上有聚集索引的文件称作索引顺序文件（index-sequential file），是数据库系统中最早采用的索引模式之一，针对继需顺序处理整个文件又需随机访问单独记录的应用而设计。

![11_1](/assets/img/2022-12-06-Database-System-Concepts/11_1.png)

&emsp;&emsp;索引项（index entry）或索引记录（index record）由一个搜索码值和指向具有该搜索码值的一条或者多条记录的指针构成。指向记录的指针包括磁盘块的标识和磁盘块内记录的块内偏移。

&emsp;&emsp;可以使用的顺序索引有两类：
+ 稠密索引（dense index）：文件中的每个搜索码值都有一个索引项。在稠密聚集索引中，索引项包括搜索码值以及指向具有该搜索码值的第一条数据记录的指针，具有相同搜索码值的其余记录顺序地存储在第一条数据记录之后，由于该索引是聚集索引，记录根据相同的搜索码值排序。在稠密非聚集索引中，索引必须存储指向所有具有相同搜索码值的记录的指针列表。
+ 稀疏索引（sparse index）：只为搜索码的某些值建立索引项。因此，只有当关系按搜索码排列顺序存储时才能使用稀疏索引，即只有索引是聚集索引时才能使用稀疏索引。为了定位一条记录，我们找到搜索码值小于或等于所查找记录的搜索码值的最大索引项，然后从该索引项指向的记录开始，沿着文件中的指针查找，直到找到所需的记录为止。

&emsp;&emsp;以 ID 为搜索码的 instructor 文件的稠密索引和稀疏索引：

![11_2](/assets/img/2022-12-06-Database-System-Concepts/11_2.png)

&emsp;&emsp;以 dept_name 为搜索码的 instructor 文件的稠密聚集索引，注意 instructor 文件按照搜索码 dept_name 排序，因为它是聚集索引，而不是非聚集索引。

![11_4](/assets/img/2022-12-06-Database-System-Concepts/11_4.png)

&emsp;&emsp;稠密索引比稀疏索引更快定位一条记录，但稀疏索引占用空间小，且插入和删除时所需的维护开销也较小。系统设计者必须在存取时间和空间开销之间进行权衡，这一权衡的决定依赖于具体的应用。但在这里，为每个块建立一个索引项的稀疏索引是一个较好的折中。原因在于，处理数据库查询的开销主要由把块从磁盘读到主存中的时间决定，使用这样的稀疏索引可以定位包含所要查找记录的块，只要记录不在溢出块中，就能使块访问次数最小，同时保持索引尽可能小。

&emsp;&emsp;像对待顺序文件一样，在原始的内层索引上构造一个稀疏的外层索引，可以根据需要多次重复此过程。由于索引项总是有序的，外层索引可以是稀疏的。具有两级或两级以上的索引称为多级（multilevel）索引。利用多级索引搜索记录与用二分法搜索记录相比需要的 I/O 操作要少得多。

![11_5](/assets/img/2022-12-06-Database-System-Concepts/11_5.png)

&emsp;&emsp;文件中有记录插入或删除时，文件中记录的任何搜索码属性更新时，索引都需要更新。对记录的更新可以设计为对旧记录的删除和新记录的插入。单级索引的更新算法如下，多级索引的插入和删除算法可以采用同样的技术更新更高层的索引。
+ 插入。系统首先根据待插入记录中的搜索码值进行查找。
    - 稠密索引。首先根据搜索码值是否在索引中，选择是否在索引中合适位置插入具有该搜索码值的索引项。如果索引项存储的是指向指针列表的指针（非聚集索引），系统就在指针列表中增加一个指向新记录的指针，否则索引项存储一个仅指向具有相同搜索码值的第一条记录的指针（聚集索引），系统把待插入的记录放到具有相同搜索码值的其他记录之后。
    - 稀疏索引。假设稀疏索引为每个块保存一个索引项。插入到新块则增加新块的索引项到索引中即可，插入到旧块，若待插入记录含有块中的最小搜索码值则更新指向该块的索引项，否则系统对索引不做任何改动。
+ 删除。系统首先查找要删除的记录。
    - 稠密索引。如果删除的记录是这个特定搜索码值的唯一的一条记录，则从索引中删除相应索引项。否则依据索引项是否存储的是指针列表（即是否是聚集索引），系统选择从索引项中的指针列表里删除指向被删除记录的指针（非聚集索引），或者若删除的记录是具有该搜索码值的第一条记录，则系统更新索引项，使其指向下一条记录（聚集索引）。
    - 稀疏索引。如果索引不包含具有被删除记录搜索码值的索引项，则索引不做任何修改。如果被删除的记录是具有该搜索码值的唯一记录，则系统用下一个搜索码值（按搜索码顺序）的索引记录替换相应的索引记录，除非下一个搜索码已经有一个索引项，则删除而不是替换该索引项，否则该搜索码值的索引如果指向被删除的记录，系统就更新索引项，使其指向具有相同搜索码值的下一条记录。

&emsp;&emsp;辅助索引（非聚集索引）必须是稠密索引，对每个搜索码值都有一个索引项，而且对文件中的每条记录都有一个指针。而聚集索引可以是稀疏索引，可以只存储部分搜索码值，通过顺序扫面文件总可以找到对应的记录。辅助索引的结构和聚集索引不同，特别是在搜索码不是一个候选码时，因为记录按聚集索引而不是辅助索引的搜索码顺序存放，辅助索引必须包含指向每一条记录的指针。我们可以用一个附加的间接指针层来实现非候选码的搜索码上的辅助索引，辅助索引中的指针不直接指向文件，而是指向一个包含文件指针的桶。

![]()

&emsp;&emsp;按聚集索引顺序对文件进行顺序扫描是非常高效的，而按辅助索引的搜索码顺序对文件进行顺序扫描是很慢的，因为每读一条记录就可能需要从磁盘读入一个新的块。辅助索引的删除和插入所采用的操作和前面稠密索引所采用的操作是一样的。辅助索引能够提高使用聚集索引搜索码以外的码的查询性能，但也显著增加了数据库的更新开销。数据库设计者根据对查询和更新相对频率的估计来决定哪些辅助索引是需要的。

&emsp;&emsp;索引的自动生成：大多数数据库实现会在主码上自动创建一个索引，该索引可以用来检查主码约束（即没有重复的主码值），而不用读取整个关系。

&emsp;&emsp;一个搜索码可以有多个属性，一个包含多个属性的搜索码称为复合搜索码（composite search key）。搜索码是属性列表，按字典序排序，其余的结构和其他搜索码一样。一个在复合码上的有序索引可以用来有效地查找某些其他类型的查询。

### （3）B+ 树索引文件

&emsp;&emsp;

&emsp;&emsp;

&emsp;&emsp;

&emsp;&emsp;

