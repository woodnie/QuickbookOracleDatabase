#### tables/view {#tables-view}

SQL&gt; select * from v$dbfile;  # 当前系统正在使用的数据文件

SQL&gt; select * from user_tables;  #当前用户所拥有的表

SQL&gt; select * dba_tablespaces; #当前系统表空间

SQL&gt; select * user_tablespaces; #当前用户表空间

SQL&gt; select * from dba_users; #当期系统sys用户及其信息

SQL&gt; select * from user_users; #当期系统用户及其信息

SQL&gt; desc dba_segments;

SQL&gt; desc user_segments;

dba_users

v$tablespaces

dba_profiles

v$spparametes

v$system_parameters

dba_constraints

recyclebin

purge

v$lock

desc dbms_rowid

exec dbms_roeid

? 表空间信息：

- DBA_TABLESPACES

- V$TABLESPACE

? 数据文件信息：

- DBA_DATA_FILES

- V$DATAFILE

? 临时文件信息：

- DBA_TEMP_FILES

- V$TEMPFILE

注：DICT 和DICT_COLUMNS 视图还包含这些动态性能视图的名称。

可查询V$FIXED_TABLE 来了解所有视图名称。