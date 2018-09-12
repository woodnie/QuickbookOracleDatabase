### OpenLab info {#openlab-info}

vnc server: oracle/192.168.0.156

user/pw ：oracle/orcale

ftp server:192.168.0.254

黄佳惠        138 1813 5547

       15517 52824

QQ        657574866

tel        13818460986

36358303

1月13号

考试号：

047

042

043

#### 11/18 {#11-18}

QQ:1551752824

1\. 约束

非空

唯一：可出现任意多空值

主键

外键

条件

查询约束信息：

select owner,constraint_name,constraint_type,table_name from user_constraints;

创建外键：

删除外键：

创建view

排序：

同意词：

同义词：

同义词不能和对象重名

公共同义词

序列：

硬解析

变量绑定

show parameter cursor

alter currsor_shaering=similar

wegligic

gildengate 数据同步

1.SGA：（show sga\show paramete? pool\）

1.1共享池

1.1.1 库缓存

1.1.2 数据字典缓存

1.2 数据缓存区

1.3 redo(LGWR)

1.4 lage pool：外部程序使用（数据导入）

1.5 java池

2.后台进程

2.1SMON:前滚，open，回滚

2.2PMON:向监听注册，监控客户端，内存回收

2.3RECO：联机事务处理

4.DBWn:把共享池中的脏数据写入磁盘

5.CKPT:检查点

6.LGWR：将联机重做日志缓存中的数据写入联机重做日志文件

7.ARCn:归档日志

$ORACLE_HOME/oradate:数据文件 .dbf

控制文件：ctl

联机重做日志文件.log

$ORACLE_HOEM/dbs：

prapw******** 口令文件

spfile******* 参数文件

盖国强

韩思捷

何明

#### 11/25 {#11-25}

v$instance

v$datebase

v$parameter

v$controlfile

v$datefile

v$logfile

v$file

show sga

