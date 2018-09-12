#### Flashback {#flashback}

[http://www.cnblogs.com/coohoo/archive/2011/01/16/1936888.html](http://www.cnblogs.com/coohoo/archive/2011/01/16/1936888.html)

Flashback 技术是以 Undo segment 中的内容为基础的， 因此受限于 UNDO_RETENTON 参数。要使用 flashback 的特性，必须启用自动撤销管理表空间。

在 Oracle 10g 中， Flash back 家族分为以下成员： Flashback Database ， Flashback Drop ， Flashback Query( 分 Flashback Query,Flashback Version Query ， Flashback Transaction Query 三种 ) 和 Flashback Table 。

一． Flashback Database

Flashback Database 功能非常类似与 RMAN 的不完全恢复， 它可以把整个数据库回退到过去的某个时点的状态， 这个功能依赖于 Flashback log 日志。 比 RMAN 更快速和高效。 因此 Flashback Database 可以看作是不完全恢复的替代技术。 但它也有某些限制：

1\. Flashback Database 不能解决 Media Failure ， 这种错误 RMAN 恢复仍是唯一选择 2\. 如果删除了数据文件或者利用 Shrink 技术缩小数据文件大小，这时不能用 Flashback Database 技术回退到改变之前的状态，这时候就必须先利用 RMAN 把删除之前或者缩小之前的文件备份 restore 出来， 然后利用 Flashback Database 执行剩下的 Flashback Datbase 。 3\. 如果控制文件是从备份中恢复出来的，或者是重建的控制文件，也不能使用 Flashback Database 。 4\. 使用 Flashback Database 锁能恢复到的最早的 SCN ， 取决与 Flashback Log 中记录的最早 SCN 。

Flashback Database 架构

Flashback Database 整个架构包括一个进程 Recover Writer(RVWR) 后台进程， Flashback Database Log 日志 和 Flash Recovery Area 。一旦数据库启用了 Flashback Database ， 则 RVWR 进程会启动，该进程会向 Flash Recovery Area 中写入 Flashback Database Log ， 这些日志包括的是数据块的 &quot; 前镜像 (before image)&quot; ， 这也是 Flashback Database 技术不完全恢复块的原因。

[oracle@dba ~]$ ps -ef|grep rvw oracle~~12620 12589~0 13:21 pts/1~~~00:00:00 grep rvw

启用 Flashback Database

数据库的 Flashback Database 功能缺省是关闭的，要想启用这个功能，就需要做如下配置。

1\. 配置 Flash Recovery Area 要想使用 Flashback Database ， 必须使用 Flash Recovery Area ，因为 Flashback Database Log 只能保存在这里。 要配置的 2 个参数如下，一个是大小，一个是位置。如果数据库是 RAC ， flash recovery area 必须位于共享存储中。数据库必须处于 archivelog 模式 .

启用 Flash Recovery Area ：

SQL&gt;ALTER SYSTEM SET DB_RECOVERY_FILE_DEST_SIZE=20G SCOPE=BOTH; SQL&gt;ALTER SYSTEM SET DB_RECOVERY_FILE_DEST=&amp;apos;/DBA/FB&amp;apos; SCOPE=BOTH;

禁用 Flash Recovery Area ：

SQL&gt;ALTER SYSTEM SET DB_RECOVERY_FILE_DEST=&amp;apos;&amp;apos; ;

对于 Flash Recovery Area ， Oracle 是这样建议的， flash recovery area 设置的越大， flashback database 的恢复能力就越强，因此建议 flash recovery area 能够放的下所有的数据文件，增量备份，以及所有尚未备份的归档文件，当然还有它自己产生的 flashback logs 。

在数据库运行过程中， oracle 自动向该区域写入文件，当剩余空间不足 15% 的时候，它就会在 alert 中增加警告，提示你空间不足。但此时不会影响数据库的正常运转，直到所有空间统统被用掉之后， oracle 首先尝试删除寻些过期的文件，冗余文件或备份过的文件，如果这些做完了，还是没有空闲空间的话，数据库就被 hang 住了。

对于因 Flash Recovery Area 导致的数据库 hang 的处理，请参考：

[http://blog.csdn.net/tianlesoftware/archive/2009/10/14/4668991.aspx](http://blog.csdn.net/tianlesoftware/archive/2009/10/14/4668991.aspx) 或者 [http://user.qzone.qq.com/251097186/blog/1244650673](http://user.qzone.qq.com/251097186/blog/1244650673)

2\. 启用数据库 Flashback 功能

1). 数据库启动到 mount 状态

SQL&gt; startup mount ；

2). 检查 Flashback 功能， 缺省时功能是关闭的。

SQL&gt; select name, current_scn, flashback_on from v$database;

NAME~~~CURRENT_SCN~FLASHBACK_ON

--------~~~-----------~~~~~~~~~------------------

DBA~~~~~945715~~~~~~~~~NO

3). 启动 Flashback 功能

SQL&gt;~alter database flashback on;

数据库已更改。

SQL&gt;~select name, current_scn, flashback_on from v$database;

NAME~~~~~CURRENT_SCN FLASHBACK_ON

--------- ----------- ------------------

