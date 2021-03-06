#### 16.Performing Database Recovery {#16-performing-database-recovery}

Checkpoint (CKPT)

LogWriter (LGWR)

日志写进程

日志写进程何时执行写入：

? 提交时

? 达到三分之一满时

? 每隔三秒

? DBWn 执行写入之前

重做日志文件记录因执行事务处理和Oracle 服务器内部操作而对数据库所做的更改。（事务处理是逻辑工作单元，由用户运行的一个或多个SQL 语句组成。）

重做日志文件可保护数据库的完整性，避免断电、磁盘故障等因素所导致的系统故障。

重做日志文件必须多路复用，以确保在出现磁盘故障事件时不会丢失其中存储的信息。

重做日志由重做日志文件组组成。

重做日志文件组又由重做日志文件和其多路复用的副本组成。每个相同的副本都是该组的一个成员，每个组按编号标识。

日志写进程(LGWR)进程将重做记录从重做日志缓冲区写入到重做日志组的所有成员，直至文件已填满或请求了日志切换操作。然后，会切换至下一组中的文件并执行写入。重做日志组以循环方式使用。

最佳方案提示：多路复用的重做日志文件应尽量驻留在不同的磁盘中。

Redo Log Files

重做日志文件：

? 记录数据库的更改

? 应多路复用以避免文件丢失

Archiver (ARCn)

归档进程(ARCn)：

? 是一个可选的后台进程

? 将数据库设置为ARCHIVELOG 模式时会自动归档联机重做日志文件

? 保留对数据库进行的所有更改的记录

ARCn 是一个可选的后台进程。但是，在丢失磁盘后恢复数据库时，这个进程至关重要。联机重做日志组填满后，Oracle 实例便开始对下一个联机重做日志组执行写入。

从一个联机重做日志组切换到另一个联机重做日志组的过程称为日志切换。

ARCn 进程在每次进行日志切换时都会对已填满的日志组进行归档。它会在重新使用日志组之前自动归档联机重做日志组，因此会保留对数据库所做的所有更改。这样，即使磁盘驱动器损坏，也可以将数据库恢复到故障点。

DBA 必须决定是将数据库配置为在ARCHIVELOG 模式下运行，还是在NOARCHIVELOG模式下运行。

? 在NOARCHIVELOG 模式下，每次发生日志切换时，都会覆盖联机重做日志文件。

? 在ARCHIVELOG 模式下，必须先归档非活动的已填满联机重做日志文件组，才能再次使用这些组。

注：对大多数备份策略而言，ARCHIVELOG 模式是必须选择的模式（并且极易配置）。

Instance Recovery