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