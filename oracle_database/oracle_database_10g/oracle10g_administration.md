### Oracle10g Administration {#oracle10g-administration}

#### PL/SQL {#pl-sql}

Intersect 和Minus的操作和Union基本一致，这里一起总结一下：

Union，对两个结果集进行并集操作，不包括重复行，同时进行默认规则的排序；

Union All，对两个结果集进行并集操作，包括重复行，不进行排序；

Intersect，对两个结果集进行交集操作，不包括重复行，同时进行默认规则的排序；

Minus，对两个结果集进行差操作，不包括重复行，同时进行默认规则的排序。

可以在最后一个结果集中指定Order by子句改变排序方式。

SELECT

**SELECT** **功能：** **投影：不加选择条件** **选择：加选择条件** **联接：** **~**

1.SELECT 语法 SELECT 语法： SELECT~~~~~~~~~~~~~~~~~~~~~~~~#select 子句 *~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~# 选择所有行 DISTINCT~~~~~~~~~~~~~~~~~~~~~~# 禁止重复 column|expression~~~~~~~~~ 选择指定列，或者列的表达式 alias,...~~~~~~~~~~~~~~~~~~~~~~~~~ 定义列的显示别名 FROM table;~~~~~~~~~~~~~~~~~ 子句，合起来就是 SQL 语句

**选择所有列：** SELECT * FROM departments; **选择特定列：** SELECT department_id, location_id FROM departments;

~

**SELECT 语句执行步骤：** 1.parse 编译阶段 ~~~~~~~~~~~~-library cache~ 库高速缓存：分为共享 SQL 区和共享 PL/SQL 区，存放 SQL select 语句正文和编译后的代码。 2.execcute 执行阶段 ~~~~~~~~~~~~~-data dictionart cache 数据字典高速缓存：存放执行 SQL select 语句时使用的数据文件，表，索引，列，用户和其他数据对象的定义和权限信息。 3.fetch 提取 ~~~~~~~~~~ 当用户发出 SQL select 语句时运行到提取数据阶段时，服务器进程首先搜索 database buffer cache ，如果找到了就直接使用而不进行磁盘操作；如果没找到就将进行磁盘操作把数据文件中的数据读入到 database buffer cahce 中。

DML

INSERT

INSERT 命令

使用基本的INSERT 语句一次可创建一行。如果使用所谓的子选择，则可以使用INSERT

命令将一个表中的若干行复制到另一个表。这种方法又称为INSERT SELECT 语句。幻

灯片示例使用的是以下INSERT 命令：

insert into dept_80 (select * from employees

where department_id = 80);

这种情况下，dept_80 表拥有与employees 表完全相同的结构。如果不是这种情况，

则可命名每个表中的列。SELECT 语句中选择的各个值与要插入的表中的各个列相关联。

列值按INSERT 和SELECT 语句中指定的顺序进行匹配。只要数据类型完全匹配即可。

例如：

insert into just_names (first, last)

(select first_name, last_name from employees);

其中just_names 表中仅有的两列的数据类型与employees 表中first_name 和

last_name 列的数据类型相同。

使用INSERT SELECT 方法可将一个或多个表中的大量数据装入到另一个表中。

INSERT INTO departments(department_id, department_name,

manager_id, location_id)

VALUES (70, &amp;apos;Public Relations&amp;apos;, 100, 1700);

UPDATE

UPDATE employees SET department_id = 70 WHERE employee_id = 113;

1 row updated.

update 语句执行步骤：

1.编译

2.执行步骤

2.1如果数据和回滚段数据不在database buffer cache 中，Oracle服务器进程把所需数据从数据文件中读到database buffer cache 中。

2.2Oracle服务器进程在要修改的数据行上加锁 。

2.3Oracle服务器进程将数据的变化信息和回滚所需的信息都记录在redo log buffer中。

2.4Oracle服务器进程将回滚段所需的原始值和对数据库所作的修改都写入database buffer cache。之后database buffer cache 中的所有这些数据块都将被标为脏数据，因为此时内外存的数据是不一致的。

DELETE

删除指定行

DELETE FROM departments WHERE department_name = &amp;apos;Finance&amp;apos;;

1 row deleted.

删除所有行

DELETE FROM copy_emp;

22 rows deleted.

MERGE

MERGE 合并表

使用MERGE 命令可在一个命令中执行UPDATE 和INSERT。可将一个源中的数据合并到

另一个源

语法

MERGE INTO table_name table_alias

USING (table|view|sub_query) alias

ON (join condition)

WHEN MATCHED THEN

UPDATE SET

col1 = col_val1,

col2 = col2_val

WHEN NOT MATCHED THEN

INSERT (column_list)

VALUES (column_values);

例子：

MERGE INTO copy_emp c

USING employees e

ON (c.employee_id = e.employee_id)

WHEN MATCHED THEN

UPDATE SET

c.first_name = e.first_name,

c.last_name = e.last_name,

c.email = e.email,

c.phone_number = e.phone_number,

c.hire_date = e.hire_date,

c.job_id = e.job_id,

c.salary = e.salary,

c.commission_pct = e.commission_pct,

c.manager_id = e.manager_id,

c.department_id = e.department_id

WHEN NOT MATCHED THEN

INSERT VALUES(e.employee_id, e.first_name, e.last_name,

e.email, e.phone_number, e.hire_date, e.job_id,

e.salary, e.commission_pct, e.manager_id,

e.department_id);

DDL

CREATE

DROP

Dropping a Table

删除表语法：

DROP TABLE [schema.] table [CASCADE CONSTRAINTS] [PURGE]

删除表时会删除：

1\. 数据

2\. 表结构

3\. 数据库触发器

4\. 相应的索引

5\. 关联的对象权限

DROP TABLE 语句的可选子句：

1\. CASCADE CONSTRAINTS：相关的引用完整性约束条件

2\. PURGE：无法闪回

注:

1\. 如果不使用PURGE 子句，则会将表定义、关联索引和触发器放置在回收站中。及表索引所占用的空间仍不会计入相关表空间的用户可用限额之内。也就是说，仍将这些空间视为已占用。表数据仍然存在，但在没有表定义的情况下无法进行访问。如果使用Enterprise Manager删除了表，则不需要使用PURGE 子句。

2\. 使用FLASHBACK TABLE 命令可从回收站恢复方案对象。PURGE RECYCLEBIN 命令可用于清空回收站。

3\. 需要使用CASCADE CONSTRAINTS 选项才能删除所有相关的引用完整性约束条件。

ALTER

RENAME

RENAME dept TO detail_dept;

TRUNCATE

Truncating a Table

截断表语法：

TRUNCATE TABLE [schema.] table [{DROP | REUSE} STORAGE]

使用此命令会产生以下影响：

1\. 通过将高水位标记(HWM) 设置到表开头，使表行不可用，将表标记为空。截断表会使表的行数据不可用，从而可以选择释放占用空间。

2\. 由于TRUNCATE TABLE 是DDL 命令，因此不会生成还原数据，而且会隐式提交命令。

3\. 相应的索引也会被截断。

4\. 不会截断外键引用的表。

5\. 使用此命令时不会触发删除触发器。

要删除所有表行，这种方式通常比发出DELETE 语句的速度要快很多倍，原因如下：

1\. Oracle 数据库重置表的HWM，而不是像DELETE 操作那样处理每一行。

2\. 不生成还原数据。

DCL

GRANT

REVOKE

COMMIT

COMMIT ：使更改成为永久性更改

ROLLBACK：撤消更改

COMMIT 和ROLLBACK 命令

默认情况下，不会提交输入的每个DML 命令。几个工具（包括iSQL*Plus）提供了一些

选项，使用这些选项可以基于每个命令进行提交，或基于一组命令进行提交。

在发出COMMIT 或ROLLBACK 之前，更改处于暂挂状态。仅执行更改的用户可以查看更

改后的数据。其他用户可选择相同数据，但只能看到更改之前的数据。其他用户不能对别

的用户已更改的数据发出DML。

默认情况下，当一个用户尝试更改另一个用户正更改的行时，此用户必须等待，直到执行

更改的用户提交或回退更改为止。这由Oracle 数据库的锁定机制自动进行控制。由于锁

定机制已经内置到行中，所以数据库绝不会用完锁。

ROLLBACK

COMMIT ：使更改成为永久性更改

ROLLBACK：撤消更改

没有提交(commit)的数据删除后无法rollback

INDEX

CREATE INDEX my_index ON employees(last_name, first_name);

CREATE INDEX emp_dept_id_idx ON emp (dept_id);

ORDER BY

ORDER BY

- ASC 升序。默认

- DESC 降序

SELECT last_name, job_id, department_id, hire_date

FROM employees

ORDER BY hire_date ;

PRIMARY KEY

CREATE TABLE employees(

employee_id NUMBER(6),

first_name VARCHAR2(20),

...

job_id VARCHAR2(10) NOT NULL,

CONSTRAINT emp_emp_id_pk

PRIMARY KEY (EMPLOYEE_ID));

ALTER TABLE emp

ADD CONSTRAINT my_emp_id_pk PRIMARY KEY (id);

Single-Row Functions

字符函数

大小写转换函数

LOWER ：

UPPER

INITCAP

EXAMPLE:

LOWER(&amp;apos;SQL Course&amp;apos;)：sql course

UPPER(&amp;apos;SQL Course&amp;apos;)：SQL COURSE

INITCAP(&amp;apos;SQL Course&amp;apos;)：Sql Course

SELECT &amp;apos;The job id for &amp;apos;||UPPER(last_name)||&amp;apos; is &amp;apos; ||LOWER(job_id) AS &quot;EMPLOYEE DETAILS&quot; FROM employees;

SELECT employee_id, last_name, department_id FROM employees WHERE LOWER(last_name) = &amp;apos;higgins&amp;apos;;

字符处理函数

CONCAT ：

将两个值联接到一起

SUBSTR：

抽取确定长度字符串

LENGTH：

以数字的形式显示字符串长度

INSTR：查找指定字符的数字位置

LPAD ：按右对齐填充字符值

RPAD：按左对齐填充字符值

TRIM：从字符串中截去头部或尾部的字符

REPLACE

EXAMPLE：

CONCAT(&amp;apos;Hello&amp;apos;, &amp;apos;World&amp;apos;)：HelloWorld

SUBSTR(&amp;apos;HelloWorld&amp;apos;,1,5)：Hello

LENGTH(&amp;apos;HelloWorld&amp;apos;)：10

INSTR(&amp;apos;HelloWorld&amp;apos;, &amp;apos;W&amp;apos;)：6

LPAD(salary,10,&amp;apos;*&amp;apos;)：*****24000

RPAD(salary, 10, &amp;apos;*&amp;apos;)：24000*****

TRIM(&amp;apos;H&amp;apos; FROM &amp;apos;HelloWorld&amp;apos;)：elloWorld

SELECT employee_id, CONCAT(first_name, last_name) NAME,

job_id, LENGTH (last_name),

INSTR(last_name, &amp;apos;a&amp;apos;) &quot;Contains &amp;apos;a&amp;apos;?&quot;

FROM employees

WHERE SUBSTR(job_id, 4) = &amp;apos;REP&amp;apos;;

数字函数

x

ROUND(column|expression, n) ：

将列，表达式的值进行舍入，保留n个小数位，或者省略了n，则没有小数位（如果n是负数，将舍入小数点左边的数字）

TRUNC(column|expression,n) ：

将列，表达式的值进行截断，保留n个小数位，或者省略了n，则n默认为零

MOD(m,n)：

返回m 除以n后的余数

其他参见：Oracle9i SQL Reference &quot;Number Functions&quot;

note

算术表达书

使用算术运算符（+,-,*,/）,可以创建数字和日期数据表达式。

运算符优先顺序：

包含空值的算术表达式的值为空

定义空值

包含空值的算术表达式的值为空

定义列别名

约束

NOT NULL 非空

连接运算符||

**Exmaple:**

SELECT first_name||last_name AS &quot;full_name&quot;

FROM employees;

文字字符串

Example:

SELECT last_name ||&amp;apos; is a &amp;apos;||job_id

AS &quot;Employee Details&quot;

FROM employees;

SELECT last_name ||&amp;apos;: 1 Month salary = &amp;apos;||salary Monthly

FROM employees;

数据类型

消除重复行DISTINCT

1\. 使用 distinct 消除重复行。

Example ：

SELECT DISTINCT department_id

FROM employees;

2\. 可以在 distinct 限定符之后指定多个列。 distinct 将影响所有指定的列，并且结果是列的每个不同组合。

例：

SELECT DISTINCT department_id, job_id

FROM employees;

限制选定的行WHERE

**SELECT *|{[DISTINCT] _column|expression_ [** **_alias_** **],...}**

FROM _table_

[WHERE _condition(s)_ ];

conditions:

列名 比较条件 限定值（列名/常数/值列表）

**1.比较条件：**

**其他比较条件**

注： != 和 ^= 也可代表不等于

**a.使用BETWEEN条件**

SELECT last_name, salary

FROM employees

WHERE salary BETWEEN 2500 AND 3500;

b.使用 IN 条件

SELECT employee_id, manager_id, department_id

FROM employees

WHERE last_name IN (&amp;apos;Hartstein&amp;apos;, &amp;apos;Vargas&amp;apos;);

c. 使用 LIKE 条件

c1\. 使用通配符_ 和 %

% 代表零个或多个字符

_ 代表一个字符

**例** **:**

SELECT first_name

FROM employees

WHERE first_name LIKE &amp;apos;S%&amp;apos;;

**c2.使用** **ESCAPE** **标示符，表示** **_** **和** **%**

例：

SELECT employee_id, last_name, job_id

FROM employees

WHERE job_id LIKE &amp;apos;%SA\_%&amp;apos; ESCAPE &amp;apos;\&amp;apos;;

**2.** **比较值**

a . 数值

例：

SELECT employee_id, last_name, job_id, department_id

FROM employees

WHERE department_id = 90 ;

b. 字符串和日期

例：

SELECT last_name, job_id, department_id

FROM employees

WHERE last_name = &amp;apos;WHALEN&amp;apos;;

#### SQL*Plus {#sql-plus}

sqlplus 命令基本格式：

sqlplus &lt;username&gt; [/&lt;password&gt;] [@&lt;connect_identifer&gt;] | / [as sysdba | as sysoper] / nolog

username:

password:

connect_identifer:

as sysdba/as sysoper:

nolog:只进入sqlplus不登陆

EXAMPLE:

[oracle@oracle ~]$ sqlplus /nolog

SQL&gt;connect hr/hr

[oracle@oracle ~]$ sqlplus /nolog

SQL&gt;connect hr/hr

1.从Shell 脚本调用SQL*Plus

脚本内容：

# Name of this file: batch_sqlplus.sh

# Count employees and give raise.

sqlplus hr/hr &lt;&lt;EOF

select count(*) from employees;

update employees set salary =

salary*1.10;

commit;

quit

EOF

exit

执行脚本：

$ ./batch_sqlplus.sh

2.从SQL*Plus 调用SQL 脚本

script.sql内容：

select * from departments where location_id = 1400;

quit

执行脚本：

$ sqlplus hr/hr @script.sql

3.基本的SqlPlus命令

命令 说明

remark 注释

set headsep 标题换行

ttitle 设置输出页的头标题

btitle 设置输出页的尾标题

column 对sql语句中的列进行格式化处理

break on 通知sqlplus在输出结果中插入空格

compute sum 通知sqlplus计算小计

set linesize(line) 设置sqlplus输出的最大行宽 --

set pagesize 设置页面的最大行数

set newpage 设置页面之间的空行数

spool sqlplus屏幕的文件输入输出命令

注释，同remark

-- 双杠，注释，同remark

set pause sqlplus屏幕输出结果时在页面之间停顿

save 保存当前session最近的sql语句至指定的文件中

host 返回到操作系统环境，类似!

start或@ 执行文件中的命令

edit 使用自定义的编辑器编辑指定文件

define_editor 自定义sqlplus里的编辑器

exit或quit 退出sqlplus

describe

描述表结构

column

columncolumn 是sqlplus里最实用的一个命令，很多时候sql语句输出的列宽度不合适而影响查看，都需要用到这个命令来更改select语句中指定列的宽度和标题。

大部分时候，我们可以简写column为col即可，主要有以下两种用法：

column c1 format a20      --将列c1（字符型）显示最大宽度调整为20个字符

column c1 format 9999999  --将列c1（num型）显示最大宽度调整为7个字符修改列标题

column c1 heading c2      --将c1的列名输出为c2

col DB_USER for a10

#### 1.Introduction {#1-introduction}

Architecture

Oracle server 包含：Oracle instance and Oracle database

1.Oracle 内存结构

与Oracle 实例关联的基本内存结构包括：

  1)系统全局区(SGA)：由所有服务器进程和后台进程共享

  2)程序全局区(PGA)：专用于每一个服务器进程或后台进程。每一个进程使用一个PGA

1.1 SGA 是包含实例的数据和控制信息的内存区。SGA 包含以下数据结构：

1.2 PGA

     -排序区（sort area）:用于处理SQL语句所需的排序。

     -游标状态区（cursor state）:用于指示会话当前使用的SQL语句的处理状态。

     -会话信息区（session information）：包含了会话的用户权限和优化统计信息。

     -堆栈区（stack space）：包括了其他的会话变量

如果是共享服务器进程或多线程的配置，以上这些结构除了堆栈区外大部分都将存在于SGA中。如果有lager pool,它们就会被存在于lager pool中，否则它们就会被存在于共享池中。

2.Oracle进程结构

  1) 用户进程：在数据库用户请求连接到Oracle 服务器时启动

  2) 服务器进程：可以连接到Oracle 实例，它在用户建立会话时启动

  3) 后台进程：在启动Oracle 实例时启动

3.Oracle数据库结构（存储结构）

物理数据库结构:

1.数据文件，OS块

构成Oracle 数据库的文件可分为以下几类：

1\. 控制文件：/pm/q/d/controln.ctl

2\. 重做日志文件：/pm/q/d/redon.log

3\. 数据文件：/pm/q/d/tn.dbf

逻辑结构：段、区和块

1\. 方案是数据库用户拥有的数据库对象的集合。方案对象是直接引用数据库数据的逻辑结构。方案对象包括表、视图、序列、存储过程、同义词、索引、集群和数据库链接等结构。一般而言，方案对象包括应用程序在数据库中创建的任何内容。

数据库对象（如表和索引）以段形式存储在表空间中。

2\. 段:每个段都包含一个或多个区。段是为特定逻辑结构分配的区集。

    2.1.数据段：每个非集群、非索引的组织表都有一个数据段。该表的所有数据都存储在表数据段的区中。对于分区表，每个分区都有一个数据段。

         每个集群都有一个数据段。集群中每个表的数据都存储在集群的数据段中。

    2.2.索引段：每个索引都有一个索引段，用于存储索引的所有数据。对于已分区索引，每个分区都有一个索引段。

    2.3.还原段：数据库管理员会创建一个UNDO 表空间，用于临时存储还原信息。

         还原段中的信息用于生成一致读取信息，在数据库恢复过程中，还可用于回退用户的未提交事务。

    2.4.临时段：当SQL 语句需要一个临时工作区来完成执行时，Oracle 数据库会创建临时段。执行完语句后，临时段的区会返回到实例，以供将来使用。

3\. 区是特定数目的相邻数据块（在一次分配中获取的），用于存储特定类型的信息。每个区只能存在于一个数据文件中。

4\. Oracle 数据库的数据以最细的粒度级存储在数据块中。一个数据块对应于磁盘上特定字节数的物理数据库空间。每个表空间的数据块大小是在创建表空间时指定的。数据库可以使用和分配Oracle 数据块中的空闲数据库空间。数据块是数据库中最小的I/O 单元。大小2-32K。 由OS磁盘块儿构成。

请为每个用户指定一个默认临时表空间，或指定一个在数据库级别使用的默认临时表空间。Oracle 数据库可动态分配空间。如果段的现有区已满，则会添加更多的区。

由于区是按需分配的，因此，段的区在磁盘上不一定是相邻的。

2.物理结构(OS块):

数据文件（一一对应区）

Oracle Instance

**Oracle instance**

**Oracle isntance** **包含：** **1.SGA** **：系统全局区，即一部分共享内存区** ~~~~~~~~~Oracle 利用区组（ granule ）来滚利 SGA 的大小。 ~~~~~~~~~~ 如果 SGA 小于 128M ， granule 即为 4M ；如果 SGA 大于等于 128M ， granule 即为 16M 。 指定 SGA 的最大值。 指定特定缓冲区大小 :SQLsystem set~~ 查看 SGA 值： SQLparameter;~~~~~~~~~~~~~~~~~~~sga;~~~~~~~~~~~~~~~~~~~~name,values from v$parameter where name=&amp;apos;SGA_MAX_SIZE&amp;apos;;~~~~~~~~~ ~~~~~1.1 数据库缓冲区高速缓存（ database buffer cache ）：缓存从数据库检索的数据块。 ~~~~~~~~~~ 当用户发出 SQL select 语句时运行到提取数据阶段时，服务器进程首先搜索 database buffer cache ，如果找到了就直接使用而不进行磁盘操作；如果没找到就将进行磁盘操作把数据文件中的数据读入到 database buffer cahce 中。 ~~~~~~~~~~ 缓冲区高速缓存中的缓冲区可能处于以下四种状态之一： ~~~~ ~~~~~~~ 1.1.1\. **已连接** ：可防止多个会话同时对同一数据块执行写操作。此时，其它会话正等待访问该块。 ~~~~~~~~~~~ **干净** ：缓冲区现已解除连接，如果没有再次引用当前内容（数据块），则可以考虑立即腾出该缓冲区。 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 此时，不是缓冲区的内容已与磁盘中存储的数据块内容同步，就是缓冲区中包含块的一致读取 (CR) 快照。 ~~~~~~~~~~~1.1.3\. **空闲或未使用** ：实例刚刚启动，缓冲区为空。此状态与 &quot; 干净 &quot; 状态非常相似，不同之处在于缓冲区尚未使用。 ~~~~~~~~~~~~ **脏** ：缓冲区不再处于已连接状态，但是内容（数据块）已更改， DBWn 必须先将内容刷新到磁盘，才能腾出缓冲区。 --------------------------------------&gt; 动态调整 database buffer cache 大小 :&gt;8i 以前版本，修改参数文件中以下值： &gt;DB_BLOCK_SIZE~~~~~~~~~# 缓冲区块儿的大小，创建数据库时设定的，如果更改需重启。 &gt;DB_BLOCK_BUFFERS~# 缓冲区块儿的个数。 &gt;9i 以后版本，修改参数文件中以下值： &gt;DB_CACHE_SIZA&gt;SQLalter system db_cache_size = 48M;&gt; 动态参数无需重启，即使生效。 --------------------------------------~ ~~~~~1.2.~ 重做日志缓冲区（ redo log buffer ）：高速缓存重做信息直到可以将其写入磁盘中存储的物理重做日志文件（用于实例恢复）。 ~~~~~1.3\. 共享池 (share pool) ：缓存可在用户间共享的各个结构。 ~~~~~~~~~~~~-library cache~ 库高速缓存：分为共享 SQL 区和共享 PL/SQL 区，存放 SQL select 语句正文和编译后的代码。 ~~~~~~~~~~~~-data dictionart cache 数据字典高速缓存：存放执行 SQL select 语句时使用的数据文件，表，索引，列，用户和其他数据对象的定义和权限信息。 --------------------------------------------------------------------Oracle 没有给出直接设置 library cache/data dictionary cache 的直接方法，只能通过修改参数文件中 SHARED_POPL_SIZE 的大小来间接的设置： --SQLsystem set SHARED_POOL_SIZE = 32M;-- 需重启 Oracle 数据库才能生效。 ------------------------------------------------------------------~~~~~1.4\. 大型池 (lager pool) ：是一个可选区域，可为某些大型进程（如 Oracle 备份和恢复操作、 I/O 服务器进程）提供大量内存分配。 ~~~~~1.5\. Java 池 (java pool) ：用于 Java 虚拟机 (JVM) 中特定会话的所有 Java 代码和数据。 ~-------------------------------- 使用动态参数： LAGRE_POOL_SIZE 调整 lager pool 的大小 使用动态参数： JAVA_POOL_SIZE~~~~ 调整 JAVA pool 的大小 SQLsystem set /--------------------------------~~~~~~1.6\. Streams 池 (Streams pool) ：由 Oracle Streams 使用。 --------------------------------Oracle 9i 及以后版本提供获得缓存信息统计工具： V$db_cache_advice~~~~~~~~~~~~~~~~~~~~~~~~~~~#database buffer cache 统计信息 V$SGA_DYNAMIC_COMPONENTS~~~~# 获得各种 buffer 的信息 -------------------------------- **2.Oracle （后台）进程** 常见的后台进程： 2.1\. 系统监视器 (SMON) ：出现故障后，在启动实例时执行崩溃恢复任务 2.2\. 进程监视器 (PMON) ：用户进程失败时执行进程清理任务 2.3\. 数据库写进程 (DBWn) ：将修改后的块从数据库缓冲区高速缓存写入磁盘中的数据文件 2.4\. 检查点 (CKPT) ：通过更新数据库的所有数据文件和控制文件指出最新的检查点 2.5\. 日志写进程 (LGWR) ：将重做日志条目写入磁盘 以上 5 个后台进程是必须得。 2.6\. 归档进程 (ARCn) ：发生日志切换时将重做日志文件复制到归档存储器（非必须的后台进程）