DBA~~~~~~~~~~~~~~~~0 YES

4). 设置初始化参数： DB_FLASHBACK_RETENTION_TARGET ：

SQL&gt;alter system set db_flashback_retention_target=1440 scope=both;

该参数用来控制 flashback log 数据保留的时间，或者说，你希望 flashback database 能够恢复的最早的时间点。默认值是 1440 ，单位是 minute ，即 24 小时，需要注意的是该参数虽然未直接指定 flash recovery area 大小，但却受其制约，举个例子假如数据库每天有 10% 左右的数据变动的话，如果该初始化参数值设置为 1440 ，则 flash recovery area 的大小至少要是当前数据库实际容量的 10% ，如果该初始化参数设置为 2880 ，则 flash recovery area 的大小就至少是数据库所占容量的 20% 。

5). 启动数据库

SQL&gt;alter database open;

Flashback Database 操作示例

做操作前先备份数据库

RMAN&gt; backup database;

1\. 检查是否启动了 flash recovery area:-

SQL&gt; show parameter db_recovery_file

NAME~~~~~~~~~~~~~~~~~~~TYPE~~~~~~~VALUE

------------------------------------~----------- ------------------------------

db_recovery_file_dest~~~~~~tring~~~~~~D:\oracle/flash_recovery_area

db_recovery_file_dest_size~big integer 1G

2\. 检查是否启用了归档 -

SQL&gt; archive log list;

数据库日志模式 ~~~~~ 存档模式

自动存档 ~~~~~~~~~~~ 启用

存档终点 ~~~~~~~~~~~USE_DB_RECOVERY_FILE_DEST

最早的联机日志序列 ~9

下一个存档日志序列 ~11

当前日志序列 ~~~~~~~11

~

3\. 检查是否启用了 flashback database~

SQL&gt; select flashback_on from v$database;

FLASHBACK_ON~~~~~

~

YES~~~~~~~~~~~~~~~

~

4\. 查询当前的 scn-

SQL&gt; SELECT CURRENT_SCN FROM V$DATABASE;

~

CURRENT_SCN

-----------

947921

~

5\. 查询当前的时间

SQL&gt; select to_char(sysdate,&amp;apos;yy-mm-dd hh24:mi:ss&amp;apos;) time from dual;

~

TIME

-----------------

09-10-14 14:37:05

~

6\. 删除表 A

SQL&gt; select * from A;

~

ID~NAME

---------- ----------

1~tianle

2~dave

~

SQL&gt; drop table A;

表已删除。

SQL&gt; commit;

~

7\. Flashback Database 实际是对数据库的一个不完全恢复操作，因为需要关闭数据库重启到 mount 状态。

SQL&gt; shutdown immediate

数据库已经关闭。

已经卸载数据库。

ORACLE 例程已经关闭。

SQL&gt; startup mount

ORACLE 例程已经启动。

~

Total System Global Area~209715200 bytes

Fixed Size~~~~~~~~~~~~~~~~~1248116 bytes

Variable Size~~~~~~~~~~~~~79692940 bytes

Database Buffers~~~~~~~~~121634816 bytes

Redo Buffers~~~~~~~~~~~~~~~7139328 bytes

数据库装载完毕。

~

8\. 执行恢复：分 timestamp 或者 SCN 两种

~

SQL&gt; Flashback database to timestamp to_timestamp(&amp;apos;09-10-14 14:37:05&amp;apos;,&amp;apos;yy-mm-dd

hh24:mi:ss&amp;apos;);

闪回完成。

~

或者：

SQL&gt; Flashback database to scn 947921;

闪回完成。

~

9\. 打开数据库：

在执行完 flashback database 命令之后， oracle 提供了两种方式让你修复数据库：

1). 直接 alter database open resetlogs 打开数据库，当然，指定 scn 或者 timestamp 时间点之后产生的数据统统丢失。

2). 先执行 alter database open read only 命令以 read-only 模式打开数据库，然后立刻通过逻辑导出的方式将误操作涉及表的数据导出，再执行 recover database 命令以重新应用数据库产生的 redo ，将数据库修复到 flashback database 操作前的状态，然后再通过逻辑导入的方式，将之前误操作的表重新导入，这样的话对现有数据的影响最小，不会有数据丢失。

~

这里演示，就以 resetlogs 方式打开：

SQL&gt; alter database open resetlogs;

数据库已更改。

~

验证数据：

SQL&gt; select * from A;

~

~~~~~~~ID NAME

---------- ----------

~~~~~~~~1 tianle

~~~~~~~~2 dave

~

~

~

~

~

~

和 Flashback Database 相关的 2 个视图：

1\. V$database

~ 这个视图可以查看是否启用了 Flashback database 功能

SQL&gt; select flashback_on from v$database;

FLASHBACK_ON

------------------

YES

~

2\. V$flashback_database_log

Database 所能回退到的最早时间，取决与保留的 Flashback Database Log 的多少， 该视图就可以查看许多有用的信息。

Oldest_flashback_scn / Oldest_flashback_time : 这两列用来记录可以恢复到最早的时点

Flashback_size: 记录了当前使用的 Flash Recovery Area 空间的大小

Retention_target: 系统定义的策略

