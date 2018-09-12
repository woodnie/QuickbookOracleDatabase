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