启动实例后， Oracle 软件会将实例与特定的数据库关联。这个过程称为装载数据库。接下来可以打开数据库，以便授权用户访问数据库。在同一台计算机上可以并发执行多个实例，每一个实例只访问自己的物理数据库。

每个实例只能操作一个数据库。在 oracle 集群中一个数据库可以同时被几个实例操作。

Oracle database

  1\. 控制文件(contro files)：包含有关数据库本身的数据（即物理数据库的结构信息）。

      这些文件对数据库而言至关重要。没有这些文件，就无法打开用于访问数据库数据的数据文件。一般为3份，最多8份，最少2份，互为镜像。

  2\. 数据文件(data files)：包含数据库中的用户数据或应用程序数据。一般3个。

  3\. 联机重做日志文件(redo log files)：可用来实现数据库实例恢复。

      如果数据库已崩溃但未丢失任何数据文件，那么使用这些文件中的信息可以通过实例恢复数据库。一般3个。

要成功运行数据库，以下附加文件也是非常重要的：

  4\. 参数文件(Parameter files)：用于定义启动实例时如何配置实例。

  5\. 口令文件(Password files)：允许用户远程连接到数据库并执行管理任务。

  6\. 备份文件：用于恢复数据库。当因介质故障或用户错误而损坏或删除了原始文件时，通常需要还原备份文件。

  7\. 归档日志文件(Archive log files)：包含实例生成的数据更改（重做）的实时历史记录。

      使用这些文件和数据库备份可以恢复丢失的数据文件。也就是说，使用归档日志可以恢复还原的数据文件。  

  8\. 跟踪文件(Trace files)：每个服务器和后台进程都可以对关联的跟踪文件执行写操作。

      如果在进程中检测到内部错误，进程就会将关于错误的信息转储到进程的跟踪文件中。

      写入到跟踪文件中的某些信息专门供数据库管理员使用，另一些信息则供Oracle 支持服务部门使用。

  9\. 预警日志文件(Alert log files)：是特殊的跟踪文件，又称为预警日志。数据库预警日志是按时间顺序列出的消息日志和错误日志。Oracle 建议您查看这些文件。

#### 2.install {#2-install}

**安装与准备过程**        

**1.** **下载软件，官方的地址要清楚**        

**2.** **安装依赖的包** [root@oracle ~]# yum install -y~make glibc libaio compat-libstdc++-33 compat-gcc-34 compat-gcc-34-c++ gcc libXp openmotif compat-db    

**3.** **配置** **/etc/sysctl.conf** ~kernel.shmall = 2097152 kernel.shmmax = 2147483648kernel.shmmni = 4096kernel.sem = 250 32000 100~128fs.file-max = 65536net.ipv4.ip_local_port_range = 65000net.core.rmem_default = 1048576net.core.rmem_max =~1048576net.core.wmem_default = 262144~net.core.wmem_max = 262144 [root@oracle ~]#-p  

**4.** **配置** **/etc/security/limits.conf** ~oracle~~~~~~~~~~~~~soft~~~nproc~~2047~oracle~~~~~~~~~~~~~hard~~~nproc~~16384oracle~~~~~~~~~~~~~soft~~~nofile~1024oracle~~~~~~~~~~~~~hard~~~nofile~65536  

**5.** **配置** **/etc/pam.d/login** session~~~~session~~~~~~ **6.** **创建组** **oinstall dba oper**    [root@oracle ~]# /usr/sbin/groupadd oinstall# /usr/sbin/groupadd dba# /usr/sbin/groupadd oper      

**7.** **创建** **oracle** **用户：** [root@oracle ~]#  /usr/sbin/useradd -g oinstall -G dba[,oper] oracle        

**8.** **创建必要的安装路径** [root@oracle ~]# mkdir -p /opt/oracle10g[root@oracle ~]# chown -R oracle:oinstall /opt/oracle10g[root@oracle ~]# chmod -R 775 /opt/oracle10g    

**9.** **配置环境变量：** [root@oracle ~]# vi ~oracle/.bash_profile~~~~~ORACLE_BASE~~~~~ORACLE_HOME~~~~~ORACLE_SID      

 ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++ 环境变量实例 :  export TMP=/tmpexport TMPDIR=$TMPexport ORACLE_BASE=/opt/oracle10gexport ORACLE_HOME=$ORACLE_BASE/product/10.2.0/db_1export ORACLE_SID=oracle0export ORACLE_TERM=xtermexport PATH=/usr/sbin:$PATHexport PATH=$ORACLE_HOME/bin:$PATHexport LD_LIBRARY_PATH=$ORACLE_HOME/bin:/bin:/usr/bin:/usr/local/bin:/usr/X11R6/bin/export CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib export JAVA_HOME=$ORACLE_HOME/jdkulimit -u 16384 -n 65536umask 022+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

**10.** **解压安装包** [oracle@oracle ~]$unzip ，

**11.** **设置客户端** **X** [root@oracle ~]# export DISPLAY= 客户端 IP:0.0[root@oracle ~]# xhost +  

**12.** **使用** **oracle** **用户运行安装程序：** [oracle@oracle ~]$runInstaller -ignoresysprereqs  

安装过程：

1\. 以管理组成员的身份登录计算机，管理组成员已获得了安装 Oracle 软件和创建、管理数据库的授权。 2\. 将数据库的分发 CD 插入到 CD 驱动器，或者浏览到 Oracle 数据库存放位置。 3\. 启动 OUI 。在 Linux 上的 &quot;XTerm&quot; 窗口中，输入 ./runInstaller 。此时会出现 &quot;Oracle Universal Installer&quot; 页。 4\. 浏览 OUI 页，根据安装计划指定预安装设置。 5\. OUI 会依据初始信息执行先决条件检查。 6\. 浏览 OUI 页，指定数据库配置选项。 OUI 会显示安装选项的概要信息。 7\. 单击 &quot;Install （安装） &quot; 开始安装 Oracle 软件。 如果在安装过程中选择创建启动数据库，那么 OUI 会调用以下所有配置辅助程序： 7.1 Oracle Net Configuration Assistant ：用于在安装期间配置基本网络组件，包括： - 监听程序名和协议地址 - 客户机将连接标识符解析为连接描述符时使用的命名方法 - tnsnames.ora 文件中的网络服务名 - 目录服务器使用情况 7.2 Oracle Database Configuration Assistant (DBCA) ：用于创建选定的启动数据库。 这个配置辅助程序完成后，可以取消帐户锁定并更改口令。 7.3 iSQL*Plus Configuration Assistant ：用于配置 iSQLPlus 使用的 Oracle ApplicationServer Containers for J2EE (OC4J) 实例，以及用于连接 Oracle 数据库的其它工具。 8\. 在 Linux 或 UNIX 安装期间出现提示时，请以 root 用户身份执行附加的配置脚本。 在 &quot;Xterm&quot; 窗口中，输入： $ su# password: oracle &lt;root 口令，在窗口中不显示 &gt;# cd /u01/app/oracle/oraInventory# ./orainstRoot.sh# cd /u01/app/oracle/product/10.2.0/db_1# ./root.sh9\. 在 Linux 或 UNIX 安装期间，接受默认的本地 bin 目录。完成脚本后，退出所有 相关的帐户和窗口以完成安装。 10\. 安装过程要结束时，请记下 URL 以备将来使用。  

 ~  

**11.** **高级安装选项** 11.1 使用 OUI 可以创建使用 Automatic Storage Management 的配置。 11.2 可以安装并配置 Enterprise Manager (EM) 框架。 Oracle Enterprise Manager Database Control 与数据库安装在同一个 Oracle 主目录中，          

且配置为在独立的 OC4J 实例上运行。必须执行独立安装才能实现 EM 集中管理功能。 11.3 如果选择使用 Oracle Enterprise Manager Database Control ，则可根据需要将数据库配置为使用 Oracle 建议的默认备份策略。 11.4 如果在安装期间选择使用 Oracle Enterprise Manager Database Control ，则可将 Oracle Enterprise Manager      

配置为向指定的电子邮件地址发送电子邮件预警。这些预警中可以包含诸如磁盘空间达到临界限制或数据库意外关闭等问题。 11.4 Oracle Database 10g 安装支持 RAC 功能，尤其是集群就绪服务 (CRS) 安装。 11.5 Oracle 主目录可以通过使用 Oracle Enterprise Configuration Management 工具进行复制。用户使用这个工具可以创建复制请求，      

然后计划和处理这些请求。可通过 EMGrid Control 使用此工具。  

 ~  

**12.** **安装选项：无提示模式** 要在无提示或隐藏模式下使用 OUI 安装并配置 Oracle 产品，请执行以下步骤： 1\. 创建 oraInst.loc 文件（如果该文件尚不存在）。如果此前安装过 Oracle 软件， 该文件很可能已存在于 /etc 中。 2\. 准备响应文件。每一产品和安装类型都有文件模板，如 enterprise.rsp 、 standard.rsp 和 netca.rsp 。 3\. 在交互模式下可以使用 OUI 记录响应文件，可以对该文件进行编辑，然后使用该文 件完成无提示模式或隐藏模式安装。请使用以下命令在 Linux 和 UNIX 环境中创建 响应文件： .runInstaller -record -destinationFile &lt;filename&gt; 其中， destinationFile 是文件位置。 4\. 在无提示模式或隐藏模式下运行 OUI 。 5\. 如果完成了仅软件安装，则可根据需要在无提示模式或非交互模式下运行 Oracle NetConfiguration Assistant (NetCA) 和 Database Configuration Assistant (DBCA) 。 有关详细信息，请参阅特定 OS 的《 Oracle 数据库安装指南》。  

###################################################################################################################install Oracle 简单的预配置配置脚本  

echo &quot;1\. 下载软件，官方的地址要清楚 &quot;

echo &quot;2\. 安装依赖的包 &quot;yum install -y make glibc libaio compat-libstdc++-33 compat-gcc-34 compat-gcc-34-c++ gcc libXp openmotif compat-db            

echo &quot;3\. 配置 /etc/sysctl.conf&quot; echo &quot;3.1 添加参数 &quot;echo &quot; &quot; &gt;&gt;/etc/sysctl.confecho &quot;##add for oracle install,begin&quot; &gt;&gt;/etc/sysctl.confecho &quot;kernel.shmall = 2097152&quot; &gt;&gt;/etc/sysctl.confecho &quot;kernel.shmmax = 2147483648&quot; &gt;&gt;/etc/sysctl.confecho &quot;kernel.shmmni = 4096&quot; &gt;&gt;/etc/sysctl.confecho &quot;kernel.sem = 250 32000 100 128&quot; &gt;&gt;/etc/sysctl.confecho &quot;fs.file-max = 65536&quot; &gt;&gt;/etc/sysctl.confecho &quot;net.ipv4.ip_local_port_range = 1024 65000&quot; &gt;&gt;/etc/sysctl.confecho &quot;net.core.rmem_default = 1048576&quot; &gt;&gt;/etc/sysctl.confecho &quot;net.core.rmem_max = 1048576&quot; &gt;&gt;/etc/sysctl.confecho &quot;net.core.wmem_default = 262144&quot; &gt;&gt;/etc/sysctl.confecho &quot;net.core.wmem_max = 262144&quot; &gt;&gt;/etc/sysctl.confecho &quot;##add for oracle install,END&quot; &gt;&gt;/etc/sysctl.conf    

echo &quot; 使参数及时生效 &quot;/sbin/sysctl -p

echo &quot;4\. 配置 /etc/security/limits.conf&quot; echo &quot;##add for oracle install,begin&quot; &gt;&gt;/etc/security/limits.confecho &quot;oracle~~~~~~~~~~~~~soft~~~nproc~~2047&quot; &gt;&gt;/etc/security/limits.confecho &quot;oracle~~~~~~~~~~~~~hard~~~nproc~~16384&quot; &gt;&gt;/etc/security/limits.confecho &quot;oracle~~~~~~~~~~~~~soft~~~nofile~1024&quot; &gt;&gt;/etc/security/limits.confecho &quot;oracle~~~~~~~~~~~~~hard~~~nofile~65536&quot; &gt;&gt;/etc/security/limits.confecho &quot;##add for oracle install,END&quot; &gt;&gt;/etc/security/limits.conf    

echo &quot;5\. 配置 /etc/pam.d/login&quot; echo &quot;##add for oracle install,begin&quot; &gt;&gt;/etc/pam.d/loginecho &quot;session~~~required~~~~/lib/security/pam_limits.so&quot; &gt;&gt;/etc/pam.d/loginecho &quot;session~~~required~~~~pam_limits.so&quot; &gt;&gt;/etc/pam.d/loginecho &quot;##add for oracle install,END&quot; &gt;&gt;/etc/pam.d/login    

echo &quot;6\. 创建组 oinstall dba oper&quot;    

/usr/sbin/groupadd oinstall/usr/sbin/groupadd dba/usr/sbin/groupadd oper~echo &quot;7\. 创建 oracle 用户 &quot;

/usr/sbin/useradd -g oinstall -G dba,oper oracle/usr/bin/passwd oracle

echo &quot;8\. 创建必要的安装路径 &quot;mkdir -p /opt/oracle10gchown -R oracle:oinstall /optchmod -R 775 /opt  

echo &quot;9\. 配置环境变量 &quot;echo &quot;##add for oracle install,begin&quot; &gt;&gt;~oracle/.bash_profileecho &quot;export TMP=/tmp&quot; &gt;&gt;~oracle/.bash_profileecho &quot;export TMPDIR=$TMP&quot; &gt;&gt;~oracle/.bash_profileecho &quot;export ORACLE_BASE=/opt/oracle10g&quot; &gt;&gt;~oracle/.bash_profileecho &quot;export ORACLE_HOME=$ORACLE_BASE/product/10.2.0/db_1&quot; &gt;&gt;~oracle/.bash_profileecho &quot;export ORACLE_SID=oracle0&quot; &gt;&gt;~oracle/.bash_profileecho &quot;export ORACLE_TERM=xtermecho &quot;export PATH=/usr/sbin:$PATH&quot; &gt;&gt;~oracle/.bash_profileecho &quot;export PATH=$ORACLE_HOME/bin:$PATH&quot; &gt;&gt;~oracle/.bash_profileecho &quot;export LD_LIBRARY_PATH=$ORACLE_HOME/bin:/bin:/usr/bin:/usr/local/bin:/usr/X11R6/bin/&quot; &gt;&gt;~oracle/.bash_profileecho &quot;export CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib&quot; &gt;&gt;~oracle/.bash_profileecho &quot;export JAVA_HOME=$ORACLE_HOME/jdk&quot; &gt;&gt;~oracle/.bash_profileecho &quot;ulimit -u 16384 -n 65536&quot; &gt;&gt;~oracle/.bash_profileecho &quot;umask 022&quot; &gt;&gt;~oracle/.bash_profileecho &quot;##add for oracle install,END&quot; &gt;&gt;~oracle/.bash_profi    

#############################################################################################################################

~

The following J2EE Applications have been deployed and are accessible at the URLs listed below.              

iSQL*Plus URL: [http://oracle.example.com:5560/isqlplus](http://oracle.example.com:5560/isqlplus)

iSQL*Plus DBA URL: [http://oracle.example.com:5560/isqlplus/dba](http://oracle.example.com:5560/isqlplus/dba)

Enterprise Manager 10g Database Control URL: [http://oracle.example.com:1158/em](http://oracle.example.com:1158/em)

Admin tools

**1.** **用于管理** **Oracle 数据库的工具** **,** **可以使用以下工具进行安装和升级：**

**Oracle Universal Installer (OUI)** **：** Oracle Universal Installer 用于安装 Oracle 软件和选件。它可以自动启动 Database Configuration Assistant (DBCA) 来创建数据库。 交互式 OUI ： windows ： $ORACLE_HOME/oui/install/setup.exelinux ： $ORACLE_HOME/oui/bin/runInstaller 非交互式 OUI ： windows ： $ORACLE_HOME/oui/install/setup.exe -responfile 响应文件 -silentlinux ： $ORACLE_HOME/oui/bin/runInstaller -responfile 响应文件 -silent

**Database Configuration Assistant (DBCA)** **：** DBCA 会根据 Oracle 提供的模板创建数据库。因此，可以复制预配置的种子数据库，或者，也可以创建自己的数据库和模板。

**Database Upgrade Assistant (DBUA)** **：** 该工具可以指导您将现有数据库升级至 Oracle 新版本。

**Oracle Net Manager** **：** 该工具用于配置 Oracle 数据库与应用程序的网络连接。

**2.** **用于管理** **Oracle 数据库的工具** **,** **以下工具用于管理** **Oracle 实例和数据库：**

**Oracle Enterprise Manager (EM)** **：** EM 将图形控制台、代理、公用服务和相关工具组合在一起，为管理 Oracle 产品提供了一个集成的综合性系统管理平台。在安装 Oracle 软件、创建或升级数据库及配置网络之后，可以将 Oracle Enterprise Manager 作为管理数据库的单一接口。它除了提供基于 Web 的用户界面来执行 SQL 命令外，还提供与管理数据库的其它 Oracle 组件（例如， Recovery Manager 和 Scheduler ）的无缝接口。 用于管理 Oracle 数据库的三个主要 Oracle Enterprise Manager 工具包括： - Enterprise Manager 数据库控制台：用于管理一个数据库 - Enterprise Manager 网格控制：用于同时管理多个数据库 - Enterprise Manager Java 控制台：用于访问不支持 Web 的工具

**SQL*Plus** **：** **SQL*Plus** 是管理数据库的标准命令行接口。

**iSQL*Plus** **：** **iSQL*Plus** 是一个基于浏览器的接口，它可连到 Oracle 数据库。

**Recovery Manager (RMAN)** **：** RMAN 是一款可以提供完整解决方案的 Oracle 工具，可用来备份、还原和恢复整个数据库或特定的数据库文件。

**Oracle Secure Backup** 为 Oracle Ecosystem 提供了磁带备份管理，其中包括： - 通过与 Recovery Manager 集成，保护磁带上的 Oracle 数据库 - 无缝支持 Oracle Real Application Clusters (RAC)- 对分布式客户机和介质服务器（包括 Oracle Application Server 、 Oracle Collaboration Suite 、 Oracle 主目录和二进制文件）进行集中式管理。

**数据泵：** 使用数据泵可以在数据库之间高速传输数据。例如，可能需要导出某个表，然后再将其导入另一数据库。

**SQL*Loader** **：** 使用 SQL*Loader 实用程序可以将来自外部文件的数据加载到 Oracle 数据库。有几个 Oracle 实用程序可以将数据加载到数据库表中， SQL*Loader 是其中的一个。

**命令行工具** ： - 要管理 Oracle Enterprise Manager ，请使用： emctl start | status | set | stop- 要停止和启动 iSQL*Plus ，请使用： isqlplusctl start | stop- 要管理监听程序，请使用： lsnrctl help | start | status | stop

OFA

Optimal Flexible Architecture (OFA)

OFA 的核心位置有一个命名方案，它提供的标准可应用于装载点（通常为物理磁盘）、这些装载点上的目录和子目录，最后可应用到文件本身。

装载点语法：

使用/pm 语法可以命名所有装载点，其中p 是字符串常量，m 是用于判别每个装载点的唯一固定长度密钥（通常为两位数）。例如，装载点可以是/u01 和/u02。

主目录语法：

使用/pm/h/u 语法可以命名所有主目录，其中pm 是装载点名称，h 是标准目录名，u 是目录所有者的名称。OFA 兼容主目录示例包括：

/u01/app/oracle

/u01/home/oracle

软件目录语法：

将每一个版本的Oracle 软件存储在与模式/pm/h/u/product/v 匹配的目录中，其中product 是文字，v 是代表版本号的变量。借助于这条语法可以启用OFA功能，同时执行多个版本的应用程序软件。Oracle Database 10g 10.2.0 版的OFA 兼容安装类似于：

/u01/app/oracle/product/10.2.0

命名子目录语法：

为了方便组织管理数据，可以将特定数据库的管理文件存储在与模式/h/admin/d/a/ 匹配的子目录中。其中h 是Oracle 软件所有者的主目录，admin 是文字，d 是数据库名称，a 是每个数据库管理文件的子目录。下面列出了这些管理文件子目录：

1.adhoc：  特定数据库的专用SQL 脚本

2.arch：    归档的重做日志文件

3.adump：  审计文件（将AUDIT_FILE_DEST 初始化参数设置为adump 目录。请定期清理此子目录。）

4.bdump：  后台进程跟踪文件

5.cdump：  核心转储文件

6.create：  用于创建数据库的程序

7.exp：      数据库导出文件

8.logbook：记录数据库状态和历史记录的文件

9.pfile：       实例参数文件

10.udump： 用户SQL 跟踪文件

文件命名语法：

使用以下数据库文件命名惯例可以很方便地标识数据库文件：

1) 控制文件：/pm/q/d/controln.ctl

2) 重做日志文件：/pm/q/d/redon.log

3) 数据文件：/pm/q/d/tn.dbf

这些文件名中使用的变量包括：

1) pm：前面所述的装载点名称

2) q：一个字符串，用于区分Oracle 数据与其它所有文件（通常称为ORACLE 或oradata）

3) d：初始化参数DB_NAME（数据库名称）的值

4) t：Oracle 表空间名

5) n：一个两位数的字符串