Estimated_flashback_size: 根据策略对需要的空间大小的估计值

SQL&gt; select oldest_flashback_scn os, to_char(oldest_flashback_time,&amp;apos;yy-mm-dd hh2

4:mi:ss&amp;apos;) ot, retention_target rt,flashback_size fs, estimated_flashback_size es

v$flashback_database_log;

~

OS~~~~~~OT~~~~~~~~~~~~RT~~~~~FS~~~~~~~ES

----------~-----------------~~~~----------~----------~~~----------

946088~09-10-14 13:49:59~1440~~~~16384000~350920704

~

3\. V$flashback_database_stat

这个视图用来对 Flashback log 空间情况进行更细粒度的记录和估计。 这个视图以小时为单位记录单位时间内数据库的活动量， Flashback_Data 代表 Flashback log 产生数量， DB_Date 代表数据改变数量， Redo_Date 代表日志数量，通过这 3 个数量可以反映出数据的活动特点，更准确的预计 Flash Recovery Area 的空间需求

~

SQL&gt; alter session set nls_date_format=&amp;apos;hh24:mi:ss&amp;apos;;

~

会话已更改。

~

SQL&gt; select *from v$flashback_database_stat;

~

BEGIN_TI END_TIME FLASHBACK_DATA DB_DATA REDO_DATA ESTIMATED_FLASHBACK_SIZE

-------- -------- -------------- ---------- ---------- ------------------------

14:43:10 15:15:28~~~~~~~6455296~~29310976~~~3898368~~~~~~~~~~~~~0

~

~

~

~

Flashback DROP

~

Flashback Drop 是从 Oracle 10g 开始出现的， 用于恢复用户误删除的对象 ( 包括表，索引等 ) ， 这个技术依赖于 Tablespace Recycle Bin( 表空间回收站 ) ，这个功能和 windows 的回收站非常类似。

Flashback 不支持 sys 用户 . system 表空间下的对象，也不能从回收站里拿到。故使用 SYS 或者 SYSTEM 用户登陆时， show recyclebin 为空。

~

~

1\. Tablespace Recycle Bin

~

从 Oracle 10g 开始， 每个表空间都会有一个叫作回收站的逻辑区域，当用户执行 drop 命令时， 被删除的表和表的关联对象 ( 包括索引， 约束，触发器， LOB 段， LOB index 段 ) 不会被物理删除， 这些对象先转移到回收站中，这就给用户提供了一个恢复的可能。

~

~

初始化参数 recyclebin 用于控制是否启用 recyclebin 功能，缺省是 ON ， 可以使用 OFF 关闭。

~

SQL&gt; show parameter recycle

~

NAME~~~~~~~~~~~~~~~~~~~TYPE~~~~~~~VALUE

------------------------------------ ----------- ------------------------------

buffer_pool_recycle~~~~~~~~~~~string

db_recycle_cache_size~~~~~~~~big integer 0

recyclebin~~~~~~~~~~~~~~~~~~~string~~~~~on

~

禁用该功能：

SQL&gt; alter system set recyclebin=off;

SQL&gt; alter system set recyclebin=on;

~

SQL&gt; alter session set recyclebin=off;

SQL&gt; alter session set recyclebin=on;

~

禁用后删除的对象将直接删除，不会写到 Recycle 中，当然在删除时，指定 purge 参数，表也将直接删除，不会写到 recyclebin 中。 SQL&gt; drop table name purge;

~

查看 recyclebin 中的对象列表：

SQL&gt; select * from A;

~~~~~~~ID

~~~~~~~----------

~~~~~~~~1

~~~~~~~~2

~~~~~~~~3

SQL&gt; drop table A;

表已删除。

SQL&gt; show recyclebin

ORIGINAL NAME~~~RECYCLEBIN NAME~~~~~~OBJECT TYPE~DROP TIME

----------------~~~~~-----------------------------~~~~~~~~~------------~~~~~~-------------------

~

A~~~~~~~~~~~BIN$RWXQQcTPRde0ws4h9ewJcg==$0~TABLE~~~~2009-10-15:12:44:33

~

查看 recyclebin 中对象：

SQL&gt; select original_name,object_name from recyclebin;

~

ORIGINAL_NAME~~~~~~~~~~~~~~~~~~~OBJECT_NAME

-------------------------------- ------------------------------

A~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~BIN$RWXQQcTPRde0ws4h9ewJcg==$0

~

查看 recyblebin 对象里的内容：

SQL&gt; select * from &quot;BIN$RWXQQcTPRde0ws4h9ewJcg==$0&quot;;

~~~~~~~ID

~~~~~~----------

~~~~~~~~1

~~~~~~~~2

~~~~~~~~3

表空间的 Recycle Bin 区域只是一个逻辑区域，而不是从表空间上物理的划出一块区域固定用于回收站，因此 Recycle Bin 是和普通对象共用表空间的存储区域，或者说是 Recycle Bin 的对象要和普通对象抢夺存储空间。当发生空间不够时， Oracle 会按照先入先出的顺序覆盖 Recycle Bin 中的对象。也可以手动的删除 Recycle Bin 占用的空间。

1). Purge tablespace tablespace_name : 用于清空表空间的 Recycle Bin

