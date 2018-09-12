#### 10.Security {#10-security}

Requirements

**Part1.** **业界安全性要求**

**1.1.** **法律：** - Sarbanes-Oxley 法案 (SOX)- 安全信息流通和责任法案 (HIPAA)- 加利福尼亚州违约法 - 英国数据保护法案

**1.2** **审计：** 许多法律都包括要求定期审计安全计划（内部控制能力）的条款。 SOX 的要求不太明确，以公司负责人的解释为准。实施细节可能会有很大的出入，这取决于公司负责人要求的细节级别。虽然 SOX 的要求不太明确，但是处罚很严厉，所以一定要确保公司符合其规定。实施安全措施的成本要与这种风险达到平衡。没有人能保证 100% 的安全性。最好的解决方法是与业界达成共识。如果您履行了议定的最低安全方案，并且已尽职尽责，就不会受到最不利的法律处罚。有一些可靠的资源提供了业界标准方案，如：美国系统管理和网络安全审计协会 (SANS) 、卡内基梅隆大学计算机系统防御系的 CERT/CC 以及 ISO-17799 认证标准，可从以下地址访问这些资源： [http://www.sans.org/index.php](http://www.sans.org/index.php) [http://www.cert.org/nav/index.html](http://www.cert.org/nav/index.html) [http://www.iso17799software.com/](http://www.iso17799software.com/) ISO-17799 认证标准是关于安全方案的国际标准，其中包括最佳方案、证书和风险评估。它涵盖了各种各样的问题并包括一些预先制定的策略。

**安全性包括以下几个方面：** - **限制对数据和服务的访问** 并不是所有用户对所有数据都具有访问权限。根据数据库中存储的内容，可按业务需要、客户期望以及日益增加的法律限制条件来管理有限制的访问权限。必须保护信用卡信息、健康保健数据、身份信息等，使之免受未授权访问。 Oracle 数据库通过提供细粒度的授权控制来限制数据库访问。限制访问时必须应用最少权限原则。 **- 验证用户** 为了对敏感数据强制实施访问控制，系统必须首先知道谁尝试访问数据。如果验证信息泄漏，则所有其它安全性预防措施都变得毫无用处。最基本的用户验证方式是要求用户提供知道的验证信息，如口令。确保口令遵循简单的规则，这样可大大增强系统的安全性。比较严格的验证方法包括要求用户提供掌握的某些验证信息，如令牌或公共密钥基础结构 (PKI) 证书。更严格的验证方法是，通过指纹、虹膜、骨组织模式等唯一生物学特征来识别用户。 Oracle 数据库通过高级安全选项支持高级验证技术，如基于令牌、生物学和证书的身份识别技术。为了防止试图泄漏验证信息，必须锁定当前未使用的用户帐户。 **- 监视可疑活动** 即使经过授权和验证的用户有时也会泄漏系统信息。要查到信息失窃的原因，第一步就是找出不寻常的数据库活动，如某个雇员突然开始查询大量的信用卡信息、研究结果或其它敏感信息。为了跟踪用户活动和确定可疑活动的变化趋向， Oracle 数据库提供了很多审计工具。

Least Privilege

**Part2.** **最少权限原则** 首先在最低级别应用最少权限原则，继而在每一级别应用此原则。总是会出现一些新的、不能预料的安全问题。通过应用这个原则，可降低发生问题的可能性，从而避免可能受到的损害。 **- 只在计算机上安装所需软件：** 通过减少安装的软件包数目，减少升级维护工作，降低出现安全漏洞的可能性，减少软件冲突。 **- 只在计算机上激活所需服务：** 服务越少意味着打开的端口越少，因而受到的攻击也就越少。 **- 只允许需要访问的用户访问操作系统** **(OS) 和数据库：** 用户越少意味着口令和帐户越少。这会降低公开帐户或帐户失效的可能性。 ~~~~~ 帐户越少，管理员就越容易跟踪帐户的最新活动。 **-~** **限制对** **root 或管理员帐户的访问：** 必须仔细保护和审计管理员帐户，决不能共享管理员帐户。 **- 限制对** **SYSDBA 和** **SYSOPER 帐户的访问：** 需要访问这两个角色的用户，必须各自有自己的帐户并且必须经过审计。 **- 只允许用户访问完成工作所需的数据库对象：** 如果用户访问的对象和服务超过需要访问的范围，就有机会发生不当行为。

**2.1** **应用最少权限原则** 应用最少权限原则意味着必须仅为用户授予有效完成任务所需的那些权限。这会降低用户无意中或恶意修改或查看他们本来无权修改或查看的数据的机率。 **a. 保护数据字典：** ~~~ 默认情况下， O7_DICTIONARY_ACCESSIBILITY 参数设置为 FALSE 。如果没有很好的理由，一定不要更改这个设置，因为这会阻止具有 ANY TABLE 系统权限的用户访问数据字典的基表。这个参数设置还确保 SYS 用户只能以 SYSDBA 身份登录。 **b.~** **从** **PUBLIC 撤销不必要的权限：** ~~~ 下列程序包对于需要这些程序包的应用程序非常有用，但需要进行正确的配置，以便安全地使用它们。请从 PUBLIC 撤销 EXECUTE 权限，在需要下列程序包时为角色授予 EXECUTE 权限： UTL_SMTP 、 UTL_TCP 、 UTL_HTTP 和 UTL_FILE 。 ~~~~~~ 有些功能较强的程序包可能会被误用，这些程序包包括： ~~~~~~UTL_SMTP ：允许将数据库用作简单邮件传输协议 (SMTP) 邮件服务器来发送任意电子邮件消息。 ~~~~~~~~~~~~~~~~~~~~~~~~~~ 如果将这个程序包授予 PUBLIC ，可能会出现未经授权就交换电子邮件消息的情况。 ~~~~~~UTL_TCP ：允许数据库服务器与任何正在接收或等待的网络服务建立外出网络连接。这样，便可以在数据库服务器和任何正在等待的网络服务之间发送任意数据。 ~~~~~~UTL_HTTP ：允许数据库服务器通过 HTTP 请求和检索数据。如果将这个程序包授予 PUBLIC ，可能会允许通过 HTML 形式将数据发送到恶意 Web 站点的行为。 ~~~~~~ ：如果配置不当，则可能会允许访问主机操作系统上任何文本文件的行为。即使配置正确，这个程序包也不会区分其调用应用程序； ~~~~~~~~~~~~~~~~~~~~~~~ 因此对 UTL_FILE 具有访问权的一个应用程序可能会将任意数据写到由另一个应用程序所写到的位置。 **c.~** **限制对操作系统目录的访问：** ~~~ 通过数据库中的 DIRECTORY 对象， DBA 可将目录映射到操作系统路径，从而将对这些目录的权限授予给各个用户。 **d. 限制具有管理权限的用户：** ~~~ 不要为数据库用户提供不必要的权限。一定不能将 DBA 角色授予给管理员之外的人员。要实施最少权限原则，请限制下列类型的权限： ~~~~~~ 系统和对象的权限 ~~~~~~ 连接到数据库的 SYS 权限，如 SYSDBA 和 SYSOPER~~~~~~ 其它 DBA 类型的权限，如 DROP ANY TABLE **e. 限制远程数据库验证：** ~~~ 默认情况下， EMOTE_OS_AUTHENT 参数设置为 FALSE 。一定不要更改这个设置，除非认为所有客户机都能适当地验证用户。在远程验证过程中： **~~~-** 数据库用户在外部进行验证 **~~~-** 远程系统对用户进行验证 **~~~-** 用户登录数据库，不必进行进一步的验证

Standard database Auditing

**审计** **(** **监视可疑活动** **)** 审计意味着捕获并存储关于系统中所发生情况的信息，这会增加系统需要执行的工作量。审计必须有重点，以便只捕获有意义的事件。如果审计重点设置适当，则会最大程度地减少对系统性能的影响。如果审计重点设置不当，则会对系统性能产生明显的影响。 3.1~ 强制性审计：不管其它审计选项或参数为何，所有 Oracle 数据库都会审计特定的操作。由于数据库需要记录系统启动和关闭等数据库活动，所以存在强制性审计日志。 3.2~ 标准数据库审计：这是通过使用 AUDIT_TRAIL 初始化参数在系统级别设置的。启用审计后，可选择要审计的对象和权限。 3.3~ 基于值审计：这种审计扩展了标准数据库审计的功能，不仅会捕获已发生的审计事件，还会捕获插入、更新或删除的实际值。基于值审计是通过数据库触发器实施的。 3.4~ 细粒度审计 (FGA) ： FGA 扩展了标准数据库审计的功能，这种审计可捕获已发出的实际 SQL 语句，而不仅仅是发生了事件的 SQL 语句。 3.5 审计：在 DBA 与审计者或安全管理员之间划分审计责任，审计者或安全管理员在操作系统审计线索中负责监视 DBA 的活动。

**3.2** **标准数据库审计（）** 启用数据库审计并指定审计选项（登录事件、系统活动和对象权限或使用的 SQL 语句）后，数据库开始收集审计信息。 3.2.1 ALTER SYSTEM SET audit_trail= &quot;OS&quot; SCOPE=SPFILE;~~~~~~~ 审计记录将存储在操作系统的审计系统中。 ~~~~~~~~ 在 Windows 环境下，审计记录存储在事件日志中。 ~~~~~~~~ 在 UNIX 或 Linux 环境下，审计记录存储在文件中。该文件的位置是 AUDIT_FILE_DEST 参数指定的位置。 3.2.2~ALTER SYSTEM SET audit_trail= &quot;DB&quot; SCOPE=SPFILE;~~~~~~~~ 审计记录会写入 视图中，这个视图是 SYS 方案的一部分。 3.2.3~ALTER SYSTEM SET audit_trail=&quot;XML&quot; SCOPE=SPFILE;~~~~~~~~ALTER SYSTEM SET audit_trail=&quot;XML,EXTENDED&quot; SCOPE=SPFILE;~~~~~~~~ 审计记录会写入 参数指向的目录中的 XML 文件。使用 V$XML_AUDIT_TRAIL 视图可查看此目录中的所有 XML 文件。 维护审计线索是一项重要的管理任务。取决于审计选项的重点，审计线索会迅速增长。 如果维护不当，审计线索会占用大量的空间，以至影响系统的性能。

**audit_sys_operations** 默认为 false ，当设置为 true 时，所有（注意是所有！） sys 用户（包括以 sysdba,sysoper 身份登录的用户）的操作都会被记录， audit trail 不会写在 aud$ 表中，这个很好理解，如果数据库还未启动 aud$ 不可用，那么像 conn /as sysdba 这样的连接信息，只能记录在其它地方。如果是 windows 平台， audti trail 会记录在 windows 的事件管理中，如果是 linux/unix 平台则会记录在 audit_file_dest 参数指定的文件中

**3.3** **指定审计选项** **3.3.1~** **审计** **SQL 语句：** ~~ **Statement** 按语句来审计，比如 audit table 会审计数据库中所有的 create table,drop table,truncate table 语句， alter session by cmy 会审计 cmy 用户所有的数据库连接。 例： AUDIT table; **3.3.2~** **审计系统权限（非重点和重点）：** 按权限来审计，当用户使用了该权限则被审计，如执行 grant select any table to a; audit select any table; 语句后，当用户 a 访问了用户 b 的表时（如 select * from b.t; ）会用到 select any table 权限，故会被审计。注意用户是自己表的所有者，所以用户访问自己的表不会被审计 例： AUDIT select any table, create any trigger; 例： AUDIT select any table BY hr BY SESSION; **3.3.3~** **审计对象权限（非重点和重点）：** **Object** 按对象审计，只审计 on 关键字指定对象的相关操作，如 aduit alter,delete,drop,insert on cmy.t by scott; 这里会对 cmy 用户的 t 表进行审计，但同时使用了 by 子句，所以只会对 scott 用户发起的操作进行审计 . 注意 Oracle 没有提供对 schema 中所有对象的审计功能，只能一个一个对象审计，对于后面创建的对象， Oracle 则提供 on default 子句来实现自动审计，比如执行 audit drop on default by access; 后， 对于随后创建的对象的 drop 操作都会审计。但这个 default 会对之后创建的所有数据库对象有效，似乎没办法指定只对某个用户创建的对象有效，想比 trigger 可以对 schema 的 DDL 进行 &quot; 审计 &quot; ，这个功能稍显不足。 例： AUDIT ALL on hr.employees; 例： AUDIT UPDATE,DELETE on hr.employees BY ACCESS;

**3.3.4** **审计的一些其他选项** **by access / by session** by access 每一个被审计的操作都会生成一条 audit trail 。 by session ，一个会话里面同类型的操作只会生成一条 audit trail 。 默认为 by session **whenever [ not ] successful** whenever successful 操作成功 (dba_audit_trail 中 returncode 字段为 0) 才审计 ,whenever not successful 反之。 省略该子句的话，不管操作成功与否都会审计。

**4\. 取消审计** **NOAUDIT**

**5\. 和审计相关的视图** **dba_audit_trail** 保存所有的 audit trail ，实际上它只是一个基于 aud$ 的视图。其它的视图 dba_audit_session,dba_audit_object,dba_audit_statement 都只是 dba_audit_trail 的一个子集。 **dba_stmt_audit_opts~~--** 可以用来查看 statement 审计级别的 audit options ，即数据库设置过哪些 statement 级别的审计。 **dba_obj_audit_opts** ~~~-- 系统权限审计选项 **dba_priv_audit_opts** ~~-- 对象权限审计选项 **all_def_audit_opts~~~~--** 用来查看数据库用 on default 子句设置了哪些默认对象审计。

STMT_AUDIT_OPTION_MAP~~~-- 审计选项类型代码 AUDIT_ACTIONS~~~-- action 代码 ALL_DEF_AUDIT_OPTS~~~-- 对象创建时默认的对象审计选项 DBA_STMT_AUDIT_OPTS~~~-- 当前数据库系统审计选项 USER_OBJ_AUDIT_OPTS~~~-- 对象审计选项 DBA_AUDIT_TRAIL~~USER_AUDIT_TRAIL~~~-- 审计记录 DBA_AUDIT_OBJECT~~USER_AUDIT_OBJECT~~~-- 审计对象列表 DBA_AUDIT_SESSION~~USER_AUDIT_SESSION~~~-- session 审计 DBA_AUDIT_STATEMENT~~USER_AUDIT_STATEMENT~~~-- 语句审计 DBA_AUDIT_EXISTS~~~-- 使用 BY AUDIT NOT EXISTS 选项的审计 DBA_AUDIT_POLICIES~~~-- 审计 POLICIESDBA_COMMON_AUDIT_TRAIL~~~-- 标准审计 + 精细审计

**6.** **维护审计线索** 应该维护审计线索。遵循下列最佳方案准则： -~ 复查和存储旧记录。 -~ 避免出现存储问题。 -~ 避免记录丢失。 维护审计线索 必须维护每种类型的审计线索。基本维护必须包括复查审计记录及删除数据库或操作系统中的较旧记录。审计线索会不断增长，一直填满可用存储空间。如果文件系统已满，系统可能会崩溃或只是导致性能问题。如果数据库审计线索填满了表空间，则审计操作不能完成。如果审计线索填满了系统表空间，在审计操作停止之前，其它操作的性能会受到影响。 标准审计的审计线索存储在 AUD$ 表中。 FGA 的审计线索存储在 FGA_LOG$ 表中。默认情况下，这两个表都是在 SYSTEM 表空间中创建的。使用导出和导入实用程序可将这两个表移到另一表空间中。 注：不支持将审计表移到 SYSTEM 表空间外部。 在从审计表中删除记录时，审计记录可能会丢失。 最佳方案提示：根据时间戳使用导出实用程序，然后根据同一时间戳删除审计线索中的行。

Value-based Auditing

**基于值审计**

基于值审计的关键在于审计触发器。典型的审计触发器如下所示： CREATE OR REPLACE TRIGGER system.hrsalary_auditAFTER UPDATE OF salaryON hr.employeesREFERENCING NEW AS NEW OLD AS OLDFOR EACH ROWBEGINIF :old.salary != :new.salary THENINSERT INTO system.audit_employeesVALUES (sys_context(&amp;apos;userenv&amp;apos;,&amp;apos;os_user&amp;apos;), sysdate,sys_context(&amp;apos;userenv&amp;apos;,&amp;apos;ip_address&amp;apos;),:new.employee_id ||&amp;apos; salary changed from &amp;apos;||:old.salary||&amp;apos; to &amp;apos;||:new.salary);END IF;END;/ 这个触发器将审计的重点设置为捕获对 hr.employees 表薪水列的更改。更新某行后，触发器就会检查薪水列。如果新旧薪水不相等，那么触发器会在 audit_employees 表（通过一个单独的操作在 SYSTEM 方案中创建）中插入一条审计记录。审计记录包括用户名、执行更改的 IP 地址、标识记录发生更改的主键及更改的实际薪水值。

如果标准数据库审计收集的数据不足，还可使用数据库触发器来捕获关于用户连接的信息。通过使用登录触发器，管理员可以捕获用来标识连接到数据库的用户的数据。示例包括下列内容： -~ 登录人员的 IP 地址 -~ 用于连接到实例的程序名的前 48 个字符 -~ 用于连接到实例的终端名 有关用户参数的完整列表，请参阅《 Oracle 数据库 SQL 参考》中的 &quot;SYS_CONTEXT&quot; 一节。

Fine-grained Auditing

**FGA 策略**

**1.EAMPLE1** **：** **begin** dbms_fga.add_policy (object_schema =&gt; &amp;apos;HR&amp;apos;,object_name =&gt; &amp;apos;EMPLOYEES&amp;apos;,policy_name =&gt; &amp;apos;audit_emps_salary&amp;apos;,audit_condition =&gt; &amp;apos;department_id=10&amp;apos;,audit_column =&gt; &amp;apos;SALARY&amp;apos;,handler_schema =&gt; &amp;apos;secure&amp;apos;,handler_module =&gt; &amp;apos;log_emps_salary&amp;apos;,enable =&gt; TRUE,statement_types =&gt; &amp;apos;SELECT&amp;apos; );end;/

**EXAMPLE2** **：** SQL&gt; exec dbms_fga.add_policy(object_schema=&gt;&amp;apos;SEC&amp;apos;, object_name=&gt; &amp;apos;t_audit&amp;apos;, policy_name=&gt; &amp;apos;check_t_audit&amp;apos;,statement_types =&gt; &amp;apos;INSERT, UPDATE, DELETE, SELECT&amp;apos;);

此过程接受下列参数： **策略名** **Policy Name** 创建每个 FGA 策略时会指定其名称。幻灯片示例通过使用以下参数来命名 AUDIT_EMPS_SALARY 策略： policy_name =&gt; &amp;apos;audit_emps_salary&amp;apos; **审计条件** **Audit Condition** 审计条件是一个 SQL 谓词，用于定义必须触发审计事件的时间。在幻灯片示例中，通过使用以下条件参数来审计部门 10 中的所有行： audit_condition =&gt; &amp;apos;department_id = 10&amp;apos; **审计列** **Audit Column** 审计列定义了要审计的数据。如果此列包括在 SELECT 语句中或审计条件允许此选择，就会发生审计事件。幻灯片示例使用以下参数来审计两列： audit_column =&gt; &amp;apos;SALARY&amp;apos; 此参数是可选参数。如果未指定此参数，则仅由 AUDIT_CONDITION 参数确定审计事件是否必须发生。 **对象** **Object** 对象是要审计的表或视图。可通过以下两个参数传递对象： -~ 包含对象的方案 -~ 对象的名称 幻灯片示例使用以下参数来审计 hr.employees 表： object_schema =&gt; &amp;apos;hr&amp;apos;object_name = &gt; &amp;apos;employees&amp;apos; **处理程序** **Handler** 可选事件处理程序是一个 PL/SQL 过程，用于定义在审计期间必须执行的任何附加操作。例如，事件处理程序可向管理员发送一个警报页。如果未定义审计事件处理程序，则在审计线索中插入审计事件条目。如果定义了审计事件处理程序，则在审计线索中插入审计条目并执行审计事件处理程序。 审计事件条目包括导致事件的 FGA 策略、执行 SQL 语句的用户、 SQL 语句及其绑定变量。可通过以下两个参数传递事件处理程序： -~ 包含 PL/SQL 程序单元的方案 - 程序单元的名称 幻灯片示例使用以下参数执行 SECURE.LOG_EMPS_SALARY 过程： handler_schema =&gt; &amp;apos;secure&amp;apos;handler_module =&gt; &amp;apos;log_emps_salary&amp;apos; 默认情况下，审计线索总是将 SQL 文本和 SQL 绑定信息写到 LOB 。可以更改默认设置（例如，系统性能下降时）。 **状态** **Status** 状态指示是否启用了 FGA 策略。在幻灯片示例中，以下参数会启用此策略： enable = &gt; TRUE

**2.** **查看审计信息** **:**

SQL&gt; select count(*) from dba_fga_audit_trail;

**3.** **审计的** **DML 语句：注意事项** 使用为 DML 语句定义的 FGA 策略时，如果要处理的数据行（包括新行和旧行）符合策 略谓词标准，就会审计 DML 语句。 但是，如果同时在策略定义中指定了相关列，当数据符合 FGA 策略谓词并且语句引用了 定义的相关列时，就会审计语句。 对于 DELETE 语句，在定义策略期间指定相关列没有用，这是因为 DELETE 语句会访问 表中的所有列。因此，不管相关列是什么，总是要审计 DELETE 语句。 FGA 支持 MERGE 语句。如果基础 INSERT 或 UPDATE 语句符合任何定义的 INSERT 或 UPDATE FGA 策略，则会审计这些语句。 使用以前定义的 FGA 策略时，会审计第一个语句而不审计第二个语句。 ~~~

**4.FGA 准则** -~ 要审计所有语句，请使用 null 条件。 - 策略名必须唯一。 -~ 创建策略时，审计的表或视图必须已经存在。 -~ 如果审计条件语法无效，则访问审计对象时会发生 ORA-28112 错误。 -~ 如果表中不存在审计的列，则不会审计任何行。 -~ 如果事件处理程序不存在，则不会返回任何错误但仍会创建审计记录。

DBA Auditing

DBA 审计

具有SYSDBA 或SYSOPER 权限的用户可在数据库处于关闭状态时进行连接。

- 审计线索必须存储在数据库外部。

- 总是审计以SYSDBA 或SYSOPER 身份进行的连接。

- 可使用audit_sys_operations 启用对SYSDBA或SYSOPER 操作的附加审计。

- 可使用audit_file_dest 控制审计线索。

DBA 审计

SYSDBA 和SYSOPER 用户有权启动和关闭数据库。因为关闭数据库时这些用户可能做了更改，所以这些权限的审计线索必须存储在数据库外部。Oracle 数据库会自动捕获SYSDBA 和SYSOPER 用户的登录事件。这为跟踪授权或未授权SYSDBA 和SYSOPER 操作提供了一种有效方法，但只在复查操作系统审计线索时才有用。

除非专门启用了审计，否则Oracle 数据库只捕获登录事件。通过设置初始化参数可启用对SYSDBA 和SYSOPER 用户的审计：

audit_sys_operations=TRUE（默认设置为FALSE。）

如果审计了SYS 操作，audit_file_dest 初始化参数则会控制审计记录的存储位置。在Windows 平台上，默认情况下，审计线索存储在Windows 事件日志中。在UNIX 或Linux 平台上，审计记录存储在$ORACLE_HOME/rdbms/audit 中。

Security Updates

1\. 安全更新 - 在 Oracle Technology Network Web 站点上公布了安全警报，其网址如下： [http://www.oracle.com/technology/deploy/security/alerts.htm](http://www.oracle.com/technology/deploy/security/alerts.htm) - 数据库管理员和开发人员单击 &quot;Subscribe to Security Alerts Here （订阅这里的安全警报） &quot; 链接进行预订后，便可通过电子邮件得到有关严重安全警报的通知。

Oracle 安全警报包含关于安全漏洞、风险评估及与漏洞相关的泄漏程度、适用的解决方法或补丁程序的简要描述。可使用 Enterprise Manager 管理补丁程序。 Oracle 会就个人或机构是否得到有关安全漏洞的通知进行确认。 安全警报被公布在 Oracle Technology Network Web 站点和 Oracle MetaLink 上。尽管安全警报是向关心这些警报的所有人公布的，但是只有拥有当前客户支持标识 (CSI) 号的客户才能下载补丁程序。 Oracle 感谢您通过及时、完善且机密的潜在安全漏洞通知来配合我们保护产品的安全。如果您发现任何 Oracle 产品有安全漏洞，请通过 MetaLink 提交服务请求或将电子邮件 发送到 secalert_us@oracle.com 来通知 Oracle 。

2\. 应用安全补丁程序 -~ 使用关键补丁程序更新进程。 -~ 应用所有安全补丁程序和解决方法。 -~ 与 Oracle 负责产品安全的团队联系。

**关键补丁程序更新** **(CPU) 方案** Oracle 在 2005 年 1 月启动了 CPU 方案。此方案按季度将关键补丁程序捆绑在一起。此方案取代了安全警报补丁程序的发布。这些补丁程序是累积性补丁程序，其中包括用户经常请求的必备补丁程序。每个季度发布的补丁程序中还包含风险评估体系，可用于确定对站点的影响和站点存在的安全风险。请参阅 MetaLink 注释： 290738.1&quot;Oracle 关键补丁程序更新方案一般常见问题解答 &quot; 。必须预订 MetaLink 才能接收这些更新。 **应用所有安全补丁程序和解决方法** 总是对数据库所在的操作系统和 Oracle 软件，以及所有已安装的选件和组件应用所有相关的最新安全补丁程序。 **与** **Oracle 负责产品安全的团队联系** 如果您确信已经发现了 Oracle 软件中的安全漏洞，请按以下网址中 &quot;Security Alerts （安全警报） &quot; 链接提供的说明进行操作： [http://otn.oracle.com](http://otn.oracle.com) 或 [http://www.oracle.com/technology/deploy/security/alerts.htm](http://www.oracle.com/technology/deploy/security/alerts.htm)