注：不要在/pm/q/d/ 路径中存储与d 数据库关联的控制文件、重做日志文件或数据文件以外的其它文件。

Environment Variables

Oracle 环境变量有很多，此处提到的环境变量是成功安装、使用Oracle 数据库的关键变量。虽然这些环境变量不需要进行设置，但是如果能在安装之前对其进行设置，则可避免将来发生的很多问题。

1）ORACLE_BASE：

    指定OFA 的Oracle 目录结构基础。该变量为可选项，如果选择使用它，则可简化日后的安装和升级操作。它是一个目录路径，如下例所示：

    /u01/app/oracle

2）ORACLE_HOME：

指定包含Oracle 软件的目录。它是一个目录路径，如下例所示：

ORACLE_BASE/product/10.2.0/db_1

3）ORACLE_SID：

初始实例名称（默认值为ORCL）。它是一个由数字和字母组成的字符串，必须以字母开头。Oracle 公司建议系统标识符最多使用八个字符。

5）NLS_LANG：

按language_territory.character set 格式指定会话的初始国家语言支持(NLS) 设置。例如，设置为：

AMERICAN_DENMARK.WE8MSWIN1252

通过这个设置可以将会话中的Oracle 消息、字母排列顺序、日期名称和月份名称设置为使用AMERICAN 语言。地区为DENMARK，这可以设置时间格式、日期格式，以及数字和货币惯例。字符集为WE8MSWIN1252，指示Oracle Net 将字符信息转换为该字符集。这个值在UNIX 中是一个环境变量，在Windows 中是一个注册表设置。

使用以下语句可以查询当前会话的实际NLS 设置：select * from nls_session_parameters;

有关有效的语言、地区、字符集和语言支持的详细信息，请参阅《GlobalizationSupport Guide》。

     注：Windows 安装默认使用注册表中的NLS_LANG 值，其中language 部分来自键盘语言。这样做的结果是，如果使用非美国键盘在Windows 上进行默认安装，则在NLS_LANG 设置中使用非美国值。这又会将NLS_SORT 会话变量的默认值设置为不同于&quot;二进制&quot;的值，使优化程序难以在从这个节点开始的会话中使用基于字符的索引。

#### 3.DBCA {#3-dbca}

Database Configuration Assistant (DBCA) 可以使用 Database Configuration Assistant (DBCA) 创建或删除数据库，或者更改数据库的配置。还可以根据预定义的模板列表创建数据库，或者通过使用现有数据库作为样本来 创建新数据库或模板。这有时被称为 &quot; 克隆数据库 &quot; 。 可通过执行以下步骤调用 DBCA ： 1\. 以已获得安装 Oracle 软件权限的管理组成员身份登录计算机。 2\. 根据需要设置环境变量。 3\. 输入 dbca 调用 DBCA 。 4\. 单击 &quot;Next （下一步） &quot; 继续操作。 DBCA 通过提供选项来帮助完成某些操作，例如，创建数据库。

**使用** **DBCA 创建数据库：** 1\. 在 &quot;DBCA Operations （ DBCA 操作） &quot; 页中，通过选择 &quot;Create a database （创建数据库） &quot; 来调用向导，以便配置和创建数据库。该向导会提示您输入后面步骤中提及的配置信息。在大多数页中，向导都提供可接受的默认设置。 2\. 选择创建数据库时使用的数据库模板类型。数据库模板有三种类型：数据仓库、通用数据库和事务处理数据库。这些模板可以复制预配置的数据库，包括数据文件。数据文件包括控制文件、重做日志文件以及所包括的各种表空间的数据文件。单击 &quot;Show Details （显示详细资料） &quot; 可查看每类数据库的配置。对于更复杂的环境，可能需要选择 &quot;Custom Database （定制数据库） &quot; 选项。 3\. Database Identification （数据库标识）：以 database_name.domain_name 格式输入全局数据库名，然后输入系统标识符 (SID) 。 SID 的默认值是数据库名称，用于唯一标识与数据库关联的实例。 4\. Management Options （管理选项）：使用此页设置数据库，以便通过 Oracle Enterprise Manager 对数据库进行管理。选择默认值： &quot;Configure the Database with Enterprise Manager （使用 Oracle Enterprise Manager 配置数据库） &quot; 。 5\. Database Credentials （数据库身份证明）：使用此页指定管理帐户（如 SYS 和 SYSTEM ）的口令。在本课中，使用 oracle 作为所有管理帐户的口令。 6\. Storage Options （存储选项）：指定希望数据库使用的存储机制类型（如文件系统）。 7\. Database File Locations （数据库文件位置）：根据需要进行选择。如果使用 Oracle Managed File (OMF) ，则不需要直接管理构成 Oracle 数据库的操作系统文件。可以按照数据库对象而不是文件名指定操作。有关详细信息，请参阅 &quot; 管理数据库存储结构 &quot; 一课。 8\. Recovery Configuration （恢复配置）：如果需要，请指定快速恢复区并启用归档。 9\. Database Content （数据库内容）：这些页提供的选项可用来选择组件，如 &quot;Sample Schemas （示例方案） &quot; ，使用定制脚本时也会用到这些选项。

10\. Initialization Parameters （初始化参数）：使用此页中的标签可以访问用来更改默认初始化参数设置的页： - Memory （内存）：使用此页可设置控制内存使用量的初始化参数。请使用 &quot;(A) Typical （典型） &quot; 或 &quot;(B) Custom （定制） &quot; 内存分配。 - Sizing （大小）：要指定块大小，请输入字节大小或接受默认值。 - Character Sets （字符集）：使用此页可指定数据库的字符集。最佳方案提示： Oracle 建议尽可能使用 Unicode 作为数据库字符集，因为这种字符集可灵活地支持 Web 技术及多数口头语言。 - Connection Mode （连接模式）：选择 &quot;Dedicated （专用） &quot; 或 &quot;Shared Server Mode （共享服务器模式） &quot; 。有关详细信息，请参阅 &quot; 配置 Oracle 网络环境 &quot; 一课。 注：有几个初始化参数是在数据库生存期设置的，如 DB_BLOCK_SIZE 和 CHARACTER_SET 参数。

11\. Database Storage （数据库存储）：如果为数据库选择了一个预配置模板，则不能添加或删除控制文件或数据文件。 注：为便于参考，可能需要将数据库定义保存为 HTML 文件。 12\. Creation Options （创建选项）：提供创建数据库、将数据库定义保存为模板和生成脚本的选项。如果选择所有选项，那么 DBCA 首先会保存数据库模板，然后在目标目录中生成脚本，最后创建数据库。

**创建数据库设计模板** 模板是预定义的数据库定义，可将其用作创建新数据库的起点。如果在数据库创建过程中未创建模板，则可以随时通过调用 DBCA 来创建模板。可通过三种方法创建模板： 1 ） ~ 根据现有模板 2 ） ~ 根据现有数据库（仅结构） 3 ） ~ 根据现有数据库（结构与数据） DBCA 会指导您完成创建数据库设计模板的各个步骤。

**使用** **DBCA 删除数据库** 要在 UNIX 或 Linux 中删除（或配置）数据库，必须在启动 DBCA 的 shell 中设置 ORACLE_SID 。在终端窗口中输入 dbca ，然后单击 &quot;Welcome （欢迎使用） &quot; 页上的 &quot;Next （下一步） &quot; ，即可启动 DBCA 。要删除数据库，请执行以下步骤： 1\. 在 &quot;Operations （操作） &quot; 页上，选择 &quot;Delete a Database （删除数据库） &quot; ，然后单击 &quot;Next （下一步） &quot; 。 2\. 选择要删除的数据库（本课中为 hist ），然后单击 &quot;Finish （完成） &quot; 。 3\. 单击 &quot;Yes （是） &quot; 确认删除。 删除数据库时会删除数据库的数据文件、重做日志文件、控制文件和初始化参数文件。 使用 DROP DATABASE 语句可删除所有控制文件及控制文件中列出的其它所有数据库文件。要成功使用 DROP DATABASE 语句，必须符合以下所有条件： 1 ）数据库必须已装载且已关闭。 2 ）必须以独占方式而不是共享模式装载数据库。 3 ）数据库必须装载为 RESTRICTED 。 此语句的一个示例是： DROP DATABASE;DROP DATABASE 语句对归档日志文件不起作用，对数据库的副本或备份也不起作用。 最好使用 Recovery Manager (RMAN) 来删除这种文件。如果数据库保存在裸磁盘中，则不会删除实际裸磁盘专用文件。

#### 4.Managing Instance {#4-managing-instance}

Enterprise Manager

instance

lsnrctl status   监听

emctl status dbconsole

