#### note {#note}

1 ）#su - oracle

2）$lsnrctl start

3）$sqlplus /nolog

4）SQL&gt;;connect sys/&lt;sys_passwd&gt;;@&lt;SID&gt;; as sysdba

5）SQL&gt;;start

sqlplus / as sysdba

starup

shutdown

listener.ora 和 tnsnames.ora

++++++++++

/etc/hosts

hostname IP

++

create table employees as select * from hr.employees;

dbca

desc dba_tables;

++++++++++++++++++

ddl dml dcl

&gt;set timing on;

杀出数据

del rollback；高水位变

tuncate 不可回滚；变水位不变

craet user u-name  

drob shanchu biao

alter user hr account unlock;

desc dba_tables;

select table_name, owner from dba_tables where tablespace_name like &amp;apos;%EXAMPLE&amp;apos;

++++

select系统信息：

select * from nls_session_parameters;

shutdown immediate

################

我觉得toad比较好，quest系列里还有个qoo，很好的冬冬。

Oracle10g中可以使用两个命令就可以知道，该版本的数据库系统是下载版还是商业版，使用两个命令就可以确定。

最大session数：

show   parameter   session  

sessions                             integer     170   #170是下载版数值

最大进程数：

show  parameter    process  

         ....................150   processes //150也是下载版数值

当前连接数：

select   *  from  v$bgprocess

查看当前连接到数据库的用户：

         select   *   from   v$session

默认系统最大session=min( session,process *  1.1 +5  )