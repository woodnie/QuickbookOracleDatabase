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