select name,ty[e,value from v$parameter where name like &amp;apos;sga%&amp;apos;

show parameter spfile

静态参数文件        pfile 9i 可以手动改动

db_name= 可以起到数据库（不是open）

动态参数文件        spfile 不能手动修改

create pfile=&amp;apos;/../xxxpfile&amp;apos; from spfile

creat spfile=&amp;apos;/../xxspfile&amp;apos; from pfile

create spfile=&amp;apos;/home/oracle/ from memory;

startup pfile=&amp;apos;/../xxxpfile&amp;apos;

alter system set parameter=value scope=spfile

scop:both memorey spfile

startup:

nmount:需要参数文件

分配sga

启动后天进程

mount:需要控制文件

数据文件

日志文件

open：数据文件scn,日志文件scn

shutdown:

immdediate：

abort:断电

nomal:等待所有用户关闭连接

增加control_file:

shutdown immediate

startup nomountalter

system set control_files=&amp;apos;/opt/oracle11g/oradata/oracle1/control01.ctl,/opt/oracle11g/flash_recovery_area/oracle1/control02.ctl,/home/oracle/backup/control03.ctl&amp;apos; scope=spfile;

shutdown immediate

startup nomount

show parameter control_files;

[oracle@localhost ~]$ sqlplus scott/tiger@192.168.1.6:2012/king

ls

netmgr 本地服务

ls

em

#### 12/02 {#12-02}

select name from v$datafile;

select table_name,tablespace_name from user_tables;

dba_data_file

1.

create tablespace

create undo tablesapce

create temporary tablespace

create TEMPORARY tablespace

1.修改user表空间数据文件位置

2.修改sys表空间数据文件位置

startup mount

cp

3\. 删除数据文件

5.OMF

6\. 修改undo表空间

7.

alter   move tablespace

8.增加数据文件

9.

10 冷备份

select &amp;apos;host cp &amp;apos;|| name ||&amp;apos; /home/oracle/backup/&amp;apos; from v$datafile;

select &amp;apos;host cp &amp;apos;|| member ||&amp;apos; /home/oracle/backup/&amp;apos; from v$logfile;

select &amp;apos;host cp &amp;apos;|| name ||&amp;apos; /home/oracle/backup/&amp;apos; from v$controlfile;

show parameter spfile;

10 口令

口令文件控制文件：

$ORACLE_HOME/network/admin/

口令文件：

$oracle_home/ dbs

#### 12/09 {#12-09}

1\. 创建用户

create user user001 identified by oracle default tablespace users temporary tablespace temp quota 10M on users password expire;

temporary tablespace 不能配置配额！

2，修改配额

dbs_users

dbs_ts_quotas;

3,dorp user

user-schema(方案，包含对象)

drop user usename

cascade 同时删除改用schema及其对象！

4.查询权限

5

系统权限：with admin option;平级（）

对象权限：with grant option;级联（收回权限时级联收回）

6 role

connect,resource

7\. 归档日志

8.修改归档模式

shutdown immediate

startup mount

alte database archivelog

alter datease open

9\. 查看

10 。切换日志

11\. 日志切换

12\. 日志位置：

默认 oracle_base/flash_recovery_area/SID/archivelog/

12.

13.热备

13.1

13.2

13.3

13.4

++++

alter tablespace users online;

alter tablespace users offline;

alter database datafile 4 online;

alter database recover datafile &amp;apos;/opt/oracle11g/oradata/oracle1/user01.dbf&amp;apos;;

+++

15.数据导入导出

exp

imp

16.

17\. 数据泵

17.1 创建目录        

17.2 查看目录

17.3 目录授权

17.3 导出数据

17.4 par文件内容

17.4.2

17.2

#### 12/16 {#12-16}

1.

RMAN&gt; show all;

RMAN configuration parameters for database with db_unique_name ORACLE1 are:

CONFIGURE RETENTION POLICY TO REDUNDANCY 1; # default

CONFIGURE BACKUP OPTIMIZATION OFF; # default

CONFIGURE DEFAULT DEVICE TYPE TO DISK; # default

CONFIGURE CONTROLFILE AUTOBACKUP OFF; # default

CONFIGURE CONTROLFILE AUTOBACKUP FORMAT FOR DEVICE TYPE DISK TO &amp;apos;%F&amp;apos;; # default

CONFIGURE DEVICE TYPE DISK PARALLELISM 1 BACKUP TYPE TO BACKUPSET; # default

CONFIGURE DATAFILE BACKUP COPIES FOR DEVICE TYPE DISK TO 1; # default

CONFIGURE ARCHIVELOG BACKUP COPIES FOR DEVICE TYPE DISK TO 1; # default

CONFIGURE MAXSETSIZE TO UNLIMITED; # default

CONFIGURE ENCRYPTION FOR DATABASE OFF; # default

CONFIGURE ENCRYPTION ALGORITHM &amp;apos;AES128&amp;apos;; # default

CONFIGURE COMPRESSION ALGORITHM &amp;apos;BASIC&amp;apos; AS OF RELEASE &amp;apos;DEFAULT&amp;apos; OPTIMIZE FOR LOAD TRUE ; # default

CONFIGURE ARCHIVELOG DELETION POLICY TO NONE; # default

CONFIGURE SNAPSHOT CONTROLFILE NAME TO &amp;apos;/opt/oracle11g/product/11.2.0/db_1/dbs/snapcf_oracle1.f&amp;apos;; # default

1.1# 修改冗余为5

RMAN&gt;        CONFIGURE RETENTION POLICY TO REDUNDANCY 5

1.2 失效窗口7天

2.rman

database

tablespace

datefile

spfile

current controlfile

archivelog all;

backup ID;

2.1

2.1 控制文件

2.2 参数文件

2.3 规定日志

2.4

2.3.

2.4\. 无返回结果

3.

RMAN&gt; report need backup

4\. 增量备份：

1.差异

2.累积

5\. 块儿改变跟踪

5.1查询

5.2 启用

5.3关闭

6\. 备份脚本

61\. 分片备份

6.2 同时备份多份

#### 12/23 {#12-23}

backup database plus archivelog;

1 切归档

2.备份所有归档日志

3.

4.切归档

5.备份自归档以来的归档日志

RMAN&gt; connect target /

connected to target database: ORACLE1 (DBID=467798412)

set dbid=

restore control file from autobackup;

alter database mount;

recover database;

alter database ope resetlogs;

piece handle=

restore controlfile from &amp;apos;/opt/oracle11g/flash_recovery_area/ORACLE1/backupset/2012_12_23/o1_mf_ncnnf_TAG20121223T164826_8ffkby7g_.bkp&amp;apos;;

select  file_id,tablespace_name from dba_datafile;

2.catalog:

2.1

3.

3\. 日志备份

删除后，修复

删除当前日志文件

回复日志直到最近可恢复

5.flushback

pugre

#### 12/30 {#12-30}

1.flashback

1.flashback

1.1开启undo sql

开启10g不用添加，11需要开启

增长undo_retention

2\. 改数据

create table emp01 as select * from emp;

update emp01 set  sal=9999;

commit;

此时无法rollback

3.查询事物ＩＤ

4.

4.1查询版本号

SCOTT@oracle1 30-DEC-12 &gt;select versions_xid,ename,sal from emp01 versions between scn minvalue and maxvalue;

4.2 查询undo sql

SYS@oracle1 30-DEC-12 &gt;select operation,undo_sql from flashback_transaction_query where xid=hextoraw(&amp;apos;0800170010040000&amp;apos;);

4.3

4.3.1

4.3.2

查询start_sn

SYS@oracle1 30-DEC-12 &gt;select operation,undo_sql,start_scn from flashback_transaction_query where xid=hextoraw(&amp;apos;0800170010040000&amp;apos;);

4\. 全库闪回

4.1

SYS@oracle1 30-DEC-12 &gt;select log_mode from v$database;

LOG_MODE

------------

ARCHIVELOG

4.2

SYS@oracle1 30-DEC-12 &gt;show parameter flashback

NAME                                 TYPE        VALUE

------------------------------------ ----------- ------------------------------

db_flashback_retention_target        integer     1440

4.3

shutdown immediate

startup mout

alter database flashback on

alter database open

4.4查开启yes

4.5

drop user scott

shutdown

4,6

4.6.1

查询时间

scn

4.7

4.7.1使用时间闪回        

4.7.1使用scn

5\. ran克隆

1.手动建立参数文件

oracle_home/dbs

2.启动到nomount

3.切归档

4\. 全库备份

backup database plus archivelog;

5 克隆

6.

#### 01/13 {#01-13}

ASM

1.

oracleasm configure -i

orcleasm init

2.

3.

4

**数据导入导出：**

1.sqlldr

               2.

3.

row=100;指定行数

direct=ture 直接路径加载(一次性读完文件)

streamsize 指定一次读取数据大小，date_cache 指定数据转换，默认1000行

3.1实例：

4.spool数据导出

sql&gt;spoll

1\. 外部表

1.dir

2.table

3.

SCOTT@oracle1 13-JAN-13 &gt;list

 1  create table ext01

 2  (name varchar2(30),job varchar2(30),sal number)

 3  organization external

 4  (type oracle_loder default directory wooddir access parameters

 5  (fields terminated by &quot;,&quot; (name,job,sal))

 6* location (&amp;apos;tb01.dat&amp;apos;))

SCOTT@oracle1 13-JAN-13 &gt;1 create table ext02

SCOTT@oracle1 13-JAN-13 &gt;list

 1  create table ext02

 2  (name varchar2(30),job varchar2(30),sal number)

 3  organization external

 4  (type oracle_loder default directory wooddir access parameters

 5  (fields terminated by &quot;,&quot; (name,job,sal))

 6* location (&amp;apos;tb01.dat&amp;apos;))

SCOTT@oracle1 13-JAN-13 &gt;/

create table ext02

            *

ERROR at line 1:

ORA-30657: operation not supported on external organized table

SCOTT@oracle1 13-JAN-13 &gt;4

 4* (type oracle_loder default directory wooddir access parameters

SCOTT@oracle1 13-JAN-13 &gt;c /loder/loader

 4* (type oracle_loader default directory wooddir access parameters

SCOTT@oracle1 13-JAN-13 &gt;list

 1  create table ext02

 2  (name varchar2(30),job varchar2(30),sal number)

 3  organization external

 4  (type oracle_loader default directory wooddir access parameters

 5  (fields terminated by &quot;,&quot; (name,job,sal))

 6* location (&amp;apos;tb01.dat&amp;apos;))

SCOTT@oracle1 13-JAN-13 &gt;/

#### rman duplicate {#rman-duplicate}

initdol.ora

db_name=dol

db_block_size=8192

control_files=(&amp;apos;/u01/app/oracle/oradata/dol/c01.ctl&amp;apos;,&amp;apos;/u01/app/oracle/oradata/dol/c02.ctl&amp;apos;)

db_file_name_convert=(&amp;apos;/u01/app/oracle/oradata/ck/&amp;apos;,&amp;apos;/u01/app/oracle/oradata/dol/&amp;apos;)

log_file_name_convert=(&amp;apos;/u01/app/oracle/oradata/ck/&amp;apos;,&amp;apos;/u01/app/oracle/oradata/dol/&amp;apos;)

rman duplicate01

[oracle@oracle dol]$ sqlplus / as sysdba

SQL*Plus: Release 11.2.0.1.0 Production on Sun Dec 30 17:35:48 2012

Copyright (c) 1982, 2009, Oracle.  All rights reserved.

Connected to:

Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - Production

With the Partitioning, OLAP, Data Mining and Real Application Testing options

SYS@ck&gt;select name from v$datafile;

NAME

--------------------------------------------------------------------------------

/u01/app/oracle/oradata/ck/system01.dbf

/u01/app/oracle/oradata/ck/sysaux01.dbf

/u01/app/oracle/oradata/ck/undotbs01.dbf

/u01/app/oracle/oradata/ck/users01.dbf

SYS@ck&gt;select member from v$logfile;

MEMBER

--------------------------------------------------------------------------------

/u01/app/oracle/oradata/ck/redo03.log

/u01/app/oracle/oradata/ck/redo02.log

/u01/app/oracle/oradata/ck/redo01.log

SYS@ck&gt;exit

Disconnected from Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - Production

With the Partitioning, OLAP, Data Mining and Real Application Testing options

[oracle@oracle dol]$ clear

[oracle@oracle dol]$ export ORACLE_SID=dol

[oracle@oracle dol]$ sqlplus / as sysdba

SQL*Plus: Release 11.2.0.1.0 Production on Sun Dec 30 17:37:10 2012

Copyright (c) 1982, 2009, Oracle.  All rights reserved.

Connected to an idle instance.

SYS@dol&gt;startup nomount

ORACLE instance started.

Total System Global Area  150667264 bytes

Fixed Size                  1335080 bytes

Variable Size              92274904 bytes

Database Buffers           50331648 bytes

Redo Buffers                6725632 bytes

SYS@dol&gt;exit

Disconnected from Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - Production

With the Partitioning, OLAP, Data Mining and Real Application Testing options

[oracle@oracle dol]$ rman target sys/oracle@ck auxiliary /

Recovery Manager: Release 11.2.0.1.0 - Production on Sun Dec 30 17:41:21 2012

Copyright (c) 1982, 2009, Oracle and/or its affiliates.  All rights reserved.

connected to target database: CK (DBID=820859332)

connected to auxiliary database: DOL (not mounted)

RMAN&gt; duplicate target database to dol;

Starting Duplicate Db at 30-DEC-12

using target database control file instead of recovery catalog

allocated channel: ORA_AUX_DISK_1

channel ORA_AUX_DISK_1: SID=96 device type=DISK

contents of Memory Script:

{

  sql clone &quot;create spfile from memory&quot;;

}

executing Memory Script

sql statement: create spfile from memory

contents of Memory Script:

{

  shutdown clone immediate;

  startup clone nomount;

}

executing Memory Script

Oracle instance shut down

connected to auxiliary database (not started)

Oracle instance started

Total System Global Area     150667264 bytes

Fixed Size                     1335080 bytes

Variable Size                 92274904 bytes

Database Buffers              50331648 bytes

Redo Buffers                   6725632 bytes

contents of Memory Script:

{

  sql clone &quot;alter system set  db_name =

&amp;apos;&amp;apos;CK&amp;apos;&amp;apos; comment=

&amp;apos;&amp;apos;Modified by RMAN duplicate&amp;apos;&amp;apos; scope=spfile&quot;;

  sql clone &quot;alter system set  db_unique_name =

&amp;apos;&amp;apos;DOL&amp;apos;&amp;apos; comment=

&amp;apos;&amp;apos;Modified by RMAN duplicate&amp;apos;&amp;apos; scope=spfile&quot;;

  shutdown clone immediate;

  startup clone force nomount

  restore clone primary controlfile;

  alter clone database mount;

}

executing Memory Script

sql statement: alter system set  db_name =  &amp;apos;&amp;apos;CK&amp;apos;&amp;apos; comment= &amp;apos;&amp;apos;Modified by RMAN duplicate&amp;apos;&amp;apos; scope=spfile

sql statement: alter system set  db_unique_name =  &amp;apos;&amp;apos;DOL&amp;apos;&amp;apos; comment= &amp;apos;&amp;apos;Modified by RMAN duplicate&amp;apos;&amp;apos; scope=spfile

Oracle instance shut down

Oracle instance started

Total System Global Area     150667264 bytes

Fixed Size                     1335080 bytes

Variable Size                 92274904 bytes

Database Buffers              50331648 bytes

Redo Buffers                   6725632 bytes

Starting restore at 30-DEC-12

allocated channel: ORA_AUX_DISK_1

channel ORA_AUX_DISK_1: SID=95 device type=DISK

channel ORA_AUX_DISK_1: starting datafile backup set restore

channel ORA_AUX_DISK_1: restoring control file

channel ORA_AUX_DISK_1: reading from backup piece /u01/app/oracle/flash_recovery_area/CK/autobackup/2012_12_30/o1_mf_s_803410837_8g030orr_.bkp

channel ORA_AUX_DISK_1: piece handle=/u01/app/oracle/flash_recovery_area/CK/autobackup/2012_12_30/o1_mf_s_803410837_8g030orr_.bkp tag=TAG20121230T174037

channel ORA_AUX_DISK_1: restored backup piece 1

channel ORA_AUX_DISK_1: restore complete, elapsed time: 00:00:01

output file name=/u01/app/oracle/oradata/dol/c01.ctl

output file name=/u01/app/oracle/oradata/dol/c02.ctl

Finished restore at 30-DEC-12

database mounted

contents of Memory Script:

{

  set until scn  813308;

  set newname for datafile  1 to

&quot;/u01/app/oracle/oradata/dol/system01.dbf&quot;;

  set newname for datafile  2 to

&quot;/u01/app/oracle/oradata/dol/sysaux01.dbf&quot;;

  set newname for datafile  3 to

&quot;/u01/app/oracle/oradata/dol/undotbs01.dbf&quot;;

  set newname for datafile  4 to

&quot;/u01/app/oracle/oradata/dol/users01.dbf&quot;;

  restore

  clone database

  ;

}

executing Memory Script

executing command: SET until clause

executing command: SET NEWNAME

executing command: SET NEWNAME

executing command: SET NEWNAME

executing command: SET NEWNAME

Starting restore at 30-DEC-12

using channel ORA_AUX_DISK_1

channel ORA_AUX_DISK_1: starting datafile backup set restore

channel ORA_AUX_DISK_1: specifying datafile(s) to restore from backup set

channel ORA_AUX_DISK_1: restoring datafile 00001 to /u01/app/oracle/oradata/dol/system01.dbf

channel ORA_AUX_DISK_1: restoring datafile 00002 to /u01/app/oracle/oradata/dol/sysaux01.dbf

channel ORA_AUX_DISK_1: restoring datafile 00003 to /u01/app/oracle/oradata/dol/undotbs01.dbf

channel ORA_AUX_DISK_1: restoring datafile 00004 to /u01/app/oracle/oradata/dol/users01.dbf

channel ORA_AUX_DISK_1: reading from backup piece /u01/app/oracle/flash_recovery_area/CK/backupset/2012_12_30/o1_mf_nnndf_TAG20121230T174004_8g02zo12_.bkp

channel ORA_AUX_DISK_1: piece handle=/u01/app/oracle/flash_recovery_area/CK/backupset/2012_12_30/o1_mf_nnndf_TAG20121230T174004_8g02zo12_.bkp tag=TAG20121230T174004

channel ORA_AUX_DISK_1: restored backup piece 1

channel ORA_AUX_DISK_1: restore complete, elapsed time: 00:00:25

Finished restore at 30-DEC-12

contents of Memory Script:

{

  switch clone datafile all;

}

executing Memory Script

datafile 1 switched to datafile copy

input datafile copy RECID=5 STAMP=803411000 file name=/u01/app/oracle/oradata/dol/system01.dbf

datafile 2 switched to datafile copy

input datafile copy RECID=6 STAMP=803411000 file name=/u01/app/oracle/oradata/dol/sysaux01.dbf

datafile 3 switched to datafile copy

input datafile copy RECID=7 STAMP=803411000 file name=/u01/app/oracle/oradata/dol/undotbs01.dbf

datafile 4 switched to datafile copy

input datafile copy RECID=8 STAMP=803411000 file name=/u01/app/oracle/oradata/dol/users01.dbf

contents of Memory Script:

{

  set until scn  813308;

  recover

  clone database

   delete archivelog

  ;

}

executing Memory Script

executing command: SET until clause

Starting recover at 30-DEC-12

using channel ORA_AUX_DISK_1

starting media recovery

archived log for thread 1 with sequence 9 is already on disk as file /u01/app/oracle/flash_recovery_area/CK/archivelog/2012_12_30/o1_mf_1_9_8g030n08_.arc

archived log for thread 1 with sequence 10 is already on disk as file /u01/app/oracle/flash_recovery_area/CK/archivelog/2012_12_30/o1_mf_1_10_8g0312lc_.arc

archived log for thread 1 with sequence 11 is already on disk as file /u01/app/oracle/flash_recovery_area/CK/archivelog/2012_12_30/o1_mf_1_11_8g031b4f_.arc

archived log for thread 1 with sequence 12 is already on disk as file /u01/app/oracle/flash_recovery_area/CK/archivelog/2012_12_30/o1_mf_1_12_8g031cnl_.arc

archived log file name=/u01/app/oracle/flash_recovery_area/CK/archivelog/2012_12_30/o1_mf_1_9_8g030n08_.arc thread=1 sequence=9

archived log file name=/u01/app/oracle/flash_recovery_area/CK/archivelog/2012_12_30/o1_mf_1_10_8g0312lc_.arc thread=1 sequence=10

archived log file name=/u01/app/oracle/flash_recovery_area/CK/archivelog/2012_12_30/o1_mf_1_11_8g031b4f_.arc thread=1 sequence=11

archived log file name=/u01/app/oracle/flash_recovery_area/CK/archivelog/2012_12_30/o1_mf_1_12_8g031cnl_.arc thread=1 sequence=12

media recovery complete, elapsed time: 00:00:00

Finished recover at 30-DEC-12

contents of Memory Script:

{

  shutdown clone immediate;

  startup clone nomount;

  sql clone &quot;alter system set  db_name =

&amp;apos;&amp;apos;DOL&amp;apos;&amp;apos; comment=

&amp;apos;&amp;apos;Reset to original value by RMAN&amp;apos;&amp;apos; scope=spfile&quot;;

  sql clone &quot;alter system reset  db_unique_name scope=spfile&quot;;

  shutdown clone immediate;

  startup clone nomount;

}

executing Memory Script

database dismounted

Oracle instance shut down

connected to auxiliary database (not started)

Oracle instance started

Total System Global Area     150667264 bytes

Fixed Size                     1335080 bytes

Variable Size                 92274904 bytes

Database Buffers              50331648 bytes

Redo Buffers                   6725632 bytes

sql statement: alter system set  db_name =  &amp;apos;&amp;apos;DOL&amp;apos;&amp;apos; comment= &amp;apos;&amp;apos;Reset to original value by RMAN&amp;apos;&amp;apos; scope=spfile

sql statement: alter system reset  db_unique_name scope=spfile

Oracle instance shut down

connected to auxiliary database (not started)

Oracle instance started

Total System Global Area     150667264 bytes

Fixed Size                     1335080 bytes

Variable Size                 92274904 bytes

Database Buffers              50331648 bytes

Redo Buffers                   6725632 bytes

sql statement: CREATE CONTROLFILE REUSE SET DATABASE &quot;DOL&quot; RESETLOGS ARCHIVELOG

 MAXLOGFILES     16

 MAXLOGMEMBERS      3

 MAXDATAFILES      100

 MAXINSTANCES     8

 MAXLOGHISTORY      292

LOGFILE

 GROUP  1 ( &amp;apos;/u01/app/oracle/oradata/dol/redo01.log&amp;apos; ) SIZE 50 M  REUSE,

 GROUP  2 ( &amp;apos;/u01/app/oracle/oradata/dol/redo02.log&amp;apos; ) SIZE 50 M  REUSE,

 GROUP  3 ( &amp;apos;/u01/app/oracle/oradata/dol/redo03.log&amp;apos; ) SIZE 50 M  REUSE

DATAFILE

 &amp;apos;/u01/app/oracle/oradata/dol/system01.dbf&amp;apos;

CHARACTER SET ZHS16GBK

contents of Memory Script:

{

  set newname for tempfile  1 to

&quot;/u01/app/oracle/oradata/dol/temp01.dbf&quot;;

  switch clone tempfile all;

  catalog clone datafilecopy  &quot;/u01/app/oracle/oradata/dol/sysaux01.dbf&quot;,

&quot;/u01/app/oracle/oradata/dol/undotbs01.dbf&quot;,

&quot;/u01/app/oracle/oradata/dol/users01.dbf&quot;;

  switch clone datafile all;

}

executing Memory Script

executing command: SET NEWNAME

renamed tempfile 1 to /u01/app/oracle/oradata/dol/temp01.dbf in control file

cataloged datafile copy

datafile copy file name=/u01/app/oracle/oradata/dol/sysaux01.dbf RECID=1 STAMP=803411016

cataloged datafile copy

datafile copy file name=/u01/app/oracle/oradata/dol/undotbs01.dbf RECID=2 STAMP=803411016

cataloged datafile copy

datafile copy file name=/u01/app/oracle/oradata/dol/users01.dbf RECID=3 STAMP=803411016

datafile 2 switched to datafile copy

input datafile copy RECID=1 STAMP=803411016 file name=/u01/app/oracle/oradata/dol/sysaux01.dbf

datafile 3 switched to datafile copy

input datafile copy RECID=2 STAMP=803411016 file name=/u01/app/oracle/oradata/dol/undotbs01.dbf

datafile 4 switched to datafile copy

input datafile copy RECID=3 STAMP=803411016 file name=/u01/app/oracle/oradata/dol/users01.dbf

contents of Memory Script:

{

  Alter clone database open resetlogs;

}

executing Memory Script

database opened

Finished Duplicate Db at 30-DEC-12

RMAN&gt; exit

Recovery Manager complete.

[oracle@oracle dol]$ su -

口令：

[root@oracle ~]# gedit /etc/oratab

[root@oracle ~]# exit

logout

[oracle@oracle dol]$ . oraenv

ORACLE_SID = [dol] ? dol

The Oracle base for ORACLE_HOME=/u01/app/oracle/product/11.2.0/dbhome_1 is /u01/app/oracle

[oracle@oracle dol]$ sqlplus / as sysdba

SQL*Plus: Release 11.2.0.1.0 Production on Sun Dec 30 17:44:36 2012

Copyright (c) 1982, 2009, Oracle.  All rights reserved.

Connected to:

Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - Production

With the Partitioning, OLAP, Data Mining and Real Application Testing options

SYS@dol&gt;select instance_name,status from v$instance;

INSTANCE_NAME    STATUS

---------------- ------------

dol              OPEN

SYS@dol&gt;select name from v$datafile;

NAME

--------------------------------------------------------------------------------

/u01/app/oracle/oradata/dol/system01.dbf

/u01/app/oracle/oradata/dol/sysaux01.dbf

/u01/app/oracle/oradata/dol/undotbs01.dbf

/u01/app/oracle/oradata/dol/users01.dbf

SYS@dol&gt;select member from v$logfile;

MEMBER

--------------------------------------------------------------------------------

/u01/app/oracle/oradata/dol/redo03.log

/u01/app/oracle/oradata/dol/redo02.log

/u01/app/oracle/oradata/dol/redo01.log

SYS@dol&gt;select name from v$controlfile;

NAME

--------------------------------------------------------------------------------

/u01/app/oracle/oradata/dol/c01.ctl

/u01/app/oracle/oradata/dol/c02.ctl

SYS@dol&gt;show parameter spfile;

NAME                                 TYPE        VALUE

------------------------------------ ----------- ------------------------------

spfile                               string      /u01/app/oracle/product/11.2.0

                                                /dbhome_1/dbs/spfiledol.ora

SYS@dol&gt;exit

Disconnected from Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - Production

With the Partitioning, OLAP, Data Mining and Real Application Testing options

rman duplicate02

[oracle@oracle dbs]$ . oraenv

ORACLE_SID = [ck] ? ck

The Oracle base for ORACLE_HOME=/u01/app/oracle/product/11.2.0/dbhome_1 is /u01/app/oracle

[oracle@oracle dbs]$ sqlplus / as sysdba

SQL*Plus: Release 11.2.0.1.0 Production on Sun Dec 30 17:38:30 2012

Copyright (c) 1982, 2009, Oracle.  All rights reserved.

Connected to:

Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - Production

With the Partitioning, OLAP, Data Mining and Real Application Testing options

SYS@ck&gt;alter system current archive log;

alter system current archive log

            *

ERROR at line 1:

ORA-02065: illegal option for ALTER SYSTEM

SYS@ck&gt;alter system current archivelog;

alter system current archivelog

            *

ERROR at line 1:

ORA-02065: illegal option for ALTER SYSTEM

SYS@ck&gt;alter system archive log current;

System altered.

SYS@ck&gt;/

System altered.

SYS@ck&gt;/

System altered.

SYS@ck&gt;exit

Disconnected from Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - Production

With the Partitioning, OLAP, Data Mining and Real Application Testing options

[oracle@oracle dbs]$ rman target /

Recovery Manager: Release 11.2.0.1.0 - Production on Sun Dec 30 17:39:37 2012

Copyright (c) 1982, 2009, Oracle and/or its affiliates.  All rights reserved.

connected to target database: CK (DBID=820859332)

RMAN&gt; show all;

using target database control file instead of recovery catalog

RMAN configuration parameters for database with db_unique_name CK are:

CONFIGURE RETENTION POLICY TO REDUNDANCY 1; # default

CONFIGURE BACKUP OPTIMIZATION OFF; # default

CONFIGURE DEFAULT DEVICE TYPE TO DISK; # default

CONFIGURE CONTROLFILE AUTOBACKUP OFF; # default

CONFIGURE CONTROLFILE AUTOBACKUP FORMAT FOR DEVICE TYPE DISK TO &amp;apos;%F&amp;apos;; # default

CONFIGURE DEVICE TYPE DISK PARALLELISM 1 BACKUP TYPE TO BACKUPSET; # default

CONFIGURE DATAFILE BACKUP COPIES FOR DEVICE TYPE DISK TO 1; # default

CONFIGURE ARCHIVELOG BACKUP COPIES FOR DEVICE TYPE DISK TO 1; # default

CONFIGURE MAXSETSIZE TO UNLIMITED; # default

CONFIGURE ENCRYPTION FOR DATABASE OFF; # default

CONFIGURE ENCRYPTION ALGORITHM &amp;apos;AES128&amp;apos;; # default

CONFIGURE COMPRESSION ALGORITHM &amp;apos;BASIC&amp;apos; AS OF RELEASE &amp;apos;DEFAULT&amp;apos; OPTIMIZE FOR LOAD TRUE ; # default

CONFIGURE ARCHIVELOG DELETION POLICY TO NONE; # default

CONFIGURE SNAPSHOT CONTROLFILE NAME TO &amp;apos;/u01/app/oracle/product/11.2.0/dbhome_1/dbs/snapcf_ck.f&amp;apos;; # default

RMAN&gt; CONFIGURE CONTROLFILE AUTOBACKUP On;

new RMAN configuration parameters:

CONFIGURE CONTROLFILE AUTOBACKUP ON;

new RMAN configuration parameters are successfully stored

RMAN&gt; backup database plus archivelog;

Starting backup at 30-DEC-12

current log archived

allocated channel: ORA_DISK_1

channel ORA_DISK_1: SID=23 device type=DISK

channel ORA_DISK_1: starting archived log backup set

channel ORA_DISK_1: specifying archived log(s) in backup set

input archived log thread=1 sequence=5 RECID=1 STAMP=803410743

input archived log thread=1 sequence=6 RECID=2 STAMP=803410744

input archived log thread=1 sequence=7 RECID=3 STAMP=803410746

input archived log thread=1 sequence=8 RECID=4 STAMP=803410803

channel ORA_DISK_1: starting piece 1 at 30-DEC-12

channel ORA_DISK_1: finished piece 1 at 30-DEC-12

piece handle=/u01/app/oracle/flash_recovery_area/CK/backupset/2012_12_30/o1_mf_annnn_TAG20121230T174003_8g02zmjl_.bkp tag=TAG20121230T174003 comment=NONE

channel ORA_DISK_1: backup set complete, elapsed time: 00:00:01

Finished backup at 30-DEC-12

Starting backup at 30-DEC-12

using channel ORA_DISK_1

channel ORA_DISK_1: starting full datafile backup set

channel ORA_DISK_1: specifying datafile(s) in backup set

input datafile file number=00001 name=/u01/app/oracle/oradata/ck/system01.dbf

input datafile file number=00002 name=/u01/app/oracle/oradata/ck/sysaux01.dbf

input datafile file number=00003 name=/u01/app/oracle/oradata/ck/undotbs01.dbf

input datafile file number=00004 name=/u01/app/oracle/oradata/ck/users01.dbf

channel ORA_DISK_1: starting piece 1 at 30-DEC-12

channel ORA_DISK_1: finished piece 1 at 30-DEC-12

piece handle=/u01/app/oracle/flash_recovery_area/CK/backupset/2012_12_30/o1_mf_nnndf_TAG20121230T174004_8g02zo12_.bkp tag=TAG20121230T174004 comment=NONE

channel ORA_DISK_1: backup set complete, elapsed time: 00:00:31

Finished backup at 30-DEC-12

Starting backup at 30-DEC-12

current log archived

using channel ORA_DISK_1

channel ORA_DISK_1: starting archived log backup set

channel ORA_DISK_1: specifying archived log(s) in backup set

input archived log thread=1 sequence=9 RECID=5 STAMP=803410836

channel ORA_DISK_1: starting piece 1 at 30-DEC-12

channel ORA_DISK_1: finished piece 1 at 30-DEC-12

piece handle=/u01/app/oracle/flash_recovery_area/CK/backupset/2012_12_30/o1_mf_annnn_TAG20121230T174036_8g030nd7_.bkp tag=TAG20121230T174036 comment=NONE

channel ORA_DISK_1: backup set complete, elapsed time: 00:00:01

Finished backup at 30-DEC-12

Starting Control File and SPFILE Autobackup at 30-DEC-12

piece handle=/u01/app/oracle/flash_recovery_area/CK/autobackup/2012_12_30/o1_mf_s_803410837_8g030orr_.bkp comment=NONE

Finished Control File and SPFILE Autobackup at 30-DEC-12

RMAN&gt; sql &amp;apos;alter system archive log current&amp;apos;;

sql statement: alter system archive log current

RMAN&gt; sql &amp;apos;alter system archive log current&amp;apos;;

sql statement: alter system archive log current

RMAN&gt; sql &amp;apos;alter system archive log current&amp;apos;;

sql statement: alter system archive log current