2). Purge tablespace tablespace_name user user_name: 清空指定表空间的 Recycle Bin 中指定用户的对象

3). Purge recyclebin: 删除当前用户的 Recycle Bin 中的对象

4). Purge dba_recyclebin: 删除所有用户的 Recycle Bin 中的对象，该命令要 sysdba 权限

5). Drop table table_name purge:~ 删除对象并且不放在 Recycle Bin 中，即永久的删除，不能用 Flashback 恢复。

6). Purge index recycle_bin_object_name ： 当想释放 Recycle bin 的空间，又想能恢复表时，可以通过释放该对象的 index 所占用的空间来缓解空间压力。 因为索引是可以重建的。

2\. Flashback Drop 实例操作

SQL&gt; select original_name,object_name from recyclebin;

ORIGINAL_NAME~~~~~~~~~~~~~~~~~~~OBJECT_NAME

-------------------------------- ------------------------------

A~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~BIN$RWXQQcTPRde0ws4h9ewJcg==$0

SQL&gt; flashback table a to before drop;

闪回完成。

SQL&gt; select * from a;

~~~~~~~ID

~~~~~~~~----------

~~~~~~~~1

~~~~~~~~2

~~~~~~~~3

当我们删除表 A 后，在新建表 A ，这时在恢复的时候就会报错，此时我们在闪回时，对表重命名就可以了：

SQL&gt; drop table a;

表已删除。

SQL&gt; create table a

~2~(id number(1));

表已创建。

SQL&gt; flashback table a to before drop ;

flashback table a to before drop

*

第 1 行出现错误 :

ORA-38312: 原始名称已被现有对象使用

SQL&gt; flashback table a to before drop rename to B;

闪回完成。

SQL&gt; select * from B;

~~~~~~~ID

~~~~~~~----------

~~~~~~~~1

~~~~~~~~2

~~~~~~~~3

当我们删除表 A ，在新建表 A ，在删除它，这是在 Recycle Bin 中就会有 2 个相同的表明，此时恢复我们就要指定 object_name 才行 .

SQL&gt; select * from B;

~~~~~~~ID

~~~~~~~----------

~~~~~~~~1

~~~~~~~~2

~~~~~~~~3

SQL&gt; drop table B;

表已删除。

SQL&gt; create table B(name varchar(20));

表已创建。

SQL&gt; drop table B;

表已删除。

SQL&gt; select original_name,object_name from recyclebin;

ORIGINAL_NAME~~~~~~~~~~~~~~~~~~~OBJECT_NAME

--------------------------------~~~~~~~~~~~------------------------------

B~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~BIN$vYuv+g9fTi2exYP9X2048Q==$0

B~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~BIN$geQ9+NekSjuRvzG+TqDVWw==$0

SQL&gt; flashback table &quot;BIN$vYuv+g9fTi2exYP9X2048Q==$0&quot; to before drop;

闪回完成。

SQL&gt; select * from B;

~~~~~~~ID

~~~~~~----------

~~~~~~~~1

~~~~~~~~2

~~~~~~~~3

一旦完成闪回恢复， Recycle Bin 中的对象就消失了 . Flashback Drop 需要注意的地方：

1). 只能用于非系统表空间和本地管理的表空间

2). 对象的参考约束不会被恢复，指向该对象的外键约束需要重建。

3). 对象能否恢复成功，取决与对象空间是否被覆盖重用。

4). 当删除表时，信赖于该表的物化视图也会同时删除，但是由于物化视图并不会被放入 recycle bin ，因此当你执行 flashback table to before drop 时，也不能恢复依赖其的物化视图，需要 dba 手工介入重新创建。

5). 对于 Recycle Bin 中的对象，只支持查询 .

~

~

Flashback Query

Flashback 是 ORACLE 自 9i 就开始提供的一项特性，在 9i 中利用 oracle 查询多版本一致的特点，实现从回滚段中读取表一定时间内操作过的数据，可用来进行数据比对，或者修正意外提交造成的错误数据，该项特性也被称为 Flashback Query 。

一、 Flashback Query 正如前言中所提， Flashback Query 是利用多版本读一致性的特性从 UNDO 表空间读取操作前的记录数据！

什么是多版本读一致性 Oracle 采用了一种非常优秀的设计，通过 undo 数据来确保写不堵塞读，简单的讲，不同的事务在写数据时，会将数据的前映像写入 undo 表空间，这样如果同时有其它事务查询该表数据，则可以通过 undo 表空间中数据的前映像来构造所需的完整记录集，而不需要等待写入的事务提交或回滚。

flashback query 有多种方式构建查询记录集，记录集的选择范围可以基于时间或基于 scn ，甚至可以同时查询出记录在 undo 表空间中不同事务时的前映象。用法与标准查询非常类似，要通过 flashback query 查询 undo 中的撤销数据，最简单的方式只需要在标准查询语句的表名后面跟上 as of timestamp( 基于时间 ) 或 as of scn( 基于 scn) 即可。 as of timestamp|scn 的语法是自 9iR2 后才开始提供支持。

1 、 As of timestamp 的示例：

