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