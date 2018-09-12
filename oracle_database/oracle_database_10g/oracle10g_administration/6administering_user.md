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