SQL&gt;~alter session set nls_date_format=&amp;apos;YYYY-MM-DD hh24:mi:ss&amp;apos;;

会话已更改。

SQL&gt; select sysdate from dual;

SYSDATE

-------------------

2009-10-15 19:04:16

SQL&gt; select * from A;

~~~~~~~ID

~~~~~~~----------

~~~~~~~~2

~~~~~~~~1

~~~~~~~~3

~~~~~~~~4

模拟用户误操作，删除数据

SQL&gt; delete from A;

已删除 4 行。

SQL&gt; commit;

提交完成。

SQL&gt; select * from A;

未选定行

查看删除之前的状态： 假设当前距离删除数据已经有 5 分钟左右的话： SQL&gt; select * from A as of timestamp sysdate-5/1440;

~~~~~~~ID

~~~~~~~----------

~~~~~~~~2

~~~~~~~~1

~~~~~~~~3

~~~~~~~~4

或者：

SQL&gt;select * from A as of timestamp to_timestamp(&amp;apos;2009-10-15 19:04:16&amp;apos;,&amp;apos;YYYY-MM-DD hh24:mi:ss&amp;apos;);

~~~~~~~ID

~~~~~~~----------

~~~~~~~~2

~~~~~~~~1

~~~~~~~~3

~~~~~~~~4

用 Flashback Query 恢复之前的数据：

SQL&gt;Insert into A select * from A as of timestamp to_timestamp(&amp;apos;2009-10-15 19:04:16&amp;apos;,&amp;apos;YYYY-MM-DD hh24:mi:ss&amp;apos;);

已创建 4 行。

SQL&gt; COMMIT;

提交完成。

SQL&gt; select * from A;

~~~~~~~ID

~~~~~~~----------

~~~~~~~~2

~~~~~~~~1

~~~~~~~~3

~~~~~~~~4

如上述示例中所表示的， as of timestamp 的确非常易用，但是在某些情况下，我们建议使用 as of scn 的方式执行 flashback query ，比如需要对多个相互有主外键约束的表进行恢复时，如果使用 as of timestamp 的方式，可能会由于时间点不统一的缘故造成数据选择或插入失败，通过 scn 方式则能够确保记录的约束一致性。

2\. As of scn 示例

查看 SCN:SELECT dbms_flashback.get_system_change_number FROM dual;SELECT CURRENT_SCN FROM V$DATABASE;

SQL&gt; SELECT CURRENT_SCN FROM V$DATABASE;

CURRENT_SCN

-----------

1095782

删除数据： SQL&gt; delete from A;

已删除 4 行。

SQL&gt; commit;

提交完成。

~

查看删除之前的状态：

SQL&gt; select * from A as of scn 1095782;

~~~~~~~ID

~~~~----------

~~~~~~~~2

~~~~~~~~1

~~~~~~~~3

~~~~~~~~4

用 Flashback Query 恢复之前的数据：

SQL&gt; insert into A select * from A as of scn 1095782;

已创建 4 行。

SQL&gt; commit;

提交完成。

SQL&gt; select * from A;

~~~~~~~ID

~~~~~~~----------

~~~~~~~~2

~~~~~~~~1

~~~~~~~~3

~~~~~~~~4

事实上， Oracle 在内部都是使用 scn ，即使你指定的是 as of timestamp ， oracle 也会将其转换成 scn ，系统时间标记与 scn 之间存在一张表，即 SYS 下的 SMON_SCN_TIME

SQL&gt; desc sys.smon_scn_time

~ 名称 ~~~~~~~~~~~~~~~~~~~~~~ 是否为空 ? 类型

--------~~~---------------------------

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~NUMBER

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~NUMBER

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~DATE

~~~~~~~~~~~~~~~~~~~~~~~~~~~~NUMBER

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~NUMBER

~~~~~~~~~~~~~~~~~~~~~~NUMBER

~~~~~~~~~~~~~~~~~~~~~~~~RAW(1200)

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~NUMBER

~~~~~~~~~~~~~~~~~~~~~~~~NUMBER

每隔 5 分钟，系统产生一次系统时间标记与 scn 的匹配并存入 sys.smon_scn_time 表，该表中记录了最近 1440 个系统时间标记与 scn 的匹配记录，由于该表只维护了最近的 1440 条记录，因此如果使用 as of timestamp 的方式则只能 flashback 最近 5 天内的数据（假设系统是在持续不断运行并无中断或关机重启之类操作的话）。

注意理解系统时间标记与 scn 的每 5 分钟匹配一次这句话，举个例子，比如 scn:339988,339989 分别匹配 08-05-3013:52:00 和 2008-13:57:00 ，则当你通过 as of timestamp 查询 08-05-30 13:52:00 或 08-05-30 13:56:59 这段时间点 内的时间时， oracle 都会将其匹配为 scn:339988 到 undo 表空间中查找，也就说在这个时间内，不管你指定的时间点是什么，查询返回的都将是 08-05-30 13:52:00 这个时刻的数据。

查看 SCN 和 timestamp 之间的对应关系： select scn,to_char(time_dp,&amp;apos;yyyy-mm-dd hh24:mi:ss&amp;apos;)from sys.smon_scn_time;

~

Flashback version Query