[http://host](http://host) name:port number/em

$ORACLE_HOME/install/portlist.ini # 查询端口

emca -config dbcontrol db [-repos (create

emca -config dbcontrol db -repos create

iSQL*Plus

iSQL*Plus 通常会使用端口号5560，除非Oracle Universal Installer (OUI) 检测到某个程序正使用该端口。

通过检查$ORACLE_HOME/install/portlist.ini 可找到iSQL*Plus使用的端口。

1.

$ORACLE_HOME/bin/isqlplusctl start

2.

[http://host](http://host) name:port/isqlplus

3.为了以SYSDBA 和SYSOPER 身份进行访问而设置iSQL*Plus

出现&quot;iSQL*Plus Connection Role（iSQL*Plus 连接角色）&quot;页时，请注意，出于安全方面的原因，需要对SYSOPER 和SYSDBA 角色进行特殊的设置和验证。为此，必须在Oracle Application Server Containers for J2EE (OC4J) 用户管理器中设置一个用户，然后给该用户授予webDba 角色访问权限。通过执行以下步骤可达到此目的。

注意，JAVA_HOME OS 环境变量必须设置为$ORACLE_HOME/jdk。

1\. 更改到正确目录：

cd $ORACLE_HOME/oc4j/j2ee/isqlplus/\

application-deployments/isqlplus

2\. 运行JAZN shell：

$JAVA_HOME/bin/java \

-Djava.security.properties=\

$ORACLE_HOME\

/oc4j/j2ee/home/config/jazn.security.props \

-jar $ORACLE_HOME/oc4j/j2ee/home/jazn.jar \

-user &quot;iSQL*Plus DBA/admin&quot; \

-password welcome -shell

3\. 通过选择用户名和口令创建一个用户：

JAZN&gt; adduser &quot;iSQL*Plus DBA&quot; wood webdba #adduser &quot;iSQL*Plus DBA&quot; username password

4\. 给该用户授予webDba 角色：

JAZN&gt; grantrole webDba &quot;iSQL*Plus DBA&quot; wood

5\. 退出JAZN shell：

JAZN&gt; exit

SQL*Plus

sqlplus 命令基本格式： sqlplus &lt;username&gt; [/&lt;password&gt;] [@&lt;connect_identifer&gt;] | / [as sysdba | as sysoper] / nologusername:password:connect_identifer:as sysdba/as sysoper:nolog: 只进入 sqlplus 不登陆

EXAMPLE:[oracle@oracle ~]$ sqlplus /nologSQL&gt;connect hr/hr[oracle@oracle ~]$ sqlplus /nologSQL&gt;connect hr/hr

**1.** **从** **Shell 脚本调用** **SQL*Plus** 脚本内容： # Name of this file: batch_sqlplus.sh# Count employees and give raise.sqlplus hr/hr &lt;&lt;EOFselect count(*) from employees;update employees set salary =salary*1.10;commit;quitEOFexit 执行脚本： $ ./batch_sqlplus.sh

**2.** **从** **SQL*Plus 调用** **SQL 脚本** script.sql 内容： select * from departments where location_id = 1400;quit 执行脚本： $ sqlplus hr/hr @script.sql

**3.** **基本的** **SqlPlus** **命令**

| 命令 | 说明 |
| --- | --- |
| remark | 注释 |
| set headsep | 标题换行 |
| ttitle | 设置输出页的头标题 |
| btitle | 设置输出页的尾标题 |
| column | 对 sql 语句中的列进行格式化处理 |
| break on | 通知 sqlplus 在输出结果中插入空格 |
| compute sum | 通知 sqlplus 计算小计 |
| set linesize(line) | 设置 sqlplus 输出的最大行宽 -- |
| set pagesize | 设置页面的最大行数 |
| set newpage | 设置页面之间的空行数 |
| spool | sqlplus 屏幕的文件输入输出命令 |
|  | 注释，同 remark |
| -- | 双杠，注释，同 remark |
| set pause | sqlplus 屏幕输出结果时在页面之间停顿 |
| save | 保存当前 session 最近的 sql 语句至指定的文件中 |
| host | 返回到操作系统环境，类似 ! |
| start 或 @ | 执行文件中的命令 |
| edit | 使用自定义的编辑器编辑指定文件 |
| define_editor | 自定义 sqlplus 里的编辑器 |
| exit 或 quit | 退出 sqlplus |

Initialization Parameter Files

初始化参数文件 Initialization Parameter Files 启动实例时系统会读取初始化参数文件。参数文件有两种类型： **1.** **服务器参数文件：** 这是首选的初始化参数文件类型。这是一个由数据库服务器写入或读取的二进制文件，不能手动进行编辑。此文件驻留在执行 Oracle 数据库的服务器中，而且永远存在，不受数据库关闭或启动的影响。通常将这个文件称为服务器参数文件 (SPFILE) 。此文件的默认名称为 spfile&lt;SID&gt;.ora ，启动时会自动搜索此文件。

**2.** **文本初始化参数文件：** 这类初始化参数文件可由数据库服务器读取，但不能由其写入。必须使用文本编辑器手动设置和更改初始化参数设置，这些设置是永久的，不受数据库关闭或启动的影响。此文件的默认名称为 init&lt;SID&gt;.ora ，如果启动时未找到 SPFILE ，则自动搜索此文件。建议您创建 SPFILE ，以动态方式维护初始化参数。使用 SPFILE ，可以在服务器端的磁盘文件中永久存储和管理初始化参数。

在新创建的系统中是没有 init&lt;SID&gt;.ora 的，可以通过以下方式创建： SQL&gt; create pfile from spfile;[oracle@oracle ~]$cp~~$ORACLE_HOME/dbs/init.ora~~~$ORACLE_HOME/dbs/init&lt;SID&gt;.ora

初始化参数被分为两组：基本初始化参数和高级初始化参数。 大多数情况下，只需设置和优化 32 个基本参数便可获得合理的数据库性能。极少数情况下，才需要修改高级参数来获得最佳性能。 **1.** **基本参数的示例包括适用于特定类型文件的** **&quot;** **目标** **&quot;** **或目录名：** AUDIT_FILE_DESTBACKGROUND_DUMP_DESTCORE_DUMP_DESTDB_CREATE_FILE_DESTDB_CREATE_ONLINE_LOG_DEST_nDB_RECOVERY_FILE_DESTUSER_DUMP_DEST

**2.** **高级初始化参数** CONTROL_FILES 参数指定了一个或多个控制文件名。 Oracle 强烈建议您多路复用并镜像控制文件。这个参数值的范围在 1 至 8 个文件名（与路径名）之间。默认范围与 OS 相关。 DB_BLOCK_SIZE 参数指定了 Oracle 数据库块的大小（以字节为单位）。这个值是在创建数据库时设置的，此后不可更改。值范围： 2048 到 32768 （与 OS 相关）。默认值： 8K （与 OS 相关）。 DB_CACHE_SIZE 参数指定了标准块缓冲区高速缓存的大小。值范围：至少为 16 MB 。 默认值： 48 MB 。 SHARED_POOL_SIZE 参数指定了共享池大小，以字节为单位。共享池包含共享游标、存储过程、控制结构和并行执行消息缓冲区等对象。较大的值可以提高多用户系统的性能。值范围：区组的大小－ 一个与 OS 相关的值。默认值：如果是 64 位系统，则为 64 MB ，否则为 16 MB 。 UNDO_MANAGEMENT 参数指定了系统应使用哪种还原空间管理模式。如果设置为 AUTO ，则在系统管理还原 (SMU) 模式下启动实例。否则，在回退还原 (RBU) 模式下启动实例。在 RBU 模式下，还原空间从外部被分配为回退段。在 SMU 模式下，还原空间从外部被分配为还原表空间。值范围： AUTO 或 MANUAL 。默认值：如果启动第一个实例时省略了 UNDO_MANAGEMENT 参数，则使用默认值 MANUAL ，而且在 RBU 模式下启动实例。如果这不是第一个实例，那么启动该实例采用的还原模式与启动其它所有现有实例的还原模式相同。

Alert Log

**查看预警日志** 每个数据库都有一个 alert_&lt;sid&gt;.log 文件。该文件位于数据库所在的服务器上，存储在 background_dump_dest 初始化参数指定的目录中。数据库预警文件按时间 顺序记录消息和错误，包括以下内容： 1.~ 启动时使用的所有非默认初始化参数 2.~ 发生的所有内部错误 (ORA-600) 、块损坏错误 (ORA-1578) 和死锁错误 (ORA-60)3\. 管理操作（如 CREATE 、 ALTER 、 DROP DATABASE 和 TABLESPACE 等 SQL 语句）， ~~~ 以及 Enterprise Manager 或 SQL*Plus 语句（ STARTUP 、 SHUTDOWN 、 ARCHIVE LOG 和 RECOVER ） 4.~ 与共享服务器和分派程序进程的功能相关的若干消息和错误 5.~ 自动刷新实体化视图过程中发生的错误

Enterprise Manager 会监视预警日志文件，而且在发生严重错误时还会发出通知。另外，通过浏览日志还可查看非严重的错误和说明性消息。该文件会增长到无法管理的大小。 可以时不时地备份预警文件，然后删除当前的预警文件。当数据库尝试再次写入预警文件时，它会重新创建一个新预警文件。

Dynamic Performance Views

**动态性能视图** Oracle 数据库还维护与数据库实例的操作和性能相关的动态数据集。这些动态性能视图建立在根据数据库服务器内的内存结构构建的虚拟表基础上。也就是说，它们不是驻留在数据库中的常规表。因此，在装载或打开数据库之前其中的某些视图就能显示数据。 动态性能视图包括关于以下内容的信息： 1.~ 会话 2.~ 文件状态 3.~ 作业和任务的进度 4.~ 锁定 5.~ 备份状态 6.~ 内存使用和分配 7.~ 系统和会话参数 8\. 执行 9.~ 统计信息和度量 注： DICT 和 DICT_COLUMNS 视图还包含这些动态性能视图的名称。

**动态性能视图：用法示例** a. 如果消耗的 CPU 时间大于 200,000 毫秒，则 SQL 语句及其相关的执行次数是什么？ SQL&gt; SELECT sql_text, executions FROM v$sql WHERE cpu_time &gt; 200000;b. 在最后一天内，哪些会话从 EDRSR9P1 计算机登录？ SQL&gt; SELECT * FROM v$session WHERE machine = &amp;apos;EDRSR9P1&amp;apos; and logon_time &gt; SYSDATE - 1;c. 当前保留锁（用于阻塞另一用户）的任何会话的会话 ID 是什么，以及锁的保留时间有多久？ SQL&gt; SELECT sid, ctime FROM v$lock WHERE block &gt; 0;

**动态性能视图：注意事项** 1.~ 这些视图由 SYS 用户拥有。 2.~ 不同时间可以使用不同视图： ~~~- 实例已启动。 ~~~- 数据库已装载。 ~~~- 数据库已打开。 3.~ 可查询 V$FIXED_TABLE 来了解所有视图名称。 4.~ 这些视图通常又称为 &quot;v$ 视图 &quot; 。 5.~ 由于数据是动态的，因此这些视图不能保证读取是一致性的。

spfile/pfile

~~ **pfile(Initialization Parameter File)** 用于 Oracle8i ，在 oracle9i 中也可以用，以文本形式存在，可以用文本编辑器对其中参数进行修改。 ~~ **spfile(Server-Side Initialization Parameter File)** 用于 oracle9i ，以二进制文本形式存在，不能用文本编辑器对其中参数进行修改。 pfile 改正了 pfile 管理混乱的问题，在多结点的环境里， pfile 会有多个 image ，启动时候需要跟踪最新的 image 。这是个烦琐的过程。用 spfile 以后，所有参数改变都写到 spfile 里面（只要定义 scope=spfile 或 both ）

1\. 查看 spfile location(Show parameter pfile/spfile;) Sql 代码： show parameter spfile~2\. 从 spfile 获取 pfileSql 代码： sqlplus /nolog~~connect / as sysdba~~Create pfile=&amp;apos;d:pfileSID.ora&amp;apos; from spfile;~~Create pfile=&amp;apos;d:pfileSID.ora&amp;apos; from spfile= &amp;apos;spfile_location&amp;apos;;~

3\. 从 pfile 获取 spfile Sql 代码： shutdown immediate~~Create spfile from pfile=&amp;apos;Your_pfile_location&amp;apos;~~Create spfile=&amp;apos;spfile_location&amp;apos; from pfile= &amp;apos;Your_pfile_location&amp;apos;~

4\. 动态修改参数 Sql 代码： ~alter system set parameter=Value scope= spfile|both|memory~(1)scope=spfile 对参数的修改仅记录在服务器初始化参数文件中。该选项同时适用于动态与静态的初始化参数。修改后的参数只有下一次启动数据库时更改才会生效。 (2)scope=memory 对参数的修改记录在内存中，对于动态初始化参数，更改立即生效。修改并不会被记录在服务器端的初始化参数中。下一次启动数据库时更改失效，不能用于静态初始化参数。 (3)scope= both 对参数的修改同时记录在内存中和服务器端的初始化参数文件中。为默认使用值。

**注：对于** **non-dynamic-parameter** **之类的参数，先生成** **pfile** **文件，然后修改** **pfile** **中的对应参数** &lt; /SPAN&gt;

查看初始化参数 Sql 代码： show parameters~

7\. 执行 startup 时，按如下顺序寻找初始化参数文件： (1)spfile.ora (2)pfile.ora (3) 都没找到，则在默认位置寻找默认名称的服务器端初始化参数文件。 (4) 还没找到，则在默认位置寻找默认名称的文本初始化参数文件。

8\. 查看系统全局区大小 : Sql 代码： ~show sga;~~~select * from v$sga;~

9\. 使用 pfile/spfile 启动数据库 如果你想使用 pfile 启动数据库，你可以在启动时指定 pfile 或者删除 spfile. Sql 代码： startup pfile=&amp;apos;E:\Oracle\admin\eyglen\pfile\init.ora&amp;apos;;~

你不能以同样的方式指定 spfile ，但是可以创建一个包含 spfile 参数的 pfile 文件，指向 spfile. SPFILE 是一个自 Oracle9i 引入的初始化参数，类似于 IFILE 参数。 SPFILE 参数用于定义非缺省路径的 spfile 文件。可以在 PFILE 链接到 SPFILE 文件 , 同时在 PFILE 中定义其他参数，如果参数重复设置，后读取的参数将取代先前的设置。

10.~ 查看系统是以 pfile 还是 spfile 启动 (1) 查询 v$parameter 动态视图，如果以下查询返回空值，那么你在使用 pfile. Sql 代码： SELECT name,value FROM v$parameter WHERE name=&amp;apos;spfile&amp;apos;;~

(2) 可以使用 SHOW 命令来显示参数设置，如果以下结果 value 列返回空值，那么说明你在使用 pfile:Sql 代码 ~ ： SHOW PARAMETER spfile~

(3) 查询 v$spparameter 视图 如果以下查询返回 0 值，表示你在使用 pfile, 否则表明你使用的是 spfile: Sql 代码： SELECT COUNT(*) FROM v$spparameter WHERE value IS NOT NULL;~

或者使用以下查询，如果 true 值返回非 0 值，那么说明我们使用的是 spfile. Sql 代码： ~select isspecified, count(*) from v$spparameter group~~by isspecified;~

11\. 修改 Oracle 最大连接数 ~a. 以 sysdba 身份登陆 PL/SQL 或者 Worksheet ~b. 查询目前连接数 ~~~~~~ 代码 ~~~~~~show parameter processes;~~c. 更改系统连接数 ~~~~~Java 代码 ~~~~~system set processes=1000 scope= spfile;BR&gt; BR&gt; ~~d. 创建 pfile~~~~~~Java 代码 ~~~~~~pfile from spfile;~~e. 重启 Oracle 服务或重启 Oracle 服务器

12\. 查询 Oracle 游标使用情况的方法 ~~~~~Java 代码： ~~~~select * from v$open_cursor where user_name = &amp;apos;TRAFFIC&amp;apos; ； ~

13\. 查询 Oracle 会话的方法 ~~~~Java 代码： ~~~select * from v$session~

14\. 关于 init.ora.XXXXXXXX~init.ora.XXXXXXXX 在 $ORACLE_BASE/admin/{SID}/pfile/ 目录下。当 database 通过 DBCA (Database creation assistant) 创建的时候 , DBCA 将创建一个 init.ora 文件用来创建 database, 这个时候，他将利用刚才创建的 init.ora 文件来创建 spfile, 然后再修改 init.ora 文件名到一个类似 init.ora.XXXXXXXX 。 ~~~XXXXXXXX is a timestamp of a sort 。 This init.ora.XXXXXXXX will not be used by Oracle afterwards, so modifiying it will not change the init parameters 。

Startingup Instance

**A.** **启动实例涉及以下任务：** 1\. 按以下顺序搜索 &lt;oracle_home&gt;/dbs 中具有特定名称的文件： ~~~~~- spfile&lt;SID&gt;.ora~~~~~- 如果未找到，则搜索 pfile.ora~~~~~- 如果未找到，则搜索 init&lt;SID&gt;.ora 这是包含实例初始化参数的文件。使用 STARTUP 指定 PFILE 参数可覆盖此默认行为。 2\. 分配 SGA3\. 启动后台进程 4\. 打开 alert&lt;SID&gt;.log 文件和跟踪文件 注： SID 是用于标识实例的系统 ID （例如 ORCL ）

**B.startup option**

**1.** **启动** **Oracle 数据库实例：** **NOMOUNT** 1.1\. 只启动数据库实例。（分配 SGA 和启动后台进程） 1.2\. 在创建数据库期间、重新创建控制文件期间，或执行某些备份和恢复方案期间，通常只在 NOMOUNT 模式下启动实例。 需要 init.ora 文件。

**2.** **启动** **Oracle 数据库实例：** **MOUNT** 启动实例并加载数据文件。 装载数据库涉及以下任务： 1\. 将数据库与以前启动的实例关联 2.~ 定位并打开参数文件中指定的控制文件（打开 controlfile ） 3.~ 通过读取控制文件来获取数据文件和联机重做日志文件的名称和状态。（定位 datafile,redolog 等） 但是，此时不必执行任何检查便可验证数据文件和联机重做日志文件是否存在要执行特定的维护操作，请启动实例，然后装载数据库，但不要打开该数据库。 例如，必须在执行以下任务期间装载数据库，但不要打开数据库： -~ 重命名数据文件（打开数据库时可重命名脱机表空间的数据文件。） - 启用和禁用联机重做日志文件归档选项 -~ 执行完整的数据库恢复 注：即使发出了 OPEN 请求，数据库仍可能处于 MOUNT 模式。这是因为可能需要以某种方式恢复数据库。

**3.** **启动** **Oracle 数据库实例：** **OPEN** 3.1 启动实例并加载数据文件，并打开。（打开 datafile,redolog 等） 正常的数据库操作意味着启动了实例、装载且打开了数据库。使用正常的数据库操作，任何有效用户都可连接到数据库，而且可执行典型的数据访问操作。 打开数据库涉及以下任务： 3.1.1\. 打开联机数据文件 3.1.2.~ 打开联机重做日志文件 如果尝试打开数据库时任一数据文件或联机重做日志文件不存在，则 Oracle 服务器会返回错误。 在最后这个阶段， Oracle 服务器会验证是否可以打开所有数据文件和联机重做日志文件，还会检查数据库的一致性。如有必要，系统监视器 (SMON) 后台进程将启动实例恢复。 open 状态的两个选项： SQL&gt; startup open read onlySQL&gt; startup open read write

**4.** **启动** **Oracle 数据库实例：** **FORCE** **~~startup** **force=shutdown abort+startup** ~~~ 强制启动方式 ~~ 当不能关闭数据库时，可以用 startup force 来完成数据库的关闭 ~~ 先关闭数据库，再执行正常启动数据库命令

------------------

open 阶段验证数据库一致性

------------ORACLE 的几种启动方式 1 、 startup nomount~~ 非安装启动，这种方式启动下可执行：重建控制文件、重建数据库 ~~ 读取 init.ora 文件，启动 instance ，即，这种启动只。 ~~~2 、 startup mount dbname~~ 安装启动，这种方式启动下可执行： ~~ 数据库日志归档、 ~~ 数据库介质恢复、 ~~ 使数据文件联机或脱机， ~~ 重新定位数据文件、重做日志文件。 ~~ 执行 &quot;nomount&quot; ，然后打开控制文件，确认数据文件和联机日志文件的位置，但此时不对数据文件和日志文件进行校验检查。 3 、 startup open dbname~~ 先执行 &quot;nomount&quot; ，然后执行 &quot;mount&quot; ，再打开包括 Redo log 文件在内的所有数据库文件，这种方式下可访问数据库中的数据。 4 、 startup ，等于以下三个命令 ~nomount~database mount~database open5 、 startup restrict~~ 约束方式启动 ~~ 这种方式能够启动数据库，但只允许具有一定特权的用户访问非特权用户访问时，会出现以下提示： ~ ： ~ORACLE 只允许具有 RESTRICTED SESSION 权限的用户使用 6 、 startup force7 、 startup pfile= 参数文件名 ~~ 带初始化参数文件的启动方式 ~~ 先读取参数文件，再按参数文件中的设置启动数据库 ~~ 例： startup pfile=E:\Oracle\admin\oradb\pfile\init.ora8 、 startup EXCLUSIVE

Shutdown instance

关闭 Oracle 数据库实例

关闭模式： A~= ABORTI~= IMMEDIATET~= TRANSACTIONALN~= NORMAL

**ABORT** **：** 在关闭之前只需执行最少量的任务。由于这种模式在启动之前需要进行恢复，所以只在需要时才使用此模式。 当启动实例时出现了问题，或者因紧急情况（如得到通知在数秒内断电）需要立即关闭时，如果其它关闭方式都不起作用，通常选择使用此模式。 **~~~~~** **关闭时：** ~~~~~1.~ 修改过的缓冲区不写入到数据文件 ~~~~~~ 不回退未提交的更改 **~~~~~** **启动时：** ~~~~~1.~ 使用联机重做日志文件重新应用更改 ~~~~~2.~ 使用还原段回退未提交的更改 ~~~~~3.~ 会释放资源 **IMMEDIATE** **：** 这是最常用的选项。选择此模式会回退未提交的事务处理。 **~~~~~~** **关闭时：** ~~~~~~1.~ 执行 IMMEDIATE 时，会回退未提交的更改 ~~~~~~2.~ 数据库缓冲区高速缓存会写入到数据文件 ~~~~~~3.~ 会释放资源 ~~~~~~ **启动时：** ~~~~~~ 不用恢复实例 **TRANSACTIONAL** **：** 允许完成事务处理 ~ **NORMAL** **：** 等待会话断开

SHUTDOWN 选项 : **SHUTDOWN NORMAL** NORMAL 是默认的关闭模式。以正常模式关闭数据库时会发生以下情况： 1.~ 可以建立新连接。 2\. 服务器等待所有用户断开连接才完成关闭。 3.~ 数据库和重做缓冲区会写入到磁盘中。 4.~ 后台进程被终止，从内存中删除 SGA 。 5\. 服务器在关闭实例之前关闭并断开数据库。 6.~ 下一次启动不需要进行实例恢复。 **SHUTDOWN TRANSACTIONAL** 以事务处理模式关闭数据库可防止客户机丢失数据，包括客户机当前活动的结果。以事务处理模式关闭数据库时会发生以下情况： 1.~ 任何客户机都不能利用这个特定实例启动新事务处理。 2.~ 客户机在结束正在进行的事务处理后断开连接。 3.~ 完成所有事务处理后立即执行关闭。 4.~ 下一次启动不需要进行实例恢复。 **SHUTDOWN IMMEDIATE** 以立即模式关闭数据库时会发生以下情况： 1\. 数据库正在处理的当前 SQL 语句尚未完成。 2\. 服务器不等待当前连接到数据库的用户断开连接。 3\. 服务器会回退活动的事务处理，而且会断开所有已连接的用户。 4\. 服务器在关闭实例之前关闭并断开数据库。 5.~ 下一次启动不需要进行实例恢复。

#### 5.Storage Structures {#5-storage-structures}

OS blocks

Blocks

**数据库块的结构**

~

Oracle 数据块包含： 1\. 块头：块头包含段类型（如表或索引）、数据块地址、表目录、行目录和事务处理插槽。每个插槽的大小为 23 字节，修改块中的行时会使用这些插槽。块头按从上向下的方向扩展。 2\. 行数据：这是块中行的实际数据。行数据空间按从下向上的方向扩展。 3\. 空闲空间：空闲空间位于块的中间。这样可以根据需要扩展头和行数据空间。插入了一些新行之后，或者使用较大值更新了现有行的某些列之后，行数据就会占用空闲空间。如果行目录需要更多的行条目，或者需要的事务处理插槽数多于最初配置的数目，就会出现头扩展。最初，块中的空闲空间是相邻的。但是，删除和更新操作可能会导致在块空闲空间中产生碎片。必要时 Oracle 服务器会接合块中的空闲空间。

Extents

Extents 区--Data file

Segments

Segments 段

Tablespaces

**Tablespaces**

**Oracle 数据库在逻辑上将数据存储在表空间中，在物理上将数据存储在数据文件中。** 1.~ 表空间： - 只能属于一个数据库 - 包括一个或多个数据文件 - 可进一步划分为逻辑存储单元 - 是存储方案对象数据的资料档案库 2.~ 数据文件： - 只能属于一个表空间和一个数据库 - 是构成表空间的基础文件

数据库、表空间和数据文件彼此密切相关，但也存在不可忽视的差异： 1\. 数据库包括一个或多个称为表空间的逻辑存储单元，这些表空间可以作为一个整体存储数据库的所有数据。 2\. Oracle 数据库中的每个表空间都包括一个或多个称为数据文件的文件，这些数据文件具有与运行 Oracle 软件的操作系统一致的物理结构。 3.~ 数据库的数据作为一个整体存储在构成数据库各个表空间的数据文件中。例如，最简单的 Oracle 数据库包括两个表空间（ SYSTEM 和 SYSAUX 表空间，这两个表空间是必须存在的），每个表空间包含一个数据文件。另一个数据库可能包括三个表空间，每个表空间包含两个数据文件（共有六个数据文件）。一个数据库最多可以包含 65,534 个数据文件。

**预配置数据库中的表空间** 1\. SYSTEM ： Oracle 服务器使用 SYSTEM 表空间管理数据库。包含在这个表空间中的数据字典和表包含关于数据库的管理信息。以上信息均包含在 SYS 方案中，只有 SYS 用户或者拥有所需权限的其它管理用户才能访问这些信息。 2\. ：这是 SYSTEM 表空间的辅助表空间。在 Oracle 数据库早期版本中使用 SYSTEM 表空间或自己表空间的某些组件和产品，现在改为使用 SYSAUX 表空间。每个 Oracle Database 10g 或更高版本都必须拥有 SYSAUX 表空间。 3.TEMP ：如果执行的 SQL 语句需要创建临时段（如大规模排序或创建索引），则可以使用临时表空间。如同为每个用户分配默认的表空间以存储创建的数据对象一样，还会向每个用户分配临时表空间。除非另行指定，否则最佳方案是为数据库定义一个默认临时表空间，会为所有新建用户分配此表空间。在预配置数据库中， TEMP 表空间被指定为默认临时表空间。这意味着，如果在创建用户帐户时未指定临时表空间， Oracle 数据库会将此表空间分配给用户作为临时表空间。 4\. ：这是数据库服务器用于存储还原信息的还原表空间。如果数据库使用 &quot; 自动还原管理 &quot; ，那么数据库在任何指定时间必须只具有一个活动的还原表空间。此表空间是在创建数据库时创建的。 5\. ：此表空间用于存储永久用户对象和数据。在预配置数据库中， USERS 表空间是用于存储非系统用户创建的所有对象的默认表空间。对于 SYS 和 SYSTEM 用户（系统用户），默认的永久表空间依然是 SYSTEM 。 6\. ：此表空间包含创建数据库时可以安装的示例方案。这些示例方案为各种示例提供了一个通用平台。 Oracle 文档和课件中包含了建立在这些示例方案基础上的示例。 注：为简化管理，通常只对索引使用表空间。

**----------------------------------------------------------------** **使用** **sqlplus** **管理表空间：** **1.** **使用** **sqlplus** **创建表空间** CREATEDATAFILE /opt/oracle10g/oradata/oracle0/wood_tablespaces.dbf&amp;apos;~SIZE~ _100M_ ~~~~~~~~~~~~~~~~~~~~~~~ 指定数据文件大小 AUTOEXTEND~~~~~~~~~~~~ 指定数据文件自动扩展大小 NEXT~ _10M_ **~~~~~~~~~~~~~~~~~~~~~~~~~** # 指定数据文件每次扩展 10M MAXSIZE~~~~~~~~ 指定数据文容量最大值：无限制 / 或者指定大小 LOGGING~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~# 创建表空间同时生成重做日志 / 或者使用 NOLOGGING: 创建表空间时 , 不创建重做日志 . EXTENT MANAGEMENT LOCAL~~~~~~~~~~~~~~~~~~~~~~~~~~~# 指定区管理方式：本地管理 SEGMENT~~~~~~~~~~AUTO~~# 指定段的管理方式为 AUTO ； 或者使用 MANUALAUTO:~~~~~ 只能使用在本地管理的表空间中 . 使用 LOCAL 管理表空间时 , 数据块中的空闲空间增加或减少后，其新状态都会在位图中反映出来。 ~~~~~~~~~~~~~~~ 位图使 Oracle 管理空闲空间的行为更加自动化，并为管理空闲空间提供了更好的性 , 但对含有 LOB 字段的表不能自动管理 .MANUAL: 目前已不用 , 主要是为向后兼容 .

**2.** **查看表空间** select tablespace_name,status, file_name,bytes from dba_tablespaces; # 查看表空间的名称 , 状态 , 数据文件 , 大小 **3.** **修改表空间** 3.1\. 修改状态 表空间的状态属性主要有在线（ ONLINE ）、离线（ OFFLINE ）、只读（ READ ONLY ）和读写（ READ WRITE ） 4 种。 通过设置表空间的状态属性，可以对表空间的使用进行管理。 表空间数据文件属于表空间，一个表空间可有多个数据文件；数据文件有在线（ ONLINE ）、离线（ OFFLINE ）、 OFFLINE DROP 三种状态 alter tablespace~ _tablespacename_ OFFLINE;alter tablespace~ _tablespacename_ ONLINE;alter tablespace~ _tablespacename_ READ ONLY;alter tablespace _~tablespacename~_ READ WRITE;3.2\. 修改表空间数据文件的大小，添加数据文件 alter database DATAFILE &amp;apos;d:\ _tablespacename_ .dbf&amp;apos; resize 10m;~~~~~// 测试成功 alter tablespace SIMPLE add datafile &amp;apos;d:\ _tablespacename_ .dbf&amp;apos; size 5m; // 测试成功 4\. 修改表空间数据文件的自动扩展性 alter database datafile &amp;apos;d:\table.dbf&amp;apos; AUTOEXTEND ON next 5m maxsize 50m;5\. 移动表空间数据文件 1). 设置数据文件状态为离线 OFFLINE:~~~~~alter tablespace wood_tablespaces OFFLINE;2). 复制数据文件 3). 重新指定表空间路径 ~~~alter tablespace wood_tablespaces RENAME DATAFILE &amp;apos;E:\table2.dbf&amp;apos; to &amp;apos;D:\table.dbf&amp;apos;;4). 重新设置数据文件状态为离线

**4.** **删除** **tablespaces** 4.1\. 首先看一下是不是已经使用了 OMF~~~~~sql&gt; show parameter db_create_file_dest~~~~~~~~~~# 查看参数 db_create_file_dest, 如果已经设置则 :~~~~~sql&gt; drop tablespace~ _tablespacename_ ~~~~~~~~~~~~ 可以直接删除表空间， OMF 会删除相应的数据文件 4.2\. 如果没使用 OMF, 则： ~~~~~sql&gt; drop tablespace _tablespacename_ including contents and datafiles

**5.** **设置默认表空间** ~~1 ） . 查询 ( 当前用户 ) 默认表空间 select default_tablespace from user_users;~~2 ） . 修改默认表空间 alter database default tablespace _tablespacename_ ;~~~~~~~~ 如果是 oracle 9i 本语句不支持，但能让用户指向某表空间，具体做法如下： ~~~~~~~~ 例： alter user scott default tablespace users; (scott 为用户名 ,users 为表空间 )~~ 有时会出现表空间有存在的情况，这时一般都是以下几个原因造成的： ~~ 写错表空间名，我想的话这种机率较小。 ~~ 回想一下，你在创建表空间时是否给表空间表加了双引号如： ~~~~~~TABLESPACE &quot; _tablespacename_ &quot; ………………~~~~~~~~ 如果是这样的话，你在修改默认表空间，写表空间名字的时候就要区分大小了，这个是非常重要的，并且还要加上双引号， ~~~~~~~~ 如： alter user scott default tablespace &quot;Sample&quot;;(scott 为用户名 ) 。 ~~

---------------------------------------------------------------------

1 、查看表空间的名称及大小 ~~select t.tablespace_name, round(sum(bytes/(1024*1024)),0) ts_size~~from dba_tablespaces t, dba_data_files d~~where t.tablespace_name = d.tablespace_name~~group by t.tablespace_name;2 、查看表空间物理文件的名称及大小 ~~select tablespace_name, file_id, file_name,~~round(bytes/(1024*1024),0) total_space~~from dba_data_files ~~order by tablespace_name;

3 、查看表空间的使用情况 ~~select sum(bytes)/(1024*1024) as free_space,tablespace_name~~~from dba_free_space~~group by tablespace_name;

**----------------------------------------------------------------** **使用** **EM** **管理表空间** **1.** **使创建新表空间** :

**1.1.Extent Management** **（区管理）** ~~~~~-Locally Managed （本地管理表空间中的区） ~~~~~-Dictionary Managed （字典管理） **表空间中的空间管理** 在表空间中以区为单位分配空间。创建表空间后可使用以下方法之一来跟踪空闲空间和已用空间： a.~ 本地管理的表空间：在表空间中通过位图管理区。位图中的每一位对应一个块或一组块。分配区或为重新利用空间而释放区后， Oracle 服务器通过更改位图值来显示块的新状态。 - 在表空间中管理空闲区。 - 使用位图记录空闲区。 - 每一位对应于一个块或一组块。 - 位值指示空闲区或占用区。 - 建议使用本地管理的表空间。 b.~ 字典管理的表空间：由数据字典管理区。每当分配或取消分配区后， Oracle 服务器会更新数据字典中的相应表。这是为了实现向后兼容；建议使用本地管理的表空间。 - 由数据字典管理空闲区。 - 分配或取消分配区后会更新相应的表。 - 支持这些表空间仅仅是为了向后兼容。 **优缺点：** **在字典中管理** **(DICTIONARY):** 将数据文件中的每一个存储单元做为一条记录 , 所以在做 DM 操作时 , 就会产生大量的对这个管理表的 Delete 和 Update 操作 . 做大量数据管理时 , 将会产生很多的 DM 操作 , 严重的影响性能 , 同时 , 长时间对表数据的操作 , 会产生很多的磁盘碎片 , 这就是为什么要做磁盘整理的原因 . **本地管理** **(LOCAL):** 用二进制的方式管理磁盘 , 有很高的效率 , 同进能最大限度的使用磁盘 . 同时能够自动跟踪记录临近空闲空间的情况，避免进行空闲区的合并操作。 **与字典管理表空间相比，本地管理表空间具有以下优势：** 1.~ 如果使用本地管理，则不需要执行递归空间管理操作。采用字典管理表空间时，如果使用或释放区中的空间导致另一个操作使用或释放还原段或数据字典表中的空间，就会发生这种情况。 2.~ 由于采用本地管理表空间时并不记录数据字典表中的空闲空间，因此可以减少对这些表的争用。 3.~ 在本地管理区，可自动跟踪相邻的空闲空间而无需合并空闲区。 4.~ 系统会自动确定本地管理区的大小。 5.~ 对区位图进行更改不会生成还原信息，因为这些更改并不更新数据字典中的表（表空间限额信息等特殊情况除外）。 **1.2.Type** **（表空间类型）** ~~~~~~~-Permanent 用户表空间 : 用于存放用户数据表空间，可以直接写成 : CREATE TABLESPACE &quot;SAMPLE&quot;......~~~~~~-Temporary~ 临时表空间 , 用于临时数据的存放 ; 创建临时表空间的语法 :CREATE TEMPORARY TABLESPACE &quot;SAMPLE&quot;......~~~~~~~-Undo 还原表空间 . 用于存入重做日志文件 .~ 创建还原表空间的语法 :CREATE UNDO TABLESPACE &quot;SAMPLE&quot;...... **1.3.&quot;Status** **（状态）** **&quot;** - **Read Write** **（读写）：** 选择 &quot; 读写 &quot; 状态表示创建表空间后，用户可以对表空间进行读写操作。这是默认设置。 - **Read Only （只读）：** 指定 &quot; 只读 &quot; 可使表空间转换到只读模式。 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 此状态下，可以完成（提交或回退）现有的事务处理，但是，不允许对表空间中的对象进一步执行数据操纵语言 (DML) 操作。 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 表空间已联机，但处于只读状态。不能使 SYSTEM 或 SYSAUX 表空间处于只读模式。 **-Offline~~~~~~** **（脱机）：** 可以让联机的表空间脱机，使数据库的这部分暂时不可用于一般用途。 数据库的余下部分是开放的，用户可以访问其中的数据。表空间脱机时，可以使用以下选项： ~~~~~~~- Normal~~~ （正常）：如果表空间中的所有数据文件都不存在错误状态，则通过正常方式便可使表空间脱机。 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 当 Oracle 数据库使表空间脱机时，通过对表空间中的所有数据文件设置检查点，可以确保将所有数据写入磁盘。 ~~~~~~- （临时）：如果表空间中的一个或多个文件存在错误状态，也可以使表空间暂时脱机。 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 当 Oracle 数据库使（尚未脱机的）数据文件脱机时，会对这些数据文件设置检查点。 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 如果没有任何文件脱机，但是您使用了临时子句，则使表空间重新联机时便不需要执行介质恢复。 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 但是，如果因写错误而导致表空间的一个或多个文件脱机，而此时您让表空间临时脱机，那么表空间需要执行恢复后才能重新联机。 ~~~~~~- （立即）：表空间可以立即脱机， Oracle 数据库不需要对任何数据文件设置检查点。 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 如果指定了 &quot;Immediate （立即） &quot; ，则必须先对表空间执行介质恢复，才能使表空间联机。 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 如果数据库在 NOARCHIVELOG 模式下运行，则无法立即使表空间脱机。 ~~~~~~-Recover （进行恢复）： FOR RECOVER 设置已被淘汰。支持此语法的目的是为了实现向后兼容。

**2.Add Datafiles** **（添加数据文件）**

**3.Storage** **存储管理：** **3.1** **本地管理表空间中的区：** 3.1.1\. （自动）：又称为自动分配，这种方式指定表空间中的区大小由系统管理。您不能指定区大小。不能为临时表空间指定 &quot;Automatic （自动） &quot; 。 3.1.2~Uniform （统一）：这种方式指定使用指定的统一区大小管理表空间。默认大小为 1 MB 。临时表空间的所有区都是统一的，而且采用默认值。 ~~~~~~~~ 不能为还原表空间指定 &quot;Uniform （统一） &quot; **3.2.** **本地管理表空间中的段：** 3.2.1 （自动）： Oracle 数据库使用位图管理段中的空闲空间。位图描述了段中各个数据块的状态，该状态与插入行可使用的块中的空间量有关。随着数据块中可用空间的增多或减少，位图会反映数据块的新状态。通过使用位图， Oracle 数据库可以更自动地管理空闲空间，因此，这种空间管理方式被称为 &quot; 自动段空间管理 (ASSM)&quot; 。 3.2.2 Manual （手动）：此方式指定使用空闲列表来管理段中的空闲空间。空闲列表是由一些数据块组成的列表，插入行可使用这些数据块中的空间。由于这种管理段空间的方式需要为在表空间中创建的方案对象指定并优化 PCTUSED 、 FREELISTS 和 FREELIST GROUPS 存储参数，因此这种方式称为 &quot; 手动段空间管理 &quot; 。支持此方式是为了实现向后兼容；建议使用 ASSM 。 **3.3** **事件记录** 对表空间对象所做的更改会写入重做日志。如果未启用事件记录，则使用 SQL*Loader 的任何直接加载和直接加载 INSERT 操作都不会写入到重做日志中，因此如果丢失了数据，对象是不可恢复的。因此，如果在未启用事件记录的情况下创建了对象，就必须备份这些对象，以便可进行恢复。 有关事件记录子句的详细信息，请参阅《 Oracle 数据库参考手册》。 **3.4** **块信息** 这个区域显示所创建表空间使用的块大小。此处显示值为只读值。如果设置了任一其它块大小初始化参数 (DB_nK_CACHE_SIZE) ，则会在此处列出其它值作为选项。有关定义其它块大小的详细信息，请参阅《 Oracle 数据库管理员指南》。

**4.** **修改表空间** **(edit)** **：** 1\. 重命名：输入表空间的新名称 2\. 更改状态：表空间有三种不同的状态 ReadWrite,Read Only,Offline3\. 更改大小：通过向表空间添加数据文件，或者更改现有数据文件的大小 4\. 存储选项：单击 &quot;Storage （存储） &quot; 可更改表空间的事件记录行为。 5.Thresholds （阈值）：可在表空间中空间使用情况到达警告或严重级别时进行更改。共有三个选项： - Use Database Default Thresholds （使用数据库默认阈值）：使用预设的默认值，可以选择设置这些默认值。 - Specify Thresholds （指定阈值）：可以设置此特定表空间的阈值。 - Disable Thresholds （禁用阈值）：关闭这个表空间的空间使用预警。 注：注册阈值预警需要花费数分钟的时间。

**5.** **删除表空间（** **delete** **）** 如果不再需要表空间及其内容（表空间中包含的段），可将其从数据库中删除。必须具有 DROP TABLESPACE 系统权限才能删除表空间。 删除表空间时，会删除关联数据库控制文件中的文件指针。而且，如果正在使用 OMF ，则还会删除基础操作系统文件。如果没有使用 OMF ，则可以根据需要指示 Oracle 服务器删除构成已删除表空间的操作统文件（数据文件）。如果不指示 Oracle 服务器在删除表空间的同时删除数据文件，那么，如果以后要删除这些文件，则必须使用操作系统的相应命令。 不能删除包含任何活动段的表空间。例如，如果表空间中的某个表当前正在使用中，或者该表空间包含回退未提交事务处理所需要的还原数据，则不能删除该表空间。表空间的状态可以是联机也可以是脱机，但是在删除表空间之前，最好使其处于脱机状态。

**6.** **查看表空间信息** 表空间信息： - DBA_TABLESPACES- V$TABLESPACE 数据文件信息： - DBA_DATA_FILES- V$DATAFILE 临时文件信息： - DBA_TEMP_FILES- V$TEMPFILE

**7.** **查看表空间内容** **Administration &gt; Tablespaces &gt; Show Tablespace** **Contents &gt; Actions &gt; Show Tablespace Contents**

**8.** **对表空间执行的操作** **:** 1\. Add Datafile （添加数据文件）：将数据文件添加到表空间，这会增大表空间。 2.Like （类似创建）：将表空间用作模板，创建另一个表空间。 3.DDL （生成 DDL ）：生成用于创建表空间的数据定义语言 (DDL) 语句。此语句随后会复制并粘贴到文本文件中，以用作脚本或文档。 4.Locally Managed （设置为本地管理）：如果表空间当前是字典管理表空间，则可将该表空间转换为本地管理表空间。这种转换只能单向进行；不能将该表空间重新转换到字典管理表空间。 5.Readonly （只读模式）：停止对表空间的所有写操作。允许完成当前的事务处理，但是，不允许对表空间启动新 DML 活动或其它写活动。只有表空间的当前状态不是只读时，才显示此选项。 6.Writable （可写模式）：允许对表空间中的对象启动 DML 活动和其它写活动。只有表空间的当前状态不是可写时，才显示此选项。 7.Place Online （联机）：使当前脱机的表空间联机。 8\. （重组）：启动 &quot;Reorganization Wizard （重组向导） &quot; ，使用此向导可在表空间中各处移动对象以回收不可用的空间。应当在表空间中的对象使用不很是频繁的期间执行此任务。 9.Segment Advisor （运行段指导）：启动 &quot;Segment Advisor （段指导） &quot; ，使用该指导可根据对象中空间碎片的级别来确定对象是否拥有可用于执行回收的空间。在表空间级别，会针对表空间中的每个段生成建议。 10.Dependencies （显示相关性）：显示此表空间依赖的对象，或依赖于此表空间的对象。 11.Tablespace Contents （显示表空间内容）：显示关于表空间中所有段的信息，包括所有区的图形。 12.Offline （脱机）：使当前联机的表空间不可用。此时不会删除表空间，只是表空间不可用。

**扩大数据库** 可以按以下方式扩大数据库： 1.~ 创建新表空间 2.~ 将数据文件添加到现有表空间 3.~ 增加数据文件的大小 4.~ 动态扩展数据文件

表空间和数据文件 一个数据库可划分为多个逻辑存储单元，这些单元称为表空间，表空间可用于对相关逻辑结构进行分组。 一个表空间中包括一个或多个数据文件。 一个数据文件仅属于一个表空间。 一个数据库按逻辑都分为一个或多个表空间。 每个表空间显式创建一个或多个数据文件，这样可在表空间中按物理方式存储所有逻辑结构的数据。

SYSTEM 和 SYSAUX 表空间是必需存在的表空间。 这些表空间是在创建数据库时创建的。 这些表空间必须是联机的。表空间的状态可以是联机的（可访问）也可以是脱机的（不可访问）。 SYSTEM 表空间用于核心功能（例如，数据字典表）。 辅助的 SYSAUX 表空间用于附加的数据库组件 .

~-------------

[关于](http://www.cnblogs.com/jonescheng/archive/2008/05/08/1189063.html) **redo log 和** **undo log 这两个概念**

redo log~ 重做日志 undo log~ 撤消日志 重做日志：每当有操作执行前，将数据真正更改时，先前相关操作写入重做日志。这样当断电，或者一些意外，导致后续任务无法完成时，系统恢复后，可以继续完成这些更改 撤消日志：当一些更改在执行一半时，发生意外，而无法完成，则可以根据撤消日志恢复到更改之前的壮态 网上找到一些解说：以便以后自己参考 有两个概念：前滚与回退 比如某一时刻数据库 DOWN 机了，有两个事务，一个事务已经提交，另一个事务正在处理 数据库重启的时候就要根据日志进行前滚及回退，把已提交事务的更改写到数据文件，未提交事务的更改恢复到事务开始前的状态。

redo--&gt; undo--&gt;datafileinsert 一条记录时 , 表跟 undo 的信息都会放进 redo 中 , 在 commit 或之前 , redo 的信息会放进硬盘上 . 故障时 , redo 便可恢复那些已经 commit 了的数据 .

redo-&gt; 每次操作都先记录到 redo 日志中，当出现实例故障（像断电），导致数据未能更新到数据文件，则数据库重启时须 redo ，重新把数据更新到数据文件 undo-&gt; 记录更改前的一份 copy ，但你系统 rollback 时，把这份 copy 重新覆盖到原来的数据

redo-&gt; 记录所有操作，用于恢复（ redo records all the database transaction used for recovery ） undo-&gt; 记录所有的前印象，用于回滚（ undo is used to store uncommited data infor used for rollback ）

redo-&gt; 已递交的事务 , 实例恢复时要写到数据文件去的 undo-&gt; 未递交的事务 .

redo 的原因是：每次 commit 时，将数据的修改立即写到 online redo 中，但是并不一定同时将该数据的修改写到数据文件中。因为该数据已经提交，但是只存在联机日志文件中，所以在恢复时需要将数据从联机日志文件中找出来，重新应用一下，使已经更改数据在数据文件中也改过来！

undo 的原因是：在 oracle 正常运行时，为了提高效率，加入用户还没有 commit, 但是空闲内存不多时，会由 DBWR 进程将脏块写入到数据文件中，以便腾出宝贵的内存供其它进程使用。这就是需要 UNDO 的原因。因为还没有发出 commit 语句，但是 oracle 的 dbwr 进程已经将没有提交的数据写到数据文件中去了。

undo 也是也是 datafile ， 可能 dirty buffer 没有写回到磁盘里面去。 只有先 redo apply 成功了，才能保证 undo datafile 里面的东西都是正确的，然后才能 rollback

做 undo 的目的是使系统恢复到系统崩溃前 ( 关机前 ) 的状态 , 再进行 redo 是保证系统的一致性 . 不做 undo, 系统就不会知道之前的状态 ,redo 就无从谈起

所以 instance crash recovery 的时候总是先 rollforward ， 再 rollback

undo 回退段中的数据是以 &quot; 回退条目 &quot; 方式存储。 回退条目 = 块信息 ( 在事务中发生改动的块的编号 )+ 在事务提交前存储在块中的数据

在每一个回退段中 oracle 都为其维护一张 &quot; 事务表 &quot; 在事务表中记录着与该回退段中所有回退条目相关的事务编号（事务 SCN&amp; 回退条目）

redo 重做记录由一组 &quot; 变更向量 &quot; 组成。 每个变更变量中记录了事务对数据库中某个块所做的修改。 当用户提交一条 commit 语句时， LGWR 进程会立刻将一条提交记录写入到重做日志文件中，然后再开始写入与该事务相关的重做信息。

# 事务提交成功后， Oracle 将为该事备生成一个系统变更码（ SCN ）。事务的 SCN 将同时记录在它的提交记录和重做记录中。

commit 提交事务前完成的工作： · 在 SGA 区的回退缓存中生成该事务的回退条目。在回退条目中保存有该事务所修改的数据的原始版本。 · 在 SGA 区的重做日志缓存中生成该事务的重做记录。重做记录中记载了该事务对数据块所进行的修改，并且还记载了对回退段中的数据块所进行的修改。缓存中的重做记录有可能在事务提交之前就写入硬盘中。 · 在 SGA 区的数据库缓丰中记录了事务对数据库所进行的修改。这些修改也有可能在事务提交之前就写入硬盘中。

提交事务时完成的工作： · 在为该事务指定的回退段中的内部事务表内记录下这个事务已经被提交，并且生成一个惟一的 SCN 记录在内部事务表中，用于惟一标识这个事务。 ·LGWR 后进进程将 SGA 区重做日志缓存中的重做记录写入联机重做日志文件。在写入重做日志的同时还将写入该事务的 SCN 。 ·Oracle 服务进程释放事务所使用的所有记录锁与表锁。 ·Oracle 通知用户事务提交完成。 ·Oracle 将该事务标记为已完成。

rollback 回退事务完成的工作： ·Oracle 通过使用回退段中的回退条目，撤销事务中所有 SQL 语句对数据库所做的修改。 ·Oracle 服务进程释放事务所使用的所有锁 ·Oracle 通知事务回退成功。 ·Oracle 将该事务标记为已完成

举个例子： insert into a(id) values(1);(redo) 这条记录是需要回滚的。 回滚的语句是 delete from a where id = 1;(undo)

试想想看。如果没有做 insert into a(id) values(1);(redo) 那么 delete from a where id = 1;(undo) 这句话就没有意义了。

现在看下正确的恢复 : 先 insert into a(id) values(1);(redo) 然后 delete from a where id = 1;(undo) 系统就回到了原先的状态，没有这条记录了。

--------------------

~ 在这里会介绍 UNDO ， REDO 是如何产生的，对 TRANSACTIONS 的影响，以及他们之间如何协同工作的。 什么是 REDO~~~~~REDO 记录 transaction logs ，分为 online 和 archived 。以恢复为目的。 ~~~~~ 比如，机器停电，那么在重起之后需要 online redo logs 去恢复系统到失败点。 ~~~~~ 比如，磁盘坏了，需要用 archived redo logs 和 online redo logs 区恢复数据。 ~~~~~ 比如， truncate 一个表或其他的操作，想恢复到之前的状态，同样也需要。 什么是 UNDO~~~~~REDO 是为了重新实现你的操作，而 UNDO 相反，是为了撤销你做的操作，比如你得一个 TRANSACTION 执行失败了或你自己后悔了，则需要用 ROLLBACK 命令回退到操作之前。回滚是在逻辑层面实现而不是物理层面，因为在一个多用户系统中，数据结构， blocks 等都在时时变化，比如我们 INSERT 一个数据，表的空间不够，扩展了一个新的 EXTENT ，我们的数据保存在这新的 EXTENT 里，其它用户随后也在这 EXTENT 里插入了数据，而此时我想 ROLLBACK ，那么显然物理上讲这 EXTENT 撤销是不可能的，因为这么做会影响其他用户的操作。所以， ROLLBACK 是逻辑上回滚，比如对 INSERT 来说，那么 ROLLBACK 就是 DELETE 了。 COMMIT~~~~~ 以前，常想当然地认为，一个大的 TRANSACTION （比如大批量地 INSERT 数据）的 COMMIT 会花费时间比短的 TRANSACTION 长。而事实上是没有什么区别的，因为 ORACLE 在 COMMIT 之前已经把该写的东西写到 DISK 中了，我们 COMMIT 只是 1 ，产生一个 SCN 给我们 TRANSACTION ， SCN 简单理解就是给 TRANSACTION 排队，以便恢复和保持一致性。 2 ， REDO 写 REDO 到 DISK 中（ LGWR ，这就是 log file sync ），记录 SCN 在 ONLINE REDO LOG ，当这一步发生时，我们可以说事实上已经提交了，这个 TRANSACTION 已经结束（在 V$TRANSACTION 里消失了） 3 ， SESSION 所拥有的 LOCK （ V$LOCK ）被释放。 4 ， Block Cleanout （这个问题是产生 ORA-01555: snapshot too old 的根本原因） ROLLBACK~~~~~ROLLBACK 和 COMMIT 正好相反， ROLLBACK 的时间和 TRANSACTION 的大小有直接关系。因为 ROLLBACK 必须物理上恢复数据。 COMMIT 之所以快，是因为 ORACLE 在 COMMIT 之前已经作了很多工作（产生 UNDO ，修改 BLOCK ， REDO ， LATCH 分配）， ROLLBACK 慢也是基于相同的原因。 ~~~~~ROLLBACK 会 ~~~~~1 ，恢复数据， DELETE 的就重新 INSERT ， INSERT 的就重新 DELETE ， UPDATE 的就再 UPDATE 。 ~~~~~2 ， RELEASE LOCK~~~~~~ROLLBACK 要比 COMMIT 消耗更多资源，因为 ORACLE 认为你一旦做数据更新，那么就意味着你要 COMMIT （其他数据库不全是这种设计理念，比如 DB2 ），所以在你更新数据的时候就做了大量的工作，这也可以理解为什么不建议用 TABLE 来做 TEMPORARY TABLE 。（ TEMP TABLE 消耗的 REDO 比固定表在 INSERT 时要少很多 ~~~~ ， UPDATE 时差不多是 1/2 ，但是 DELETE 却相差无几）

OMF

**Oracle Managed Files (OMF)** 如果使用 Oracle Managed Files (OMF) ，则不需要直接管理构成 Oracle 数据库的操作系统文件。可以根据数据库对象而不是文件名指定操作。对于以下数据库结构，可以按需要在数据库内部使用标准文件系统接口来创建和删除文件： 1.~ 表空间 2.~ 重做日志文件 3.~ 控制文件 4.~ 归档日志 5.~ 块更改跟踪文件 7.~ 闪回日志 8\. 备份 数据库可以混合包含 Oracle Managed Files 和非 Oracle Managed Files 。由上面任一参数指定的文件系统目录必须已经存在：数据库不会创建文件系统目录。该目录还必须拥有允许数据库在其中创建文件的权限。

**参数说明** **:** DB_CREATE_FILE_DEST~~~~~~~~~~~~~~~~~~~~~ 定义数据文件和临时文件的默认文件系统目录的位置 DB_CREATE_ONLINE_LOG_DEST_n~~~ 定义重做日志文件和控制文件的创建位置 DB_RECOVERY_FILE_DEST~~~~~~~~~~~~~~~ 定义 RMAN 备份位置 示例： SQL&gt; ALTER SYSTEM SET DB_CREATE_FILE_DEST = &amp;apos;/u01/oradata&amp;apos;;SQL&gt; CREATE TABLESPACE tbs_1;

ASM

Automatic Storage Management

ASM ：概念 使用 ASM 并不意味着不再需要任何预先存在的数据库功能。现有数据库可以按原有方式 运行。可以将新文件创建为 ASM 文件，并按原有方式管理现有文件，也可以最后将这些 文件移植到 ASM 。 上图描述了使用 ASM 的 Oracle 数据库中不同存储组件之间的关系。图的左侧和中间部分 显示了以前版本中存在的关系。图右侧为 ASM 引入的新概念。 数据库文件可作为 ASM 文件进行存储。新层次的顶端为 ASM 磁盘组。任何单个 ASM 文件只能包含在一个磁盘组中。但是，一个磁盘组可能包含属于多个数据库的文件，一个 数据库可能使用多个磁盘组中的存储空间。如图所示，一个磁盘组由多个 ASM 磁盘构成， 而每个 ASM 磁盘只属于一个磁盘组。 ASM 文件始终分布在磁盘组中的所有 ASM 磁盘上。 ASM 磁盘按分配单位 (AU) 进行分区，每个分配单位为一兆字节。分配单位是 ASM 分配 的最小连续磁盘空间。 ASM 不允许在多个分配单位之间分割一个 Oracle 块。 注：此图只涉及了一类 ASM 文件：数据文件。实际上， ASM 还可用来存储其它数据库 文件类型。

ASM ：主要功能和优点 ASM 将文件划分为区（不同于前面介绍的数据文件区），而且将每个文件的区均匀地分 布在所有磁盘上。 ASM 使用索引技术跟踪每个区的位置。存储容量发生变化时， ASM 并 不重新条带化所有数据，而是根据添加或删除的存储量，按比例移动一定数量的数据，通 过重新平均分配文件来保持磁盘间的负载平衡。此操作是在数据库处于活动状态时完成的。 可以提高重新平衡操作的速度以更快地完成操作，也可以降低此速度以减少对 I/O 子系统 的影响。 ASM 还提供了镜像保护，因此不需要购买第三方逻辑卷管理器。 ASM 的一个特 有优势是可为每个文件创建镜像，而不是为每个卷创建镜像。因此，同一磁盘组可以包含 镜像文件或非镜像文件的组合。 ASM 支持数据文件、日志文件、控制文件、归档日志、 Recovery Manager (RMAN) 备份 集及其它 Oracle 数据库文件类型。 ASM 还支持 RAC ，这样就不再需要使用集群逻辑卷 管理器或集群文件系统。

ASM ： 1\. 对文件而不是逻辑卷进行条带化 2.~ 提供联机重新配置磁盘和动态重新平衡功能 3.~ 可用来调整重新平衡速度 4.~ 为每个文件提供冗余 5\. 仅支持 Oracle 数据库文件 6.~ 可识别集群 7.~ 可自动安装

#### 6.Administering User {#6-administering-user}

Users

**part1.** **用户**

**预定义帐户：** **SYS 和** **SYSTEM** SYS 帐户： - 授予有 DBA 角色 - 具有 ADMIN OPTION 的所有权限 - 执行启动、关闭和某些维护命令时所必需的帐户 - 拥有数据字典 - 拥有自动工作量资料档案库 (AWR)SYSTEM 帐户授予有 DBA 角色。 这两个帐户不适用于例行程序操作。

默认情况下， SYS 和 SYSTEM 帐户授予有数据库管理员 (DBA) 角色。 此外， SYS 帐户还具有 ADMIN OPTION 的所有权限，而且拥有数据字典。要连接到 SYS 帐户，必须使用 AS SYSDBA 子句。任何授予了 SYSDBA 权限的用户均可使用 ASSYSDBA 子句连接到 SYS 帐户。只有授予了 SYSDBA 或 SYSOPER 权限的 &quot; 授权 &quot; 用户才 能启动或关闭数据库实例。 默认情况下， SYSTEM 帐户授予有 DBA 角色，但不具有 SYSDBA 权限。

**Oracle** **管理员账户登录认证方式：** 操作系统认证：要登录了数据库服务器上的操作系统用户，就能在 Oracle 中以 sys 身份登录。 即： sql&gt; connect / as sysdbasql&gt; connect / as sysoper 口令文件认证：必须以 Oracle 管理员账户登录 即： sql&gt; connect sys/oracel as sysdbasql&gt; connect sys/oracel as sysdba

%oracle_home%/network/admin/sqlnet.ora~~~ 以下参数项来控制登录方式 :SQLNET.AUTHENTICATION_SERVICES= (NTS|NONE|ALL)~~~~#win 平台启用 OS 认证

~

Authentication

**part2.** **验证用户** **2.1** **口令** 又称为 Oracle 数据库验证。创建的每个用户都有一个关联的口令，用户尝试建立连接时，必须提供这个口令。设置口令时，可以使该口令立即失效，这会强制用户在首次登录后更改口令。如果决定要使用户口令失效，应确保用户能够更改口令。有些应用程序不具备此功能。 在（客户机 / 服务器之间和服务器 / 服务器之间）建立网络连接期间，系统总是使用修改过的数据加密标准 (DES) 算法，以自动透明方式对口令进行加密，然后通过网络发送这些口令。 **2.2** **外部验证：** 又称为操作系统验证。用户可以在不指定用户名或口令的情况下连接到 Oracle 数据库。使用外部验证时，数据库依赖于基础操作系统或网络验证服务来限制对数据库帐户的访问。数据库口令不适用于此类登录。如果操作系统或网络服务允许，可以使用外部验证来验证用户。如果要执行此操作，请设置 OS_AUTHENT_PREFIX 初始化参数，并在 Oracle 用户名中使用此前缀。 OS_AUTHENT_PREFIX 参数定义了一个前缀， Oracle 数据库会在每个用户的操作系统帐户名之前添加此前缀。为了实现与 Oracle 软件早期版本的向后兼容，此参数的默认值为 OPS$ 。用户尝试建立连接时， Oracle 数据库会将带前缀的用户名与数据库中的 Oracle 用户名进行比较。 例如，假定对 OS_AUTHENT_PREFIX 进行了如下设置： OS_AUTHENT_PREFIX=OPS$ **2.3** **全局验证** 使用 Oracle Advanced Security 选项时，可通过全局验证（一种严格的验证方法）并使用生物统计学、 x509 证书、标记设备和 Oracle Internet Directory 来识别用户。

Privileges

**part3.Privileges**

**A.Privileges** **类型** 1\. 系统权限：每种系统权限都允许用户执行一种或一类特定的数据库操作。例如，创建表空间的权限就是一个系统权限。系统权限可由管理员授予，或者由可以显式授予管理权限的用户授予。共有一百多种不同的系统权限。很多系统权限都包含 ANY 子句。 1.1.SESSION ：使用此权限您可以登录，即使数据库是在受限模式下打开的。 1.2\. 和 SYSOPER ：使用这两个权限可以在数据库中执行关闭、启动、恢复及其它管理任务。使用 SYSOPER 用户可执行基本操作任务，但不能查看用户数据。此权 限包括以下系统权限： - STARTUP 和 SHUTDOWN- CREATE SPFILE- ALTER DATABASE OPEN/MOUNT/BACKUP- ALTER DATABASE ARCHIVELOG- ALTER DATABASE RECOVER （仅限完全恢复。任何形式的不完全恢复，如 UNTIL TIME|CHANGE|CANCEL|CONTROLFILE ，需要以 SYSDBA 身份建立连接。） - RESTRICTED SESSION 除此之外， SYSDBA 系统权限还可授权执行不完全恢复和删除数据库。使用 SYSDBA 系统权限用户可以 SYS 用户身份有效地建立连接。 1.3ANY 对象：使用 DROP ANY 权限用户可删除其他用户拥有的对象。 1.4 、 MANAGE 、 DROP 和 ALTER TABLESPACE ：这些权限可用来管理表空间，包括创建、删除和更改表空间的属性。 1.5ANY DIRECTORY ：使用 Oracle 数据库开发人员可以在 PL/SQL 内调用外部代码（例如 C 库）。作为一种安全措施，代码所在的操作系统目录必须链接到一个虚拟 Oracle 目录对象。使用 CREATE ANY DIRECTORY 权限，您可能会调用不安全的代码对象。 使用 CREATE ANY DIRECTORY 权限，用户可以在 Oracle 软件所有者能够访问的任何目录中创建目录对象（具有读写访问权限）。这意味着用户可以访问这些目录中的外部过程。用户可以尝试直接读写任何数据库文件，如数据文件、重做日志和审计日志。一定要确保组织已采用了安全策略，以防止误用此类作用很强的权限。 1.6.ANY OBJECT PRIVILEGE ：使用此权限，您可以授予对他人所拥有的对象的对象权限。 1.7.DATABASE 和 ALTER SYSTEM ：这些权限的作用很强，可用于修改数据库和 Oracle 实例，如重命名数据文件或刷新缓冲区高速缓存。

2\. 对象权限：对象权限允许用户对特定对象（如表、视图、序列、过程、函数或程序包）执行一个特定的操作。在没有特定权限的情况下，用户只能访问自己拥有的对象。对象权限可以由对象的所有者或管理员授予，也可以由为其显式授予了对象授予权限的用户授予。

**B.** **撤销权限** 1\. 撤销系统权限 使用 REVOKE SQL 语句可撤销使用 GRANT 命令直接授予的系统权限。具有 ADMIN OPTION 系统权限的用户可撤销其它任何数据库用户的权限。撤销者与最初被授予权限的用户不一定是同一个用户。 撤销系统权限时不会产生级联效应，无论是否指定了 ADMIN OPTION 。 2\. 撤销对象权限 撤销与数据操纵语言 (DML) 操作相关的系统权限时可能会出现级联效应。例如，如果将 SELECT ANY TABLE 权限授予给某个用户，该用户又创建了使用表的过程，则必须先 重新编译用户方案中包含的所有过程，才能再次使用这些过程。 使用 WITH GRANT OPTION 撤销对象权限也会产生级联效应。

-------------------------- **实例：** **sqlplus** **授与用户系统权限**

1\. 创建用户： user ，密码： passwordCREATE USER **_user_** IDENTIFIED BY **_password_** ;

1.1\. 授予用户 user,privilege 权限 GRANT _privilege_ TO _user_

1.2\. 授予角色 role,privilege 权限 GRANT _privilege_ TO _role_

1.3\. 授予所有用户 ,privilege 权限 GRANT _privilege_ TO _public~_

**SQL&gt; grant connect,resource** ~#connect,resource 角色一般情况下满足普通用户基本权限

sysdba: 系统可用权限保存在表： dba_sys_privs

2\. 修该用户（当期用户必须具有 ALTER USER 权限）

2.1 修改用户密码 ALTER USER userIDENTIFIED BY tiger;

2.2 锁定，解锁用户 alter user hr account unlock; alter user hr account lock;

**实例：** **sqlplus** **授与用户对象权限** 未授予 UU1 select hr.departmentsSQL&gt; select * from hr.departments;select * from hr.departments~~~~~~~~~~~~~~~~*ERROR at line 1:ORA-00942: table or view does not exist~~~~# 用户无 select 对象 hr.departments 的权限 授予 UU1 select hr.departmentsSQL&gt; grant select on hr.departments to uu1;

++++++++++++

Oracle 提供两种验证方式，一种是 OS 验证，另一种密码文件验证方式，如果是第一种方式用以下方法修改密码： sqlplus /nolog;connect / as sysdbaalter user sys identified by ;alter user system identified by ; 如果是第二种方法用以下方法修改密码： orapwd file=pwdxxx.ora password= 你设定的新密码 entries=10 设定完后，重新启动服务，再次登陆就可以了。

Roles

**part4.** **角色**

**A.** **角色特性** 1.~ 角色就像用户，可以将权限授予给角色，也可以撤销角色的权限。 2.~ 角色就像系统权限，可以将其授予给用户或其它角色，也可以撤销用户或其它角色的角色。 3.~ 角色可以由系统权限和对象权限组成。 4.~ 可以针对授予某一角色的每个用户启用或禁用该角色。 5.~ 可能需要口令才能启用角色。 6.~ 角色不由任何用户拥有，角色也不属于任何方案。

**B.** **预定义角色**

|  | CREATE SESSION~ |
| --- | --- |
|  | CREATE CLUSTER 、 CREATE INDEXTYPE 、 CREATE OPERATOR 、 CREATE PROCEDURE 、 CREATE SEQUENCE 、 CREATE TABLE 、 CREATETRIGGER 、 CREATE TYPE |
|  | ANY JOB 、 CREATE EXTERNAL JOB 、 CREATE JOB 、 EXECUTE ANY CLASS 、 EXECUTEANY PROGRAM 、 MANAGE SCHEDULER |
|  | ~ 具有大多数系统权限和其它若干个角色。不要将其授予给管理员之外的人员。 |
|  | ~ 没有系统权限，但是具有对数据字典的 HS_ADMIN_ROLE 权限和 1,700 多种对象权限 |

**C.** **保护角色** 默认情况下角色通常处于启用状态，这意味着如果角色已授予给某个用户，则该用户可以行使指定给角色的权限。此时用户可以： 1 使角色成为非默认角色。将角色授予给用户后，取消选中 &quot;DEFAULT （默认） &quot; 复选框。现在，用户必须先显式启用角色，之后才能行使角色的权限。 2 将角色设置为需要通过附加验证，之后才能使用该角色。默认情况下角色的验证为 &quot; 无 &quot; ，但可以将角色设置为需要先通过附加验证，然后才能进行设置。 3 只有通过成功地执行 PL/SQL 过程，才能创建安全的应用程序角色。 PL/SQL 过程可以检查用户运行程序的用户网络地址、日期时间或正确保护权限组所需的其它元素。

Profiles

**part5.Profiles and Users** **概要文件和用户**

概要文件是指一组已命名的针对数据库用法和实例资源的资源限制条件。通过概要文件还可管理帐户状态并设置对用户口令的限制（长度、失效时间等）。每个用户都分配有一个概要文件，而且该用户在指定时间只属于一个概要文件。如果用户在您更改用户概要文件时已经登录，那么所做更改在用户下一次登录后才生效。 默认概要文件可作为其它所有概要文件的基础。如幻灯片中所示，概要文件的限制可以是隐式指定的（如 &quot;CPU/ 会话 &quot; ）、无限制的（如 &quot;CPU/ 调用 &quot; ），或者引用默认概要文件中的任何设置（如 &quot; 连接时间 &quot; ）。 除非 RESOURCE_LIMIT 初始化参数设置为 TRUE ，否则不能通过概要文件强行设置对用户的资源限制。如果 RESOURCE_LIMIT 使用默认值 FALSE ，则忽略概要文件的限制。

管理员使用概要文件来控制以下系统资源： **1.** **：** 可以针对每个会话或每个调用限制 CPU 资源。 &quot;CPU/ 会话 &quot; 限制为 1,000 表示：当使用此概要文件的任一会话占用 10 秒以上的 CPU 时间（ CPU 时间限制以百分之一秒为单位）时，该会话就会收到错误并被注销： ORA-02392: exceeded session limit on CPU usage, you are beinglogged off 每个调用限制都具有相同的作用，但是它并不限制用户的整个会话，而是防止任一命令占用过多的 CPU 。如果 &quot;CPU/ 调用 &quot; 受到限制，而且用户超出了限制，命令就 会中止，用户还会收到错误消息，例如： ORA-02393: exceeded call limit on CPU usage

**2.** **网络** **/** **内存：** 每个数据库会话都会占用系统内存资源和网络资源（如果会话来自服务器的非本地用户）。可以指定以下参数： - 连接时间：指示用户在自动注销前可以保持连接的分钟数。 - 闲置时间：指示用户会话在自动注销前可以保持闲置的分钟数。只计算服务器进程的闲置时间，不考虑应用程序活动。 IDLE_TIME 限制不受长时间运行的查询和其它操作的影响。 - 并行会话：指示使用数据库用户帐户可以创建多少个并行会话。 - 专用 SGA ：限制系统全局区域 (SGA) 中排序、合并位图等操作占用的空间。 此限制仅在会话使用共享服务器时才有效（将在 &quot; 配置 Oracle Network 环境 &quot; 一课讨论共享服务器）

**3.** **磁盘** **I/O** **：** 限制用户在每个会话或每个调用级别可读取的数据量。 &quot; 读取 / 会话 &quot; 和 &quot; 读取 / 调用 &quot; 可限制内存和磁盘的总读取次数。这可确保执行大量输入 / 输出 (I/O) 的语句不会过度使用内存和磁盘。 概要文件还支持组合限制。组合限制以 &quot;CPU/ 会话 &quot; 、 &quot; 读取 / 会话 &quot; 、 &quot; 连接时间 &quot; 和 &quot; 专用 SGA&quot; 的加权组合为基础。《 Oracle 数据库安全性指南》中对组合限制进行了详细介绍。

------------------------------------------------ 实例：使用自定义的 profile

create profile 文件语法： CREATE PROFILE profile~~LIMIT {~resource_parameters 对资源的限制 ~~~~~~~~~~~~| password_parameters 对密码的限制 ~~~~~~~~}...;

**1.** **使用** **sqlplus** **创建用户** **profile CREATE** **PROFILE** &quot;MYPROFILE&quot; **LIMITCPU_PER_SESSION** **DEFAULT** **CPU_PER_CALL** **DEFAULT** **CONNECT_TIMEDEFAULT** **IDLE_TIME** **DEFAULT** **SESSIONS_PER_USERDEFAULT** **LOGICAL_READS_PER_SESSION** **DEFAULT** **LOGICAL_READS_PER_CALL** **DEFAULT** **PRIVATE_SGA** **DEFAULT** **COMPOSITE_LIMIT** **DEFAULT** **PASSWORD_LIFE_TIME** **DEFAULT** **PASSWORD_GRACE_TIME** **DEFAULT** **PASSWORD_REUSE_MAX** **DEFAULT** **PASSWORD_REUSE_TIME** **DEFAULT** **PASSWORD_LOCK_TIME** **DEFAULT** **FAILED_LOGIN_ATTEMPTS** **DEFAULT** **PASSWORD_VERIFY_FUNCTION** **DEFAULT**

**Unlimited** ：分配该 profile 的用户对资源使用无限制，当使用密码参数时， unlimited 意味着没有对参数加限制。 **Default** ：指定为 default 意味着忽略对 profile 中的一些资源限制， Default profile 初始定义对资源不限制

**2.** **启用资源限制：** SQL&gt; alter system set resource_limit=true; System altered.

**3.** **修改用户** **profile** **文件** SQL&gt; alter userprofile profilename; User altered.

**4.** **退出当前会话，重新建立会话，验证** **profile** **文件**

**5.** **修改** **profile** **为** **default** SQL&gt; alter userprofile default;User altered.

**6.** **删除自定义的** **profile** drop profile profilename cascade 如果使用 CASCADE 选项删除了概要文件（例如，在 SQL*Plus 中），则会向具有该概要文件的所有用户自动分配 DEFAULT 概要文件。

相关表： desc dbms_resource_manager # 查看资源管理器 select profile from dba_users where usename= &amp;apos;&amp;apos;;~~# 查看用户和资源限制的相关信息 dba_profiles # 查看用户和资源限制的相关信息

~

Password Security

**part6.Implementing Password Security Features** **实施口令安全功能**

**A.password profile option** Oracle 口令管理是通过用户概要文件实施的。概要文件可提供许多标准安全功能，其中包括： 1\. 帐户锁定：如果用户的登录失败次数达到了指定次数，系统会在设置的一段时间内自动锁定帐户。 1.1 参数指定了在锁定帐户前失败的尝试登录次数。 1.2 参数指定了尝试登录失败次数达到指定次数后帐户的锁定天数。 2\. 口令过期和失效：使用户口令具有生存期，口令在此生存期之后失效，必须进行更改。 2.1 参数指定了口令的生存期（以天为单位），此时间之后，口令会失效。 2.2 参数指定了口令失效后首次成功登录之后，更改口令的宽限期（以天为单位）。 3\. 口令历史记录：检查新口令以确保在指定时间内或者在指定口令更改次数内不重复使用口令。可使用下列参数之一进行检查： 3.1 ：指定用户不能在指定天数内重复使用口令 3.2 ：指定在可重复使用当前口令之前需要更改口令的次数（系统记录？） 这两个参数是互相排斥的，因此，如果其中一个参数值没有被设置 UNLIMITED （如果 DEFAULT 概要文件将值设置为 UNLIMITED ，则为 DEFAULT ），则另一个参数必须设置 为 UNLIMITED 。 4\. 口令复杂性验证：对口令进行复杂性检查可验证口令是否符合特定的规则。此检查必须确保口令是足够复杂的，这样才能防止入侵者通过猜测口令来尝试闯入系统。 PASSWORD_VERIFY_FUNCTION 参数指定一个 PL/SQL 函数，用于在分配口令之前执行口令复杂性检查。口令验证函数必须由 SYS 用户拥有，而且必须返回布尔值（ TRUE 或 FALSE ）。

**B.** **口令验证函数**

口令验证函数可强制设置以下口令限制： 1.~ 最小长度是四个字符。 2.~ 口令不能与用户名相同。 3.~ 口令必须至少包含一个字母、一个数字和一个特殊字符。 4.~ 口令与前一口令必须有三个字母不相同。 提示：使用此函数作为模板可创建自己的自定义口令验证。

Oracle 服务器提供了一个名为 VERIFY_FUNCTION 的口令复杂性验证函数。此函数是使用 &lt;oracle_home&gt;/rdbms/admin/utlpwdmg.sql 脚本创建的。必须在 SYS 方案中创建口令复杂性验证函数。可将此函数作为模板来创建自定义的口令验证。 除了创建 VERIFY_FUNCTION 以外，还可以在 utlpwdmg 脚本中使用以下 ALTER PROFILE 命令更改 DEFAULT 概要文件： ALTER PROFILE default LIMITPASSWORD_LIFE_TIME 60PASSWORD_GRACE_TIME 10PASSWORD_REUSE_TIME 1800PASSWORD_REUSE_MAX UNLIMITEDFAILED_LOGIN_ATTEMPTS 3PASSWORD_LOCK_TIME 1/1440PASSWORD_VERIFY_FUNCTION verify_function; 请注意，创建用户时，除非指定了其它概要文件，否则会向用户分配 DEFAULT 概要文件。

----------------------------------------------------

create profile 文件语法： CREATE PROFILE profile~~~~~~~~~~{~resource_parameters 对资源的限制 ~~~~~~~~~~~~| password_parameters 对密码的限制 ~~~~~~~~}...;

**实例：创建** **password profile**

**1.** **创建** **pw profile** create profile mypw_profile limitPASSWORD_LIFE_TIME 60PASSWORD_GRACE_TIME 10PASSWORD_REUSE_TIME 1800PASSWORD_REUSE_MAX UNLIMITEDFAILED_LOGIN_ATTEMPTS 3PASSWORD_LOCK_TIME 1/1440PASSWORD_VERIFY_FUNCTION verify_function;create profile mypw_profile limit*ERROR at line 1:ORA-07443: function VERIFY_FUNCTION not found~~~~~~# 系统未常见 VERIFY_FUNCTION 函数 SQL&gt; @$ORACLE_HOME/rdbms/admin/utlpwdmg.sql~~# 使用 utlpwdmg.sql 脚本创建 VERIFY_FUNCTION 函数 Function created.Profile altered.~~~~~~~~# 创建完该函数会对系统 default/ profile 有部分修改

**2.** **查看** **default profile** **变更情况** **(** **其变更值为，例子中所要修改的值** **)** select * from dba_profiles where profile=&amp;apos;&amp;apos;profile_name&quot;;

PROFILE~~~RESOURCE_NAME~~~~~~~~~~~~~~~~~~~RESOURCE LIMIT---------- -------------------------------- -------- -----------------DEFAULT~~~COMPOSITE_LIMIT~~~~~~~~~~~~~~~~~KERNEL~~UNLIMITEDDEFAULT~~~SESSIONS_PER_USER~~~~~~~~~~~~~~~KERNEL~~UNLIMITEDDEFAULT~~~CPU_PER_SESSION~~~~~~~~~~~~~~~~~KERNEL~~UNLIMITEDDEFAULT~~~CPU_PER_CALL~~~~~~~~~~~~~~~~~~~~KERNEL~~UNLIMITEDDEFAULT~~~LOGICAL_READS_PER_SESSION~~~~~~~KERNEL~~UNLIMITEDDEFAULT~~~LOGICAL_READS_PER_CALL~~~~~~~~~~KERNEL~~UNLIMITEDDEFAULT~~~IDLE_TIME~~~~~~~~~~~~~~~~~~~~~~~KERNEL~~UNLIMITEDDEFAULT~~~CONNECT_TIME~~~~~~~~~~~~~~~~~~~~KERNEL~~UNLIMITEDDEFAULT~~~PRIVATE_SGA~~~~~~~~~~~~~~~~~~~~~KERNEL~~UNLIMITEDDEFAULT~~~FAILED_LOGIN_ATTEMPTS~~~~~~~~~~~PASSWORD 3DEFAULT~~~PASSWORD_LIFE_TIME~~~~~~~~~~~~~~PASSWORD 60DEFAULT~~~PASSWORD_REUSE_TIME~~~~~~~~~~~~~PASSWORD 1800DEFAULT~~~PASSWORD_REUSE_MAX~~~~~~~~~~~~~~PASSWORD UNLIMITEDDEFAULT~~~PASSWORD_VERIFY_FUNCTION~~~~~~~~PASSWORD VERIFY_FUNCTIONDEFAULT~~~PASSWORD_LOCK_TIME~~~~~~~~~~~~~~PASSWORD .0006DEFAULT~~~PASSWORD_GRACE_TIME~~~~~~~~~~~~~PASSWORD 10

**3.** **修改用户** **profile** **文件** SQL&gt; alter userprofile profilename; User altered. 或者创建用户同时制定 profile 文件 SQL&gt; create user uu1 identified by uu1 profile new_profile;~~

**4.** **退出当前会话验证** ~

Quota

part7.Assigning Quota to Users 将限额分配给用户

**用户表空间：** 1\. 创建用户 create user uu1 idetified by uui;2\. 创建表空间 create tablespace &quot;mytabelesapce&quot; datafile &amp;apos;/opt/oracle10g/oradata/oracle0/mytablespace.dbf&amp;apos; size 100M autoextend on maxsize unlimited logging extent management local segment space management auto;3\. 修改用户默认表空间 alter user uu1 default tablespace &quot;mytablespace&quot;; 4\. 修改用户表空间配额 4.1.alter user user_name quota size on tablespace_name~~~~~~~~~~~~# 其中 size 可以是诸如 &amp;apos;32M&amp;apos; ， &amp;apos;2G&amp;apos; 。这都是针对某个特定的表空间进行配额修改。 4.2.alter user user_name quota unlimited on tablespace_name~~~~~#unlimitied 无限制使用指定表空间，这都是针对某个特定的表空间进行配额修改。 4.3.grant unlimited tablespace to user_name~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~# 这是全局性质的授权无限使用表空间。 可用配额选项： 1.~ **无限制：** 允许用户最大限度地使用表空间中的可用空间。 2.~ **值：** 用户可以使用的空间，以千字节或兆字节为单位。这并不保证会为用户保留该空间。因为此值可能大于或小于表空间中的当前可用空间。 3.~ **UNLIMITED TABLESPACE 系统权限：** 此系统权限会覆盖所有单个的表空间限额，并向用户提供所有表空间（包括 SYSTEM 和 SYSAUX ）的无限制限额。授予此权限 时要慎重。 注：请注意，授予 RESOURCE 角色时也会授予此权限。一定不要为用户提供 SYSTEM 或 SYSAUX 表空间限额。通常，只有 SYS 和 SYSTEM 用户才能在 SYSTEM 或 SYSAUX 表空间中创建对象。对于分配的临时表空间或任何还原表空间则不需要限额。

#### 7.Managing Schema {#7-managing-schema}

Schema

Part1.Schema

**A.** **方案** 是指由特定用户拥有的数据库对象的集合。对于生产数据库而言，这个用户代表的通常不是一个人，而是一个应用程序。方案与拥有该方案的用户同名。方案对象是指直接引用数据库数据的逻辑结构。方案对象包含表、视图和索引等多种结构。

创建数据库时，系统会创建多个方案。 以下是两个重要方案： 1\. 方案：包含数据字典，这在 &quot; 管理用户安全性 &quot; 一课有所介绍。 2\. 方案：包含存储管理信息的附加表和视图。 &quot; 管理用户安全性 &quot; 一课对此进行了介绍。 以下是几个示例方案： 1\. HR ： &quot; 人力资源 &quot; 方案是一个用于介绍基本主题的简单方案。此方案经过扩展后可支持 Oracle Internet Directory 演示。 2\. ： &quot; 订单录入 &quot; 方案是一个中等复杂程度的方案。 OE 方案中提供了多种数据类型。 OC （联机目录）子方案是内置于 OE 方案中的对象关系数据库对象的集合。 3\. PM ： &quot; 产品媒体 &quot; 方案专用于多媒体数据类型。 4\. ： &quot; 排队发运 &quot; 方案包含一组用于演示 Oracle Advanced Queuing 功能的方案。 5\. ： &quot; 销售历史记录 &quot; 方案用于演示大量数据。这个方案经过扩展可支持高级分析处理。

Schema Objects

命名数据库对象 1.~ 名称长度必须为 1 至 30 个字节，但以下情况除外： - 数据库名称为 8 个字节。 - 数据库链接名最长可为 128 个字节。 2.~ 不带引号的名称不能采用 Oracle 保留字。 3.~ 不带引号的名称必须以数据库字符集中的字母字符开头。 4.~ 不建议使用带引号的名称。 注： 命名数据库中的对象时，可以将名称括在双引号 (&quot;&quot;) 中。使用这种方式，您可以违反幻灯片中提到的一些命名规则。。但是，我们不建议这样做，因为如果采用这种方式命名对象，那么引用时名称永远都要带着引号。 不带引号的名称会以大写形式存储且不区分大小写。处理 SQL 语句时，不带引号的名称会全部转换为大写。 不带引号的标识符只能包含数据库字符集中的字母数字字符，以及下划线 (_) 、美元符号 ($) 和磅符号 (#) 。数据库链接还可包含句点 (.) 和 &quot;at&quot; 符号 (@) 。强烈反对在不带引号的标识符中使用 $ 和 # 。 带引号的标识符可以包含任意字符、标点符号和空格。但是，带引号与不带引号的标识符都不能包含双引号。

以下是 Oracle 数据库中的内置数据类型： 1.CHAR(size [BYTE|CHAR]) ： size 个字节或字符的固定长度字符数据。最大大小为 2,000 个字节或字符，默认大小及最小大小均为 1 个字节。 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~- BYTE 表示列采用字节长度语义。 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~- CHAR 表示列采用字符语义。 2.VARCHAR2(size [BYTE|CHAR]) ：最大长度为 size 个字节或字符的可变长度字符串。最大大小为 4,000 个字节。必须指定 VARCHAR2 的大小。 3\. ：在公元前 4712 年 1 月 1 日至公元 9999 年 12 月 31 日之间的有效日期。它还存储以下时间：小时、分钟和秒。 4\. NUMBER(p,s) ：精度为 p 且小数位数为 s 的数字。精度可介于 1 与 38 之间。小数位数可介于 -84 与 127 之间。 5\. ： 32 位浮点数。这种数据类型需要 5 个字节（包括长度字节）。 6\. ： 64 位浮点数。这种数据类型需要 9 个字节。 7\. ：美国国家标准协会 (ANSI) 数据类型。 FLOAT 数据类型是二进制精度为 p 的浮点数。这种数据类型的默认精度为二进制数 126 或十进制数 38 。 8\. ：等效于 NUMBER(p,0) 。 9\. ： NCHAR 数据类型是仅使用 Unicode 的数据类型。创建包含 NCHAR 列的表时，请以字符为单位定义列长度。创建数据库时，请定义国家字符集。列的最大长度根据国家字符集定义来确定。 NCHAR 数据类型的宽度规范是指字符数。允许的最大列大小为 2,000 个字节。如果插入值小于列长度，为了完全达到列长度， Oracle 数据库会使用空格填充该值。不能将 CHAR 值插入到 NCHAR 列中，也不能将 NCHAR 值插入到 CHAR 列中。 10.~ NVARCHAR2 (size [BYTE|CHAR]) ： NVARCHAR2 数据类型是仅使用 Unicode 的数据类型。它与 NCHAR 相似，不同之处在于，它的最大长度为 4,000 字节且不能使用空格填充。 11\. ：可变长度最大为 2 GB 或 231 -1 个字节的字符数据。 LONG 数据类型已弃用；请改用大对象 (LOB) 数据类型。 12.RAW ：可变长度最大为 2 GB 的原始二进制数据。 13\. ：长度为 size 个字节的原始二进制数据。最大大小为 2,000 个字节。必须指定 RAW 值的大小。 14\. ： Base 64 字符串，表示某一行在数据库中的唯一地址。这种数据类型主要用于 ROWID 伪列返回的值。 15\. ： Base 64 字符串，表示某一行在索引表中的逻辑地址。可选大小为 UROWID 类型的列的大小。最大大小及默认大小均为 4,000 个字节。 16\. ：二进制大对象。 17\. ：字符大对象，包含单字节字符或多字节字符。支持固定宽度字符集和可变宽度字符集，这两种字符集都使用 CHAR 数据库字符集。 18\. ：字符大对象，包含 Unicode 字符。支持固定宽度字符集和可变宽度字符集，这两种字符集都使用 NCHAR 数据库字符集。这种数据类型用于存储国家字符集数据。 注：所有 LOB 数据类型（ BLOB 、 CLOB 和 NCLOB ）的最大大小为： (4 GB - 1) * (CHUNK 值 ) 。 CHUNK 是定义 LOB 时可设置的可选属性。 CHUNK 指定了要为 LOB 操作分配的字节数。如果字节大小不是数据库块大小的倍数，那么数据库会将字节大小舍入为下一个倍数。例如，如果数据库块大小为 2,048 ， CHUNK 大小为 2,050 ，数据库会分配 4,096 字节（ 2 个块）。最大值为 32,768 (32 KB) ，这是允许的 Oracle 数据库块最大大小。默认的 CHUNK 大小为 1 个 Oracle 数据库块。 19\. ： BFILE 数据类型包含到存储在数据库之外的大型二进制文件的定位符。通过这种数据类型可以字节流 I/O 方式访问驻留在数据库服务器上的外部 LOB 。最大大小为 4 GB 。 20\. ：使用这种数据类型可指定日期中的年、月和日，以及时间中的小时、分钟和秒。其中 fractional_seconds_precision 是秒小数部分的位数。可接受值为 0 至 9 。默认值为 6 。

Data Integrity

Part2.Data Integrity 数据完整性

**列完整性约束条件** **:** 1.NULL （非空）：默认情况下，表中的所有列均允许空值。 Null 指没有值。 NOTNULL 约束条件要求表列必须包含非空值。 例如，可以定义 NOT NULL 约束条件要求在 EMPLOYEES 表的每一行的 LAST_NAME 列中输入值。 2.Key （唯一键）： UNIQUE 键完整性约束条件要求一列或一组列（键）中的每个值必须唯一，即在指定的列或一组列中，表任意两行的值均不重复。 例如，如果要禁止每行显示重复的部门名称，请对 DEPARTMENTS 表的 DEPARTMENT_NAME 列定义 UNIQUE 键约束条件。除了特殊情况，这个约束条件与唯一索引一起强制使用。 3.KEY （主键）：数据库中的每个表最多可包含一个 PRIMARY KEY 约束条件。在这个约束条件的限制下，单个列或一组列中的值就构成了行的唯一标识符。事实上，每行的名称均由其主键值确定。 - 在指定列或一组列中，表的任意两行不出现重复值。 - 主键列不允许为空。也就是说，每行的主键列必须有一个值。 4\. 引用完整性约束条件：关系数据库中的各个表通过公用列相互关联，因此必须维护用于管理列关系的规则。引用完整性规则可确保这些关系能够持续下去。对于每个表行，引用完整性约束条件要求外键中的值必须与父键中的值匹配。 5\. 检查约束条件：单个列或一组列的 CHECK 完整性约束条件要求为每个表行指定的条件必须为真或未知。如果数据操纵语言 (DML) 语句对 CHECK 约束条件的求值为假，则会回退语句。

**违反约束条件的示例包括：** 1.~ 唯一性：插入重复的主键值 2.~ 引用完整性：删除引用完整性约束条件中的子行的父行 3.~ 检查：更新后的列值超出了检查约束条件的范围

**约束条件状态** 1.NOVALIDATE ：不检查新数据和现有数据，因此这些数据可能不符合约束条件。当数据来自验证过的源，而且表是只读表时，通常会使用此状态，这样就不会在表中输入新数据。 2.VALIDATE ：如果约束条件处于此状态，则不允许对受约束的列进行任何修改，因为在验证现有数据后又允许将未经检查的数据输入表中，会出现不一致的情况。在必须验证现有数据，但不希望修改数据，而且考虑到性能又不需要另外创建索引时，通常会使用此状态。 3.NOVALIDATE ：新数据符合约束条件，但现有数据处于未知状态。在可以更正现有约束条件违规情况，同时又不允许将新的违规数据输入到系统中时，常常会使用此状态。 4.VALIDATE ：新数据与现有数据均符合约束条件。这是约束条件的典型状态和默认状态。

**约束条件检查** **非延迟约束条件：** 又称即时约束条件，是在每个 DML 语句结束时强制执行的。违反约束条件会导致回退语句。如果约束条件导致 delete cascade 等操作，则认为该操作是导致执行此操作的语句的一部分。定义为 &quot; 非延迟 &quot; 的约束条件不能更改为延迟约束条件。 **延迟约束条件：** 是提交事务处理时才检查的约束条件。如果在提交时检测到任何违反约束条件的情况，则会回退整个事务处理。当同时输入外键关系中的父行和子行（如在订单录入系统中，需要同时录入订单及订单项）时，这些约束条件非常有用。

使用 SQL 创建约束条件：示例 a. 执行此语句后，需要对 COUNTRIES 表执行插入或更新操作，才能生成唯一的 COUNTRY_NAME 值。但是，有可能在发出此语句后，表中已存在非唯一的 COUNTRY_NAME 值。 NOVALIDATE 关键字指示应忽略这些值。仅新行受到约束条件的约束。 SQL&gt; ALTER TABLE countries ADD (UNIQUE(country_name) ENABLE NOVALIDATE);b. 使用此语句可向雇员表添加主键。约束条件名称为 PK ，主键为 EMPLOYEE_ID 列。 SQL&gt;ALTER TABLE employees ADD CONSTRAINT pk PRIMARY KEY (employee_id)c. 此语句用于在创建表时定义约束条件，而不是以后使用 ALTER TABLE 语句进行定义。 RI 约束条件强制使 FK 列中的值必须存在于 T1 表的主键列中。 CK1 约束条件强制使 PK 和 C1 列大于零。 SQL&gt; CREATE TABLE t1 (pk NUMBER PRIMARY KEY, fk NUMBER, c1 NUMBER,SQL&gt; c2 NUMBER,SQL&gt; CONSTRAINT ri FOREIGN KEY (fk) REFERENCES t1, CONSTRAINTSQL&gt; ck1 CHECK (pk &gt; 0 and c1 &gt; 0));

----------------------------------------------------------------------------- 高水位线（ High-warter mark, HWM ）

在 Oracle 数据的存储中，可以把存储空间想象为一个水库，数据想象为水库中的水。水库中的水的位置有一条线叫做水位线，在 Oracle 中，这条线被称为高水位线（ High-warter mark, HWM ）。在数据库表刚建立的时候，由于没有任何数据，所以这个时候水位线是空的，也就是说 HWM 为最低值。当插入了数据以后，高水位线就会上涨，但是这里也有一个特性，就是如果你采用 delete 语句删除数据的话，数据虽然被删除了，但是高水位线却没有降低，还是你刚才删除数据以前那么高的水位。也就是说，这条高水位线在日常的增删操作中只会上涨，不会下跌。 下面我们来谈一下 Oracle 中 Select 语句的特性。 Select 语句会对表中的数据进行一次扫描，但是究竟扫描多少数据存储块呢，这个并不是说数据库中有多少数据， Oracle 就扫描这么大的数据块，而是 Oracle 会扫描高水位线以下的数据块。现在来想象一下，如果刚才是一张刚刚建立的空表，你进行了一次 Select 操作，那么由于高水位线 HWM 在最低的 0 位置上，所以没有数据块需要被扫描，扫描时间会极短。而如果这个时候你首先插入了一千万条数据，然后再用 delete 语句删除这一千万条数据。由于插入了一千万条数据，所以这个时候的高水位线就在一千万条数据这里。后来删除这一千万条数据的时候，由于 delete 语句不影响高水位线，所以高水位线依然在一千万条数据这里。这个时候再一次用 select 语句进行扫描，虽然这个时候表中没有数据，但是由于扫描是按照高水位线来的，所以需要把一千万条数据的存储空间都要扫描一次，也就是说这次扫描所需要的时间和扫描一千万条数据所需要的时间是一样多的。所以有时候有人总是经常说，怎么我的表中没有几条数据，但是还是这么慢呢，这个时候其实奥秘就是这里的高水位线了。 那有没有办法让高水位线下降呢，其实有一种比较简单的方法，那就是采用 TRUNCATE 语句进行删除数据。采用 TRUNCATE 语句删除一个表的数据的时候，类似于重新建立了表，不仅把数据都删除了，还把 HWM 给清空恢复为 0 。所以如果需要把表清空，在有可能利用 TRUNCATE 语句来删除数据的时候就利用 TRUNCATE 语句来删除表，特别是那种数据量有可能很大的临时存储表。 在手动段空间管理（ Manual Segment Space Management ）中，段中只有一个 HWM ，但是在 Oracle9iRelease1 才添加的自动段空间管理（ Automatic Segment Space Management ）中，又有了一个低 HWM 的概念出来。为什么有了 HWM 还又有一个低 HWM 呢，这个是因为自动段空间管理的特性造成的。在手段段空间管理中，当数据插入以后，如果是插入到新的数据块中，数据块就会被自动格式化等待数据访问。而在自动段空间管理中，数据插入到新的数据块以后，数据块并没有被格式化，而是在第一次在第一次访问这个数据块的时候才格式化这个块。所以我们又需要一条水位线，用来标示已经被格式化的块。这条水位线就叫做低 HWM 。一般来说，低 HWM 肯定是低于等于 HWM 的。

---------------------------------------

Indexes

**Part3.INDEX** **索引** 索引是与表关联的可选结构。创建索引可提高更新和检索数据的性能。 Oracle 索引提供了到数据行的直接访问路径。 可以对表的一个或多个列创建索引。创建索引后， Oracle 服务器会自动维护和使用索引。表数据的更新（如添加新行、更新行或删除行）会自动传播到所有相关的索引，这些对用户来说是完全透明的。

B 树索引：采用二进制树的形式，它是默认的索引类型。 位图索引：每个建立索引的独特值都有一个位图，每一位的位置都表示一行，其中可能包含（也可能不包含）索引值。对于低基数列而言，这是最佳结构。

索引选项 1.~ 唯一索引可确保每个索引值是唯一的。 2.~ 索引可按升序或降序存储其键值。 3.~ 反向键索引以反向顺序存储其键值字节。 4.~ 组合索引是基于多列的索引。 5.~ 基于函数的索引是以函数返回值为基础的索引。 6.~ 在压缩的索引中重复的键值已被删除。

CREATE INDEX my_index ON employees(last_name, first_name);

View

Part4.View

视图是一个或多个表中或其它视图中的数据的自定义表示方式。由于视图可隐藏极其复杂的条件、联接，以及其它复杂表达式和SQL 构造，所以可认为视图是存储的查询。实际上，视图并不包含数据，它们从所基于的表中获取数据。这些表称为视图的基表。

Sequences

Part5.Sequences

Temp Tables

Part6.Temp Tables

#### 8.Concurrency {#8-concurrency}

Locks

1\. ROW SHARE ：允许并发访问锁定的表，但禁止会话锁定整个表进行独占访问。

2\. ROW EXCLUSIVE：与ROW SHARE 相同，但是还禁止以SHARE 模式锁定。更新、插入或删除数据时会自动获取ROW EXCLUSIVE 锁定。

3\. SHARE：允许并发查询，但禁止更新锁定的表。需要有（并且会自动请求）SHARE锁定才能创建表的索引。

4\. SHARE ROW EXCLUSIVE：用于查询整个表，允许其他人查询表中的行，但禁止其他人在SHARE 模式下锁定表或更新行。

5\. EXCLUSIVE：允许查询锁定表，但禁止对锁定表执行任何其它活动。需要有EXCLUSIVE 锁定才能删除表。

DML Locks

DML 锁定,每个DML 事务处理会获取两个锁定：

1\. 针对正在更新的一行或多行的EXCLUSIVE 行锁定

2\. 针对要更新的表的ROW EXCLUSIVE 表级锁定。这可避免在进行更改时另一会话

锁定整个表（可能会删除或截断表）。

#### 10.Security {#10-security}

Requirements

**Part1.** **业界安全性要求**

**1.1.** **法律：** - Sarbanes-Oxley 法案 (SOX)- 安全信息流通和责任法案 (HIPAA)- 加利福尼亚州违约法 - 英国数据保护法案

**1.2** **审计：** 许多法律都包括要求定期审计安全计划（内部控制能力）的条款。 SOX 的要求不太明确，以公司负责人的解释为准。实施细节可能会有很大的出入，这取决于公司负责人要求的细节级别。虽然 SOX 的要求不太明确，但是处罚很严厉，所以一定要确保公司符合其规定。实施安全措施的成本要与这种风险达到平衡。没有人能保证 100% 的安全性。最好的解决方法是与业界达成共识。如果您履行了议定的最低安全方案，并且已尽职尽责，就不会受到最不利的法律处罚。有一些可靠的资源提供了业界标准方案，如：美国系统管理和网络安全审计协会 (SANS) 、卡内基梅隆大学计算机系统防御系的 CERT/CC 以及 ISO-17799 认证标准，可从以下地址访问这些资源： [http://www.sans.org/index.php](http://www.sans.org/index.php) [http://www.cert.org/nav/index.html](http://www.cert.org/nav/index.html) [http://www.iso17799software.com/](http://www.iso17799software.com/) ISO-17799 认证标准是关于安全方案的国际标准，其中包括最佳方案、证书和风险评估。它涵盖了各种各样的问题并包括一些预先制定的策略。

**安全性包括以下几个方面：** - **限制对数据和服务的访问** 并不是所有用户对所有数据都具有访问权限。根据数据库中存储的内容，可按业务需要、客户期望以及日益增加的法律限制条件来管理有限制的访问权限。必须保护信用卡信息、健康保健数据、身份信息等，使之免受未授权访问。 Oracle 数据库通过提供细粒度的授权控制来限制数据库访问。限制访问时必须应用最少权限原则。 **- 验证用户** 为了对敏感数据强制实施访问控制，系统必须首先知道谁尝试访问数据。如果验证信息泄漏，则所有其它安全性预防措施都变得毫无用处。最基本的用户验证方式是要求用户提供知道的验证信息，如口令。确保口令遵循简单的规则，这样可大大增强系统的安全性。比较严格的验证方法包括要求用户提供掌握的某些验证信息，如令牌或公共密钥基础结构 (PKI) 证书。更严格的验证方法是，通过指纹、虹膜、骨组织模式等唯一生物学特征来识别用户。 Oracle 数据库通过高级安全选项支持高级验证技术，如基于令牌、生物学和证书的身份识别技术。为了防止试图泄漏验证信息，必须锁定当前未使用的用户帐户。 **- 监视可疑活动** 即使经过授权和验证的用户有时也会泄漏系统信息。要查到信息失窃的原因，第一步就是找出不寻常的数据库活动，如某个雇员突然开始查询大量的信用卡信息、研究结果或其它敏感信息。为了跟踪用户活动和确定可疑活动的变化趋向， Oracle 数据库提供了很多审计工具。

Least Privilege

**Part2.** **最少权限原则** 首先在最低级别应用最少权限原则，继而在每一级别应用此原则。总是会出现一些新的、不能预料的安全问题。通过应用这个原则，可降低发生问题的可能性，从而避免可能受到的损害。 **- 只在计算机上安装所需软件：** 通过减少安装的软件包数目，减少升级维护工作，降低出现安全漏洞的可能性，减少软件冲突。 **- 只在计算机上激活所需服务：** 服务越少意味着打开的端口越少，因而受到的攻击也就越少。 **- 只允许需要访问的用户访问操作系统** **(OS) 和数据库：** 用户越少意味着口令和帐户越少。这会降低公开帐户或帐户失效的可能性。 ~~~~~ 帐户越少，管理员就越容易跟踪帐户的最新活动。 **-~** **限制对** **root 或管理员帐户的访问：** 必须仔细保护和审计管理员帐户，决不能共享管理员帐户。 **- 限制对** **SYSDBA 和** **SYSOPER 帐户的访问：** 需要访问这两个角色的用户，必须各自有自己的帐户并且必须经过审计。 **- 只允许用户访问完成工作所需的数据库对象：** 如果用户访问的对象和服务超过需要访问的范围，就有机会发生不当行为。

**2.1** **应用最少权限原则** 应用最少权限原则意味着必须仅为用户授予有效完成任务所需的那些权限。这会降低用户无意中或恶意修改或查看他们本来无权修改或查看的数据的机率。 **a. 保护数据字典：** ~~~ 默认情况下， O7_DICTIONARY_ACCESSIBILITY 参数设置为 FALSE 。如果没有很好的理由，一定不要更改这个设置，因为这会阻止具有 ANY TABLE 系统权限的用户访问数据字典的基表。这个参数设置还确保 SYS 用户只能以 SYSDBA 身份登录。 **b.~** **从** **PUBLIC 撤销不必要的权限：** ~~~ 下列程序包对于需要这些程序包的应用程序非常有用，但需要进行正确的配置，以便安全地使用它们。请从 PUBLIC 撤销 EXECUTE 权限，在需要下列程序包时为角色授予 EXECUTE 权限： UTL_SMTP 、 UTL_TCP 、 UTL_HTTP 和 UTL_FILE 。 ~~~~~~ 有些功能较强的程序包可能会被误用，这些程序包包括： ~~~~~~UTL_SMTP ：允许将数据库用作简单邮件传输协议 (SMTP) 邮件服务器来发送任意电子邮件消息。 ~~~~~~~~~~~~~~~~~~~~~~~~~~ 如果将这个程序包授予 PUBLIC ，可能会出现未经授权就交换电子邮件消息的情况。 ~~~~~~UTL_TCP ：允许数据库服务器与任何正在接收或等待的网络服务建立外出网络连接。这样，便可以在数据库服务器和任何正在等待的网络服务之间发送任意数据。 ~~~~~~UTL_HTTP ：允许数据库服务器通过 HTTP 请求和检索数据。如果将这个程序包授予 PUBLIC ，可能会允许通过 HTML 形式将数据发送到恶意 Web 站点的行为。 ~~~~~~ ：如果配置不当，则可能会允许访问主机操作系统上任何文本文件的行为。即使配置正确，这个程序包也不会区分其调用应用程序； ~~~~~~~~~~~~~~~~~~~~~~~ 因此对 UTL_FILE 具有访问权的一个应用程序可能会将任意数据写到由另一个应用程序所写到的位置。 **c.~** **限制对操作系统目录的访问：** ~~~ 通过数据库中的 DIRECTORY 对象， DBA 可将目录映射到操作系统路径，从而将对这些目录的权限授予给各个用户。 **d. 限制具有管理权限的用户：** ~~~ 不要为数据库用户提供不必要的权限。一定不能将 DBA 角色授予给管理员之外的人员。要实施最少权限原则，请限制下列类型的权限： ~~~~~~ 系统和对象的权限 ~~~~~~ 连接到数据库的 SYS 权限，如 SYSDBA 和 SYSOPER~~~~~~ 其它 DBA 类型的权限，如 DROP ANY TABLE **e. 限制远程数据库验证：** ~~~ 默认情况下， EMOTE_OS_AUTHENT 参数设置为 FALSE 。一定不要更改这个设置，除非认为所有客户机都能适当地验证用户。在远程验证过程中： **~~~-** 数据库用户在外部进行验证 **~~~-** 远程系统对用户进行验证 **~~~-** 用户登录数据库，不必进行进一步的验证

Standard database Auditing

**审计** **(** **监视可疑活动** **)** 审计意味着捕获并存储关于系统中所发生情况的信息，这会增加系统需要执行的工作量。审计必须有重点，以便只捕获有意义的事件。如果审计重点设置适当，则会最大程度地减少对系统性能的影响。如果审计重点设置不当，则会对系统性能产生明显的影响。 3.1~ 强制性审计：不管其它审计选项或参数为何，所有 Oracle 数据库都会审计特定的操作。由于数据库需要记录系统启动和关闭等数据库活动，所以存在强制性审计日志。 3.2~ 标准数据库审计：这是通过使用 AUDIT_TRAIL 初始化参数在系统级别设置的。启用审计后，可选择要审计的对象和权限。 3.3~ 基于值审计：这种审计扩展了标准数据库审计的功能，不仅会捕获已发生的审计事件，还会捕获插入、更新或删除的实际值。基于值审计是通过数据库触发器实施的。 3.4~ 细粒度审计 (FGA) ： FGA 扩展了标准数据库审计的功能，这种审计可捕获已发出的实际 SQL 语句，而不仅仅是发生了事件的 SQL 语句。 3.5 审计：在 DBA 与审计者或安全管理员之间划分审计责任，审计者或安全管理员在操作系统审计线索中负责监视 DBA 的活动。

**3.2** **标准数据库审计（）** 启用数据库审计并指定审计选项（登录事件、系统活动和对象权限或使用的 SQL 语句）后，数据库开始收集审计信息。 3.2.1 ALTER SYSTEM SET audit_trail= &quot;OS&quot; SCOPE=SPFILE;~~~~~~~ 审计记录将存储在操作系统的审计系统中。 ~~~~~~~~ 在 Windows 环境下，审计记录存储在事件日志中。 ~~~~~~~~ 在 UNIX 或 Linux 环境下，审计记录存储在文件中。该文件的位置是 AUDIT_FILE_DEST 参数指定的位置。 3.2.2~ALTER SYSTEM SET audit_trail= &quot;DB&quot; SCOPE=SPFILE;~~~~~~~~ 审计记录会写入 视图中，这个视图是 SYS 方案的一部分。 3.2.3~ALTER SYSTEM SET audit_trail=&quot;XML&quot; SCOPE=SPFILE;~~~~~~~~ALTER SYSTEM SET audit_trail=&quot;XML,EXTENDED&quot; SCOPE=SPFILE;~~~~~~~~ 审计记录会写入 参数指向的目录中的 XML 文件。使用 V$XML_AUDIT_TRAIL 视图可查看此目录中的所有 XML 文件。 维护审计线索是一项重要的管理任务。取决于审计选项的重点，审计线索会迅速增长。 如果维护不当，审计线索会占用大量的空间，以至影响系统的性能。

**audit_sys_operations** 默认为 false ，当设置为 true 时，所有（注意是所有！） sys 用户（包括以 sysdba,sysoper 身份登录的用户）的操作都会被记录， audit trail 不会写在 aud$ 表中，这个很好理解，如果数据库还未启动 aud$ 不可用，那么像 conn /as sysdba 这样的连接信息，只能记录在其它地方。如果是 windows 平台， audti trail 会记录在 windows 的事件管理中，如果是 linux/unix 平台则会记录在 audit_file_dest 参数指定的文件中

**3.3** **指定审计选项** **3.3.1~** **审计** **SQL 语句：** ~~ **Statement** 按语句来审计，比如 audit table 会审计数据库中所有的 create table,drop table,truncate table 语句， alter session by cmy 会审计 cmy 用户所有的数据库连接。 例： AUDIT table; **3.3.2~** **审计系统权限（非重点和重点）：** 按权限来审计，当用户使用了该权限则被审计，如执行 grant select any table to a; audit select any table; 语句后，当用户 a 访问了用户 b 的表时（如 select * from b.t; ）会用到 select any table 权限，故会被审计。注意用户是自己表的所有者，所以用户访问自己的表不会被审计 例： AUDIT select any table, create any trigger; 例： AUDIT select any table BY hr BY SESSION; **3.3.3~** **审计对象权限（非重点和重点）：** **Object** 按对象审计，只审计 on 关键字指定对象的相关操作，如 aduit alter,delete,drop,insert on cmy.t by scott; 这里会对 cmy 用户的 t 表进行审计，但同时使用了 by 子句，所以只会对 scott 用户发起的操作进行审计 . 注意 Oracle 没有提供对 schema 中所有对象的审计功能，只能一个一个对象审计，对于后面创建的对象， Oracle 则提供 on default 子句来实现自动审计，比如执行 audit drop on default by access; 后， 对于随后创建的对象的 drop 操作都会审计。但这个 default 会对之后创建的所有数据库对象有效，似乎没办法指定只对某个用户创建的对象有效，想比 trigger 可以对 schema 的 DDL 进行 &quot; 审计 &quot; ，这个功能稍显不足。 例： AUDIT ALL on hr.employees; 例： AUDIT UPDATE,DELETE on hr.employees BY ACCESS;

**3.3.4** **审计的一些其他选项** **by access / by session** by access 每一个被审计的操作都会生成一条 audit trail 。 by session ，一个会话里面同类型的操作只会生成一条 audit trail 。 默认为 by session **whenever [ not ] successful** whenever successful 操作成功 (dba_audit_trail 中 returncode 字段为 0) 才审计 ,whenever not successful 反之。 省略该子句的话，不管操作成功与否都会审计。

**4\. 取消审计** **NOAUDIT**

**5\. 和审计相关的视图** **dba_audit_trail** 保存所有的 audit trail ，实际上它只是一个基于 aud$ 的视图。其它的视图 dba_audit_session,dba_audit_object,dba_audit_statement 都只是 dba_audit_trail 的一个子集。 **dba_stmt_audit_opts~~--** 可以用来查看 statement 审计级别的 audit options ，即数据库设置过哪些 statement 级别的审计。 **dba_obj_audit_opts** ~~~-- 系统权限审计选项 **dba_priv_audit_opts** ~~-- 对象权限审计选项 **all_def_audit_opts~~~~--** 用来查看数据库用 on default 子句设置了哪些默认对象审计。

STMT_AUDIT_OPTION_MAP~~~-- 审计选项类型代码 AUDIT_ACTIONS~~~-- action 代码 ALL_DEF_AUDIT_OPTS~~~-- 对象创建时默认的对象审计选项 DBA_STMT_AUDIT_OPTS~~~-- 当前数据库系统审计选项 USER_OBJ_AUDIT_OPTS~~~-- 对象审计选项 DBA_AUDIT_TRAIL~~USER_AUDIT_TRAIL~~~-- 审计记录 DBA_AUDIT_OBJECT~~USER_AUDIT_OBJECT~~~-- 审计对象列表 DBA_AUDIT_SESSION~~USER_AUDIT_SESSION~~~-- session 审计 DBA_AUDIT_STATEMENT~~USER_AUDIT_STATEMENT~~~-- 语句审计 DBA_AUDIT_EXISTS~~~-- 使用 BY AUDIT NOT EXISTS 选项的审计 DBA_AUDIT_POLICIES~~~-- 审计 POLICIESDBA_COMMON_AUDIT_TRAIL~~~-- 标准审计 + 精细审计

**6.** **维护审计线索** 应该维护审计线索。遵循下列最佳方案准则： -~ 复查和存储旧记录。 -~ 避免出现存储问题。 -~ 避免记录丢失。 维护审计线索 必须维护每种类型的审计线索。基本维护必须包括复查审计记录及删除数据库或操作系统中的较旧记录。审计线索会不断增长，一直填满可用存储空间。如果文件系统已满，系统可能会崩溃或只是导致性能问题。如果数据库审计线索填满了表空间，则审计操作不能完成。如果审计线索填满了系统表空间，在审计操作停止之前，其它操作的性能会受到影响。 标准审计的审计线索存储在 AUD$ 表中。 FGA 的审计线索存储在 FGA_LOG$ 表中。默认情况下，这两个表都是在 SYSTEM 表空间中创建的。使用导出和导入实用程序可将这两个表移到另一表空间中。 注：不支持将审计表移到 SYSTEM 表空间外部。 在从审计表中删除记录时，审计记录可能会丢失。 最佳方案提示：根据时间戳使用导出实用程序，然后根据同一时间戳删除审计线索中的行。

Value-based Auditing

**基于值审计**

基于值审计的关键在于审计触发器。典型的审计触发器如下所示： CREATE OR REPLACE TRIGGER system.hrsalary_auditAFTER UPDATE OF salaryON hr.employeesREFERENCING NEW AS NEW OLD AS OLDFOR EACH ROWBEGINIF :old.salary != :new.salary THENINSERT INTO system.audit_employeesVALUES (sys_context(&amp;apos;userenv&amp;apos;,&amp;apos;os_user&amp;apos;), sysdate,sys_context(&amp;apos;userenv&amp;apos;,&amp;apos;ip_address&amp;apos;),:new.employee_id ||&amp;apos; salary changed from &amp;apos;||:old.salary||&amp;apos; to &amp;apos;||:new.salary);END IF;END;/ 这个触发器将审计的重点设置为捕获对 hr.employees 表薪水列的更改。更新某行后，触发器就会检查薪水列。如果新旧薪水不相等，那么触发器会在 audit_employees 表（通过一个单独的操作在 SYSTEM 方案中创建）中插入一条审计记录。审计记录包括用户名、执行更改的 IP 地址、标识记录发生更改的主键及更改的实际薪水值。

如果标准数据库审计收集的数据不足，还可使用数据库触发器来捕获关于用户连接的信息。通过使用登录触发器，管理员可以捕获用来标识连接到数据库的用户的数据。示例包括下列内容： -~ 登录人员的 IP 地址 -~ 用于连接到实例的程序名的前 48 个字符 -~ 用于连接到实例的终端名 有关用户参数的完整列表，请参阅《 Oracle 数据库 SQL 参考》中的 &quot;SYS_CONTEXT&quot; 一节。

Fine-grained Auditing

**FGA 策略**

**1.EAMPLE1** **：** **begin** dbms_fga.add_policy (object_schema =&gt; &amp;apos;HR&amp;apos;,object_name =&gt; &amp;apos;EMPLOYEES&amp;apos;,policy_name =&gt; &amp;apos;audit_emps_salary&amp;apos;,audit_condition =&gt; &amp;apos;department_id=10&amp;apos;,audit_column =&gt; &amp;apos;SALARY&amp;apos;,handler_schema =&gt; &amp;apos;secure&amp;apos;,handler_module =&gt; &amp;apos;log_emps_salary&amp;apos;,enable =&gt; TRUE,statement_types =&gt; &amp;apos;SELECT&amp;apos; );end;/

**EXAMPLE2** **：** SQL&gt; exec dbms_fga.add_policy(object_schema=&gt;&amp;apos;SEC&amp;apos;, object_name=&gt; &amp;apos;t_audit&amp;apos;, policy_name=&gt; &amp;apos;check_t_audit&amp;apos;,statement_types =&gt; &amp;apos;INSERT, UPDATE, DELETE, SELECT&amp;apos;);

此过程接受下列参数： **策略名** **Policy Name** 创建每个 FGA 策略时会指定其名称。幻灯片示例通过使用以下参数来命名 AUDIT_EMPS_SALARY 策略： policy_name =&gt; &amp;apos;audit_emps_salary&amp;apos; **审计条件** **Audit Condition** 审计条件是一个 SQL 谓词，用于定义必须触发审计事件的时间。在幻灯片示例中，通过使用以下条件参数来审计部门 10 中的所有行： audit_condition =&gt; &amp;apos;department_id = 10&amp;apos; **审计列** **Audit Column** 审计列定义了要审计的数据。如果此列包括在 SELECT 语句中或审计条件允许此选择，就会发生审计事件。幻灯片示例使用以下参数来审计两列： audit_column =&gt; &amp;apos;SALARY&amp;apos; 此参数是可选参数。如果未指定此参数，则仅由 AUDIT_CONDITION 参数确定审计事件是否必须发生。 **对象** **Object** 对象是要审计的表或视图。可通过以下两个参数传递对象： -~ 包含对象的方案 -~ 对象的名称 幻灯片示例使用以下参数来审计 hr.employees 表： object_schema =&gt; &amp;apos;hr&amp;apos;object_name = &gt; &amp;apos;employees&amp;apos; **处理程序** **Handler** 可选事件处理程序是一个 PL/SQL 过程，用于定义在审计期间必须执行的任何附加操作。例如，事件处理程序可向管理员发送一个警报页。如果未定义审计事件处理程序，则在审计线索中插入审计事件条目。如果定义了审计事件处理程序，则在审计线索中插入审计条目并执行审计事件处理程序。 审计事件条目包括导致事件的 FGA 策略、执行 SQL 语句的用户、 SQL 语句及其绑定变量。可通过以下两个参数传递事件处理程序： -~ 包含 PL/SQL 程序单元的方案 - 程序单元的名称 幻灯片示例使用以下参数执行 SECURE.LOG_EMPS_SALARY 过程： handler_schema =&gt; &amp;apos;secure&amp;apos;handler_module =&gt; &amp;apos;log_emps_salary&amp;apos; 默认情况下，审计线索总是将 SQL 文本和 SQL 绑定信息写到 LOB 。可以更改默认设置（例如，系统性能下降时）。 **状态** **Status** 状态指示是否启用了 FGA 策略。在幻灯片示例中，以下参数会启用此策略： enable = &gt; TRUE

**2.** **查看审计信息** **:**

SQL&gt; select count(*) from dba_fga_audit_trail;

**3.** **审计的** **DML 语句：注意事项** 使用为 DML 语句定义的 FGA 策略时，如果要处理的数据行（包括新行和旧行）符合策 略谓词标准，就会审计 DML 语句。 但是，如果同时在策略定义中指定了相关列，当数据符合 FGA 策略谓词并且语句引用了 定义的相关列时，就会审计语句。 对于 DELETE 语句，在定义策略期间指定相关列没有用，这是因为 DELETE 语句会访问 表中的所有列。因此，不管相关列是什么，总是要审计 DELETE 语句。 FGA 支持 MERGE 语句。如果基础 INSERT 或 UPDATE 语句符合任何定义的 INSERT 或 UPDATE FGA 策略，则会审计这些语句。 使用以前定义的 FGA 策略时，会审计第一个语句而不审计第二个语句。 ~~~

**4.FGA 准则** -~ 要审计所有语句，请使用 null 条件。 - 策略名必须唯一。 -~ 创建策略时，审计的表或视图必须已经存在。 -~ 如果审计条件语法无效，则访问审计对象时会发生 ORA-28112 错误。 -~ 如果表中不存在审计的列，则不会审计任何行。 -~ 如果事件处理程序不存在，则不会返回任何错误但仍会创建审计记录。

DBA Auditing

DBA 审计

具有SYSDBA 或SYSOPER 权限的用户可在数据库处于关闭状态时进行连接。

- 审计线索必须存储在数据库外部。

- 总是审计以SYSDBA 或SYSOPER 身份进行的连接。

- 可使用audit_sys_operations 启用对SYSDBA或SYSOPER 操作的附加审计。

- 可使用audit_file_dest 控制审计线索。

DBA 审计

SYSDBA 和SYSOPER 用户有权启动和关闭数据库。因为关闭数据库时这些用户可能做了更改，所以这些权限的审计线索必须存储在数据库外部。Oracle 数据库会自动捕获SYSDBA 和SYSOPER 用户的登录事件。这为跟踪授权或未授权SYSDBA 和SYSOPER 操作提供了一种有效方法，但只在复查操作系统审计线索时才有用。

除非专门启用了审计，否则Oracle 数据库只捕获登录事件。通过设置初始化参数可启用对SYSDBA 和SYSOPER 用户的审计：

audit_sys_operations=TRUE（默认设置为FALSE。）

如果审计了SYS 操作，audit_file_dest 初始化参数则会控制审计记录的存储位置。在Windows 平台上，默认情况下，审计线索存储在Windows 事件日志中。在UNIX 或Linux 平台上，审计记录存储在$ORACLE_HOME/rdbms/audit 中。

Security Updates

1\. 安全更新 - 在 Oracle Technology Network Web 站点上公布了安全警报，其网址如下： [http://www.oracle.com/technology/deploy/security/alerts.htm](http://www.oracle.com/technology/deploy/security/alerts.htm) - 数据库管理员和开发人员单击 &quot;Subscribe to Security Alerts Here （订阅这里的安全警报） &quot; 链接进行预订后，便可通过电子邮件得到有关严重安全警报的通知。

Oracle 安全警报包含关于安全漏洞、风险评估及与漏洞相关的泄漏程度、适用的解决方法或补丁程序的简要描述。可使用 Enterprise Manager 管理补丁程序。 Oracle 会就个人或机构是否得到有关安全漏洞的通知进行确认。 安全警报被公布在 Oracle Technology Network Web 站点和 Oracle MetaLink 上。尽管安全警报是向关心这些警报的所有人公布的，但是只有拥有当前客户支持标识 (CSI) 号的客户才能下载补丁程序。 Oracle 感谢您通过及时、完善且机密的潜在安全漏洞通知来配合我们保护产品的安全。如果您发现任何 Oracle 产品有安全漏洞，请通过 MetaLink 提交服务请求或将电子邮件 发送到 secalert_us@oracle.com 来通知 Oracle 。

2\. 应用安全补丁程序 -~ 使用关键补丁程序更新进程。 -~ 应用所有安全补丁程序和解决方法。 -~ 与 Oracle 负责产品安全的团队联系。

**关键补丁程序更新** **(CPU) 方案** Oracle 在 2005 年 1 月启动了 CPU 方案。此方案按季度将关键补丁程序捆绑在一起。此方案取代了安全警报补丁程序的发布。这些补丁程序是累积性补丁程序，其中包括用户经常请求的必备补丁程序。每个季度发布的补丁程序中还包含风险评估体系，可用于确定对站点的影响和站点存在的安全风险。请参阅 MetaLink 注释： 290738.1&quot;Oracle 关键补丁程序更新方案一般常见问题解答 &quot; 。必须预订 MetaLink 才能接收这些更新。 **应用所有安全补丁程序和解决方法** 总是对数据库所在的操作系统和 Oracle 软件，以及所有已安装的选件和组件应用所有相关的最新安全补丁程序。 **与** **Oracle 负责产品安全的团队联系** 如果您确信已经发现了 Oracle 软件中的安全漏洞，请按以下网址中 &quot;Security Alerts （安全警报） &quot; 链接提供的说明进行操作： [http://otn.oracle.com](http://otn.oracle.com) 或 [http://www.oracle.com/technology/deploy/security/alerts.htm](http://www.oracle.com/technology/deploy/security/alerts.htm)

#### 11.Network Environment {#11-network-environment}

SQL&gt; show parameter instance_name;

SQL&gt; show parameter db_name;  

SQL&gt; show parameter db_domain;  #

SQL&gt; show parameter SERVICE_NAME;

listener.ora:

SID_LIST_LISTENER =

 (SID_LIST =

   (SID_DESC =

     (SID_NAME = PLSExtProc)

     (ORACLE_HOME = /opt/ora10g/product/10.2.0/db_guo)

     (PROGRAM = extproc)

   )

 )

LISTENER =

 (DESCRIPTION_LIST =

   (DESCRIPTION =

     (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1))

     (ADDRESS = (PROTOCOL = TCP)(HOST = oracle.example.com)(PORT = 1521))

   )

 )

&lt;username&gt;/&lt;password&gt;@&lt;hostname&gt;:&lt;listener port&gt;/&lt;service name&gt;

#### 14.Backup&amp;Recovery Concepts {#14-backup-recovery-concepts}

#### 16.Performing Database Recovery {#16-performing-database-recovery}

Checkpoint (CKPT)

LogWriter (LGWR)

日志写进程

日志写进程何时执行写入：

? 提交时

? 达到三分之一满时

? 每隔三秒

? DBWn 执行写入之前

重做日志文件记录因执行事务处理和Oracle 服务器内部操作而对数据库所做的更改。（事务处理是逻辑工作单元，由用户运行的一个或多个SQL 语句组成。）

重做日志文件可保护数据库的完整性，避免断电、磁盘故障等因素所导致的系统故障。

重做日志文件必须多路复用，以确保在出现磁盘故障事件时不会丢失其中存储的信息。

重做日志由重做日志文件组组成。

重做日志文件组又由重做日志文件和其多路复用的副本组成。每个相同的副本都是该组的一个成员，每个组按编号标识。

日志写进程(LGWR)进程将重做记录从重做日志缓冲区写入到重做日志组的所有成员，直至文件已填满或请求了日志切换操作。然后，会切换至下一组中的文件并执行写入。重做日志组以循环方式使用。

最佳方案提示：多路复用的重做日志文件应尽量驻留在不同的磁盘中。

Redo Log Files

重做日志文件：

? 记录数据库的更改

? 应多路复用以避免文件丢失

Archiver (ARCn)

归档进程(ARCn)：

? 是一个可选的后台进程

? 将数据库设置为ARCHIVELOG 模式时会自动归档联机重做日志文件

? 保留对数据库进行的所有更改的记录

ARCn 是一个可选的后台进程。但是，在丢失磁盘后恢复数据库时，这个进程至关重要。联机重做日志组填满后，Oracle 实例便开始对下一个联机重做日志组执行写入。

从一个联机重做日志组切换到另一个联机重做日志组的过程称为日志切换。

ARCn 进程在每次进行日志切换时都会对已填满的日志组进行归档。它会在重新使用日志组之前自动归档联机重做日志组，因此会保留对数据库所做的所有更改。这样，即使磁盘驱动器损坏，也可以将数据库恢复到故障点。

DBA 必须决定是将数据库配置为在ARCHIVELOG 模式下运行，还是在NOARCHIVELOG模式下运行。

? 在NOARCHIVELOG 模式下，每次发生日志切换时，都会覆盖联机重做日志文件。

? 在ARCHIVELOG 模式下，必须先归档非活动的已填满联机重做日志文件组，才能再次使用这些组。

注：对大多数备份策略而言，ARCHIVELOG 模式是必须选择的模式（并且极易配置）。

Instance Recovery