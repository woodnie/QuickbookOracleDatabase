#### sql_trace {#sql-trace}

**使用** **SQL_TRACE** **进行数据库诊断**

作者： [eygle](http://www.eygle.com) | [\\t &quot;_blank&quot;](http://translate.google.com/translate?langpair=zh-CN%7Cen&hl=zh-CN&ie=UTF8&u=http%3A//www.eygle.com/case/Use.sql_trace.to.Diagnose.database.htm) English Version 链接： [http://www.eygle.com/archives/2004/10/use_sql_trace_to_diagnose_database.html](http://www.eygle.com/archives/2004/10/use_sql_trace_to_diagnose_database.html)

**SQL_TRACE** **是** **Oracle** **提供的用于进行** **SQL** **跟踪的手段，是强有力的辅助诊断工具** **.** **在日常的数据库问题诊断和解决中，** **SQL_TRACE** **是非常常用的方法。** **本文就** **SQL_TRACE** **的使用作简单探讨，并通过具体案例对** **sql_trace** **的使用进行说明** **.**

一、 基础介绍

**1.** **说明**

SQL_TRACE 可以作为初始化参数在全局启用，也可以通过命令行方式在具体 session 启用。 1.1\. 在全局启用 ~~~~~ 在参数文件 (pfile/spfile) 中指定 :

~~~~sql_trace =true

在全局启用 SQL_TRACE 会导致所有进程的活动被跟踪，包括后台进程及所有用户进程，这通常会导致比较严重的性能问题，所以在生产环境中要谨慎使用，这个参数在 10g 之后是动态参数，可以随时调整，在某些诊断中非常有效。 提示 : 通过在全局启用 sql_trace ，我们可以跟踪到所有后台进程的活动，很多在文档中的抽象说明，通过跟踪文件的实时变化，我们可以清晰的看到各个进程之间的紧密协调 .

1.2\. 在当前 session 级设置 大多数时候我们使用 sql_trace 跟踪当前进程 . 通过跟踪当前进程可以发现当前操作的后台数据库递归活动 ( 这在研究数据库新特性时尤其有效 ) ， 研究 SQL 执行，发现后台错误等 . 在 session 级启用和停止 sql_trace 方式如下 : 启用当前 session 的跟踪 :SQL&gt; alter session set sql_trace=true;&lt; BR&gt;session altered.

此时的 SQL 操作将被跟踪 :SQL&gt; select count(*) from dba_users;~~~~~~~~~COUNT(*)~~~~~~~~~~~~~~~~~~~~~~~34 结束跟踪 :SQL&gt; alter session set sql_trace=false;&lt; BR&gt; s ession altered.~~~~~~~1.3\. 跟踪其他用户进程 ~~~~~~ 在很多时候我们需要跟踪其他用户的进程，而不是当前用户，这可以通过 Oracle 提供的系统包 DBMS_SYSTEM. SET_SQL_TRACE_IN_SESSION 来完成

SET_SQL_TRACE_IN_SESSION 过程序要提供三个参数 :SQL&gt; desc

dbms_system

我们可以获得 sid 、 serial# 等信息 : 获得进程信息，选择需要跟踪的进程 :

SQL&gt; select sid,serial#,username from v$session~2~where username is not null;

~~~~~~SID~~~SERIAL#~USERNAME---------- ---------- ------------------------------~~~~~~~~8~~~~~~2041~SYS~~~~~~~~9~~~~~~~437~EYGLE

设置跟踪 :SQL&gt; exec dbms_system.set_sql_trace_in_session(9,437,true)

PL/SQL procedure successfully completed.

.... 可以等候片刻，跟踪 session 执行任务 , 捕获 sql 操作 .......

停止跟踪 :SQL&gt; exec dbms_system.set_sql_trace_in_session(9,437,false)

PL/SQL procedure successfully completed.~

**2.10046** 10046 事件说明 10046 事件是 Oracle 提供的内部事件，是对 SQL_TRACE 的增强 .10046 事件可以设置以下四个级别 :1 - 启用标准的 SQL_TRACE 功能 , 等价于 sql_trace4 - Level 1 加上绑定值 (bind values)8 - Level 1 + 等待事件跟踪 12 - Level 1 + Level 4 + Level 8 类似 sql_trace ， 10046 事件可以在全局设置，也可以在 session 级设置。 **2.1\. 在全局设置** 在参数文件中增加 : event=&quot;10046 trace name context forever,level 12&quot; 此设置对所有用户的所有进程生效、包括后台进程 . **2.2\. 对当前** **session** **设置** 通过 alter session 的方式修改，需要 alter session 的系统权限 : SQL&gt; alter session set events &amp;apos;10046 trace name context forever&amp;apos;;Session altered.SQL&gt; alter session set events &amp;apos;10046 trace name context forever, level 8&amp;apos;;Session altered.SQL&gt; alter session set events &amp;apos;10046 trace name context off&amp;apos;;Session altered.~ **~~~~~~~**

**3** **．** **对其他用户** **session** **设置** 通过 DBMS_SYSTEM.SET_EV 系统包来实现 :

SQL&gt; desc dbms_system

...

PROCEDURE SET_EV

Argument Name                  Type                    In/Out Default?

-------------------------- ----------------------- ------ --------

SI                             BINARY_INTEGER          IN

SE                             BINARY_INTEGER          IN

EV                             BINARY_INTEGER          IN

LE                             BINARY_INTEGER          IN

NM                             VARCHAR2                IN

...

其中的参数 SI 、 SE 来自 v$session 视图 : 查询获得需要跟踪的 session 信息 :SQL&gt; select sid,serial#,username from v$session where username is not null;

SID SERIAL# USERNAME---------- ----------

8 2041 SYS9 437 EYGLE  

执行跟踪 :SQL&gt; exec dbms_system.set_ev(9,437,10046,8,&amp;apos;eygle&amp;apos;);

PL/SQL procedure successfully completed.

结束跟踪 :SQL&gt; exec dbms_system.set_ev(9,437,10046,0,&amp;apos;eygle&amp;apos;);

PL/SQL procedure successfully completed.

**3.** **获取跟踪文件** 以上生成的跟踪文件位于 user_dump_dest 目录中，位置及文件名可以通过以下 SQL 查询获得 :

SQL&gt; select

 2    d.value||&amp;apos;/&amp;apos;||lower(rtrim(i.instance, chr(0)))||&amp;apos;_ora_&amp;apos;||p.spid||&amp;apos;.trc&amp;apos; trace_file_name  3  from  4    ( select p.spid  5      from sys.v$mystat m,sys.v$session s,sys.v$process p  6      where m.statistic# = 1 and s.sid = m.sid and p.addr = s.paddr) p,  7    ( select t.instance from sys.v$thread  t,sys.v$parameter  v  8      where v.name = &amp;apos;thread&amp;apos; and (v.value = 0 or t.thread# = to_number(v.value))) i,  9    ( select value from sys.v$parameter where name = &amp;apos;user_dump_dest&amp;apos;) d 10  /

TRACE_FILE_NAME--------------------------------------------------------------------------------/opt/oracle/admin/hsjf/udump/hsjf_ora_1026.trc~

**4.~** **读取当前** **session** **设置的参数** 当我们通过 alter session 的方式设置了 sql_trace, 这个设置是不能通过 show parameter 的方式得到的 , 我们需要通过 dbms_system.read_ev 来获取：

SQL&gt; set feedback off

SQL&gt; set serveroutput on

SQL&gt; declare 2 event_level number; 3 begin 4 for event_number in 10000..10999 loop 5 sys.dbms_system.read_ev(event_number, event_level); 6 if (event_level &gt; 0) then 7 sys.dbms_output.put_line(8 &amp;apos;Event &amp;apos; ||9 to_char(event_number) ||10 &amp;apos; is set at level &amp;apos; || 11 to_char(event_level)12 ); 13 end if; 14 end loop; 15 end; 16 /Event 10046 is set at level 1

**Oracle** **诊断案例** **-Sql_trace** **之二**

作者： [eygle](http://www.eygle.com) | [\\t &quot;_blank&quot;](http://translate.google.com/translate?langpair=zh-CN%7Cen&hl=zh-CN&ie=UTF8&u=http%3A//www.eygle.com/archives/2004/06/sql_trace_2.html) English Version 链接： [http://www.eygle.com/archives/2004/06/sql_trace_2.html](http://www.eygle.com/archives/2004/06/sql_trace_2.html)

问题说明 :

很多时候在我们进行数据库操作时比如 drop user,drop table 等，经常会遇到这样的错误

ORA - 00604 : error occurred at recursive SQL level 1 .

这样的提示，很多时候是没有丝毫用处的 . 本案例就这一类问题提供一个思路及方法供大家参考 .

1\. drop user 出现问题 报出以下错误后退出

ORA - 00604 : error occurred at recursive SQL level 1 ORA - 00942 : table or view does not exist .

~ 关于 recursive SQL 错误我们有必要做个简单说明 . 我们知道，当我们发出一条简单的命令以后 Oracle 数据库要在后台解析这条命令，并转换为 Oracle 数据库的一系列后台操作 . 这些后台操作统称为递归 sql 比如 create table 这样一条简单的 DDL 命令 Oracle 数据库在后台，实际上要把这个命令转换为对于 obj$,tab$,col$ 等底层表的插入操作 .Oracle 所作的工作可能比我们有时候想的要复杂的多 .

2\. 跟踪问题 我们知道 Oracle 提供 sql_trace 的功能可以用于跟踪 Oracle 数据库的后台递归操作 . 通过跟踪文件，我们可以找到问题的所在以下是格式化 (tkprof) 后的输出 :

********************************************************************************

The following statement encountered a error during parse :

DELETE FROM SDO_GEOM_METADATA_TABLE WHERE SDO_OWNER = &amp;apos;WAPCOMM&amp;apos;

Error encountered : ORA - 00942

******************************************************************************** Oracle 把错误信息首先呈现出来 我们看到 ORA-00942 错误是由于 SDO_GEOM_METADATA_TABLE 表 / 视图不存在所致 问题由此可以定位 对于这一类的错误，定位问题以后解决的方法就要依据具体问题原因而定了。

~

3\. 问题定位 对于本案例，通过 Metalink 获得以下解释 :Problem Description ------------------- The Oracle Spatial Option has been installed and you are encountering the following errors while trying to drop a user, who has no spatial tables, connected as SYSTEM: ERROR at line 1: ORA-00604: error occurred at recursive SQL level 1 ORA-00942: table or view does not exist ORA-06512: at line 7 A 942 error trace shows the failing SQL statement as: DELETE FROM SDO_GEOM_METADATA_TABLE WHERE SDO_OWNER = &amp;apos;&lt;user&gt;&amp;apos; Solution Description -------------------- (1) Create a synonym for SDO_GEOM_METADATA_TABLE under SYSTEM which points to MDSYS.SDO_GEOM_METADATA_TABLE. 对于本例，为 MDSYS.SDO_GEOM_METADATA_TABLE 创建一个同义词即可解决 . 是相对简单的情况 . (2) Now the user can be dropped connected as SYSTEM. Related Documents ----------------- &lt;Note.159776.1&gt; ORA-604 and ORA-942 Reported During DROP USER CASCA

4\. 实际处理 MDSYS.SDO_GEOM_METADATA_TABLE 为 Spatial 对象 如果未使用 Spatial 选项，可以删除

SQL&gt; connect / as sysdbaConnected.

SQL&gt; select * from dba_sdo_geom_metadata order by owner; select * from dba_sdo_geom_metadata order by owner*ERROR at line 1:ORA-00942: table or view does not existORA-04063: view &quot;MDSYS.DBA_SDO_GEOM_METADATA&quot; has errors

SQL&gt; select object_name from dba_objects where object_name like &amp;apos;%SDO%&amp;apos;;

OBJECT_NAME--------------------------------------------------------------------------------ALL_SDO_GEOM_METADATAALL_SDO_INDEX_INFOALL_SDO_INDEX_METADATADBA_SDO_GEOM_METADATADBA_SDO_INDEX_INFODBA_SDO_INDEX_METADATA....DBA_SDO_GEOM_METADATADBA_SDO_INDEX_INFO...SDO_WITHIN_DISTANCEUSER_SDO_GEOM_METADATAUSER_SDO_INDEX_INFOUSER_SDO_INDEX_METADATA

88 rows selected.

SQL&gt; drop user MDSYS cascade;

User dropped.

SQL&gt; select owner,type_name from dba_types where type_name like &amp;apos;SDO%&amp;apos;;

no rows selected

SQL&gt;

SQL&gt; alter session set sql_trace=true;&lt; /FONT&gt;

Session altered.

SQL&gt; drop user wapcomm;

User dropped.

SQL&gt; alter session set sql_trace=false;&lt; /FONT&gt;

Session altered.

SQL&gt; exitDisconnected from Oracle8i Enterprise Edition Release 8.1.7.4.0 - 64bit ProductionWith the Partitioning optionJServer Release 8.1.7.4.0 - 64bit Production

这时用户得以顺利 drop

~

5\. 一点总结 使用 sql_trace 可以跟踪数据库的很多后台操作有利于我们发现问题的所在 很多时候，我们想要研究 Oracle 的内部活动或后台操作也可以通过 sql_trace 跟踪 sql_trace/10046 是 Oracle 提供的最为有效的诊断工具之一 .