相对于 Flashback Query 只能看到某一点的对象状态， Oracle 10g 引入的 Flashback Version Query 可以看到过去某个时间段内，记录是如何发生变化的。 根据这个历史， DBA 就可以快速的判断数据是在什么时点发生了错误，进而恢复到之前的状态。

先看一个伪列 ORA_ROWSCN.~ 所谓的伪列，就是假的，不存在的数据列，用户创建表时虽然没有指定，但是 Oracle 为了维护而添加的一些内部字段，这些字段可以像普通文件那样的使用。

最熟悉的伪列就是 ROWID ， 它相当于一个指针，指向记录在磁盘上的位置。 ORA_ROWSCN 是 Oracle 10g 新增的，暂且把它看作是记录最后一次被修改时的 SCN 。 Flashback Version Query 就是通过这个伪列来跟踪出记录的变化历史。

举个例子：

SQL&gt; select * from A;

~~~~~~~ID

~~~~~~~----------

~~~~~~~~2

~~~~~~~~1

~~~~~~~~3

~~~~~~~~4

SQL&gt; insert into A values(5);

已创建 1 行。

SQL&gt; select * from A;

~~~~~~~ID

~~~~~~~----------

~~~~~~~~2

~~~~~~~~1

~~~~~~~~3

~~~~~~~~4

~~~~~~~~5

SQL&gt; commit;

提交完成。

SQL&gt; select ora_rowscn, id from A;

ORA_ROWSCN~~~~~~ID

----------~~~~~~~~~~~~----------

~~1098443~~~~~~~~~2

~~1098443~~~~~~~~~1

~~1098443~~~~~~~~~3

~~1098443~~~~~~~~~4

~~1098443~~~~~~~~~5

获取更多的历史信息

SQL&gt;Select versions_xid,versions_startscn,versions_endscn,

~~DECODE(versions_operation,&amp;apos;I&amp;apos;,&amp;apos;Insert&amp;apos;,&amp;apos;U&amp;apos;,&amp;apos;Update&amp;apos;,&amp;apos;D&amp;apos;,&amp;apos;Delete&amp;apos;, &amp;apos;Original&amp;apos;) &quot;Operation&quot;, id from A versions between scn minvalue and maxvalue;

或者

SQL&gt;select xid,commit_scn,commit_timestamp,operation,undo_sql

from flashback_transaction_query q where q.xid in(select versions_xid from B versions between scn 413946 and 413959);

VERSIONS_XID~~~~VERSIONS_STARTSCN VERSIONS_ENDSCN Operatio~~~ID

---------------- ----------------- --------------- -------- ----------

05001A0054020000~~~~~~~~~~1099482~~~~~~~~~~~~~~~~Update~~~~~~~~~~~3

05001A0054020000~~~~~~~~~~1099482~~~~~~~~~~~~~~~~Delete~~~~~~~~~~~3

05001A0054020000~~~~~~~~~~1099482~~~~~~~~~~~~~~~~Delete~~~~~~~~~~~2

05001A0054020000~~~~~~~~~~1099482~~~~~~~~~~~~~~~~Delete~~~~~~~~~~~1

0400150005020000~~~~~~~~~~1098443~~~~~~~~~~~~~~~~Insert~~~~~~~~~~~~5

下面我们来讲下伪列， Flashback Version Query 技术其实有很多伪列，但是 ORA_ROWSCN 是最重要。它记录的是最后一次被修改时的 SCN ， 注意是被提交的修改。如果没有提交，这个伪列不会发生变化。

~

ORA_ROWSCN 缺省是数据块级别的，也就是一个数据块内的所有记录都是一个 ORA_ROWSCN ，数据块内任意一条记录被修改，这个数据库块内的所有记录的 ORA_ROWSCN 都会同时改变。上例的查询结果以证明。

不过我们可以在建表时使用关键字 rowdependencies ， 可以改变这种缺省行为，使用这个关键字后，每条记录都有自己的 ORA_ROWSCN 。

举例：

SQL&gt; create table B (id number(2)) rowdependencies;

表已创建。

SQL&gt; insert into B values(1);

已创建 1 行。

SQL&gt; insert into B values(2);

已创建 1 行。

SQL&gt; insert into B values(3);

已创建 1 行。

SQL&gt; commit;

提交完成。

SQL&gt; select ora_rowscn, id from B;

ORA_ROWSCN~~~~~~~~ID

---------- ----------

~~1100560~~~~~~~~~1

~~1100560~~~~~~~~~2

~~1100560~~~~~~~~~3

此处 SCN 一样，一定很奇怪，这正好说明是最后一次被修改时的 SCN ，如果没有提交，是不会变的，我们重做一下就清楚了。

SQL&gt; analyze table B compute statistics;

表已分析。

SQL&gt; select ora_rowscn, id from B;

ORA_ROWSCN~~~~~~~~ID

---------- ----------

~~1100560~~~~~~~~~1

~~1100560~~~~~~~~~2

~~1100560~~~~~~~~~3

SQL&gt; delete from B;

已删除 4 行。

SQL&gt; select ora_rowscn, id from B;

未选定行

SQL&gt; insert into B values(1);

已创建 1 行。

SQL&gt; commit;

提交完成。

SQL&gt; insert into B values(2);

已创建 1 行。

SQL&gt; commit;

提交完成。

SQL&gt; select ora_rowscn, id from B;

ORA_ROWSCN~~~~~~~~ID

---------- ----------

~~1100723~~~~~~~~~1

~~1100729~~~~~~~~~2

~

~

~

~

~

~

Flashback Transaction Query

Flashback Transaction Query 也是使用 UNDO 信息来实现。利用这个功能可以查看某个事务执行的所有变化，它需要访问 flashback_transaction_query 视图，这个视图的 XID 列代表事务 ID ，利用这个 ID 可以区分特定事务发生的所有数据变化。

示例：

SQL&gt; insert into B values(3);

已创建 1 行。

SQL&gt; commit;

提交完成。

SQL&gt; select * from B;

~~~~~~~ID

~~~~~~----------

~~~~~~~~1

~~~~~~~~2

~~~~~~~~3

查看视图，每个事务都对应相同的 XID

SQL&gt;Select xid,operation,commit_scn,undo_sql from flashback_transaction_query where xid in (

Select versions_xid from B versions between scn minvalue and maxvalue);

或者

SQL&gt;select xid,commit_scn,commit_timestamp,operation,undo_sql

from flashback_transaction_query q where q.xid in(select versions_xid from B versions between scn 413946 and 413959);

XID~~~~~~~~~~~~~OPERATION~~~~~~~~~~~~~~~~~~~~~~~COMMIT_SCN

---------------- -------------------------------- ----------

UNDO_SQL

--------------------------------------------------------------------------------

03001C006A020000 DELETE~~~~~~~~~~~~~~~~~~~~~~~~~~~~~1100723

insert into &quot;SYS&quot;.&quot;B&quot;(&quot;ID&quot;) values (&amp;apos;4&amp;apos;);

03001C006A020000 DELETE~~~~~~~~~~~~~~~~~~~~~~~~~~~~~1100723

insert into &quot;SYS&quot;.&quot;B&quot;(&quot;ID&quot;) values (&amp;apos;3&amp;apos;);

03001C006A020000 DELETE~~~~~~~~~~~~~~~~~~~~~~~~~~~~~1100723

insert into &quot;SYS&quot;.&quot;B&quot;(&quot;ID&quot;) values (&amp;apos;2&amp;apos;);

~

Flashback Table

注意 SYS 用户不支持闪回，这点前面已经说明过。

Flashback Table 也是使用 UNDO tablespace 的内容来实现对数据的回退。该命令相对简单，输入： flashback table table_name to scn(to timestamp) 即可。

注意：如果想要对表进行 flashback ，必须允许表的 row movement.

Alter table table_name row movement;

~

要查看某表是否启用 row movement ，可以到 user_tables 中查询 ( 或 all_tables,dba_tables) ，

例如： SQL&gt; select row_movement from user_tables where table_name=&amp;apos;C&amp;apos;;ROW_MOVE--------ENABLED

要启用或禁止某表 row movement ，可以通过下列语句：

-- 启用 JSSWEB&gt; ALTER TABLE table_name ENABLE ROW MOVEMENT; 表已更改。 -- 禁止 JSSWEB&gt; ALTER TABLE table_name DISABLE ROW MOVEMENT; 表已更改。

举例：

SQL&gt; create table C (id number(2));

表已创建。

SQL&gt; insert into C values(1);

已创建 1 行。

SQL&gt; insert into C values(2);

已创建 1 行。

SQL&gt; commit;

提交完成。

SQL&gt; select * from c;

~~~~~~~ID

----------

~~~~~~~~1

~~~~~~~~2

SQL&gt; alter session set nls_date_format=&quot;yyyy-mm-dd hh24:mi:ss&quot;;

会话已更改。

SQL&gt; select sysdate from dual;

SYSDATE

-------------------

2009-10-15 21:17:47

SQL&gt; select current_scn from v$database;

CURRENT_SCN

-----------

~~~~~1103864

删除数据并恢复

SQL&gt; delete from C;

已删除 2 行。

SQL&gt; commit;

提交完成。

SQL&gt; alter table c enable row movement;

表已更改。

SQL&gt; flashback table c to scn~1103864;

闪回完成。

或者：

SQL&gt; flashback table c to timestamp to_timestamp(&amp;apos;2009-10-15 21:17:47&amp;apos;,&amp;apos;yyyy-mm-

dd hh24:mi:ss&amp;apos;);

SQL&gt; select * from c;

~~~~~~~ID

----------

~~~~~~~~1

~~~~~~~~2

Flashback table 命令支持同时操作多个表，表名中间以逗号分隔即可，如果你执行一条 flashback table 命令时同时指定了多个表，要记住单个 flashback table 是在同一个事务中，因此这些表的恢复操作要么都成功，要么都失败。

如：

flashback table a,b ,c to scn 1103864;

~

一些注意事项

1\. 基于 undo 的表恢复，需要注意 DDL 操作的影响 第三个就是修改并提交过数据之后，对表做过 DDL 操作，包括： drop/modify 列 , move 表 , drop 分区 ( 如果有的话 ), truncate table/partition ，这些操作会另 undo 表空间中的撤销数据失效，对于执行过这些操作的表应用 flashback query 会触发 ORA-01466 错误。另外一些表结构修改语句虽然并不会影响到 undo 表空间中的撤销记录，但有可能因表结构修改导致 undo 中重做记录无法应用的情况，比如对于增加了约束，而 flashback query 查询出的 undo 记录已经不符合新建的约束条件，这个时候直接恢复显然不可能成功，你要么暂时 disable 约束，要么通过适当逻辑，对要恢复的数据进行处理之后，再执行恢复。 另外， flashback query 对 v$tables,x$tables 等动态性能视图无效，不过对于 dba_*,all_*,user_* 等数据字典是有效的。同时该特性也完全支持访问远端数据库，比如 select * from tbl@dblink as of scn 360; 的形式。

~

2\. 基于 undo 的表恢复， flashback table 实际上做的也是 dml 操作 ( 会在被操作的表上加 dml 锁 ) ，因此还需要注意 triggers 对其的影响，默认情况下， flashback table to scn/timestamp 在执行时会自动 disable 掉与其操作表相差的 triggers ，如果你希望在此期间 trigger 能够继续发挥做用，可以在 flashback table 后附加 ENABLE TRIGGERS 子句。

~

补充：

什么是 Automatic Undo Management( 自动撤销管理表空间 ) 提到自动撤销管理表空间，就不得不提手动管理的回滚段。在 9i 之前，回滚段的管理和监控是需要 dba 手工介入的，创建合适的回滚段是件非常耗费 dba 精力的事情，你可能需要不断关注 oracle 运行状况很长一阵子时间后，通过不断的调整才能基本确认一段时期内回滚段的大小，一旦回滚段创建的不合适，就极有可能引起性能问题甚至错误，比如 ora-1555 就是典型的回滚段设置不合适触发的。 9i 之后呢 ( 含 9i) ， oracle 为了清晰它的整个概念，取消了回滚段这个说法 ( 实际上并未取消回滚段 ) ，而完全以 undo 来代替，这也它正好与 redo 相对应，一个重做，一个撤销。回滚段可以不再由 dba 手工介入，而是完全由它自己在运行时自动分配，这在一定程度上即解放了 dba ，也确实起到了提高性能的作用，比如采用自动管理表空间就可以最大程序的降低 ora-1555 发生的机率 ( 注意是降低，不是避免，我们不可能创建一个无限大的回滚段， ora-1555 也并不完全是回滚段造成的，关于 ora-1555 的问题这里就不深入讨论了，互联网上已经有太多文章描述和介绍该问题及解决方案 )

是否起用自动管理的撤销表空间由二个初始化参数决定： UNDO_MANAGEMENT ：值为 AUTO 表示使用了自动撤销管理表空间， MANUAL 则表示手动管理 UNDO_TABLESPACE ：当 UNDO_MANAGEMENT 值为 AUTO 时，该参数用来指定当前的 undo 表空间名称。 undo 表空间的大小，直接影响到 flashback query 的查询能力，因为多版本查询所依赖的 undo 数据都存储在 undo 表空间中，该表空间越大，所能够存储的 undo 数据自然也越多，如果该表空间可用空间非常小，别说 flashback 了，恐怕正常查询都有可能触发 ora-1555 吧。

初始化参数 UNDO_RETENTION 该参数用来指定 undo 记录保存的最长时间，以秒为单位，是个动态参数，完全可以在实例运行时随时修改通常默认是 900 秒，也就是 15 分钟。 一定要注意， undo_retention 只是指定 undo 数据的过期时间，并不是说， undo 中的数据一定会在 undo 表空间中保存 15 分钟，比如说刚一个新事务开始的时候，如果 undo 表空间已经被写满，则新事务的数据会自动覆盖已提交事务的数据，而不管这些数据是否已过期，因此呢，这就又关联回了第一点，当你创建 一个自动管理的 undo 表空间时，还要注意其空间大小，要尽可能保证 undo 表空间有足够的存储空间。 同时还要注意，也并不是说， undo_retention 中指定的时间一过，已经提交事务中的数据就立刻无法访问，它只是失效，只要不被别的事务覆盖，它会仍然存在，并可随时被 flashback 特性引用。如果你的 undo 表空间足够大，而数据库又不是那么繁忙，那么其实 undo_retention 参数的值并不会影响到你，哪怕你设置成 1 ，只要没有事务去覆盖 undo 数据，它就会持续有效。因此呢，这里还是那句话，要注意 undo 表空间的大小，保证其有足够的存储空间。

只有在一种情况下， undo 表空间能够确保 undo 中的数据在 undo_retention 指定时间过期前一定有效，就是为 undo 表空间指定 Retention Guarantee ，指定之后， oracle 对于 undo 表空间中未过期的 undo 数据不会覆盖，

例如： SQL&gt; Alter tablespace undotbs1 retention guarantee; 如果想禁止 undo 表空间 retention guarantee ，

例如： SQL&gt; Alter tablespace undotbs1 retention noguarantee;

[http://blog.csdn.net/tianlesoftware](http://blog.csdn.net/tianlesoftware)