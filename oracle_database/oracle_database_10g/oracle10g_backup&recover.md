### Oracle10g Backup&amp;Recover {#oracle10g-backup-recover}

[root@oracle ~]# yum install  readline*  

[root@oracle ~]# wget [http://utopia.knoware.nl/~hlub/rlwrap/rlwrap-0.37.tar.gz](http://utopia.knoware.nl/~hlub/rlwrap/rlwrap-0.37.tar.gz)

[root@oracle ~]# tar -xvzf rlwrap-0.37.tar.gz

[root@oracle ~]# cd rlwrap-0.37

[root@oracle rlwrap-0.37]# ./configure

[root@oracle rlwrap-0.37]# make

[root@oracle rlwrap-0.37]# make install

[oracle@oracle ~]$ vi .bashrc # 添加以下内容alias sqlplus=&amp;apos;rlwrap sqlplus&amp;apos;

alias rman=&amp;apos;rlwrap rman&amp;apos;

#### recover controlfile {#recover-controlfile}

准备工作：

1\. 备份 RMAN&gt; backup contolfile;RMAN&gt; backup database;

2\. 查看 dbidSQL&gt; select dbid from v$database;

~~~~~DBID----------

23\. 查看 log modeSQL&gt; select log_mode from v$database;

LOG_MODE------------ARCHIVELOG

4\. 查看 SQL&gt; ARCHIVE LOG list;Database log mode~~~~~~~~~~~ModeAutomatic archival~~~~~~~~~~~~EnabledArchive destination~~~~~~~~~~~USE_DB_RECOVERY_FILE_DESTOldest online log sequence~~~~~0Next log sequence to archive~~1Current log sequence~~~~~~~~~~~~~1

5\. 模拟 cntrolfile 故障 #mv controlfiel

~

SQL&gt; alter database open;alter database open*ERROR at line 1:ORA-01589: must use RESETLOGS or NORESETLOGS option for database open

SQL&gt; alter database open NORESETLOGS;alter database open NORESETLOGS*ERROR at line 1:ORA-01610: recovery using the BACKUP CONTROLFILE option must be done

SQL&gt; alter database open RESETLOGS;alter database open RESETLOGS*ERROR at line 1:ORA-01152: file 1 was not restored from a sufficiently old backupORA-01110: data file 1: &amp;apos;/opt/oracle10g/oradata/oracle0/system01.dbf&amp;apos;

#### recover database {#recover-database}

recover database 和 recover database using backup controlfile2010-07-16 14:18

recover database ：

~~~~~~ 在普通的 recover database 或者 recover tablespace, recover datafile 时， Oracle 会以当前 controlfile 所纪录的 SCN 为准，利用 archive log 和 redo log 的 redo entry ，把相关的 datafile 的 block 恢复到 &quot; 当前 controlfile 所纪录的 SCN&quot; 。

recover database using backup controlfile ：

~~~~~~~Oracle 需要把数据恢复到比当前 controlfile 所纪录的 SCN 还要靠后的位置（比如说： control file 是 backup controlfile ，或者 controlfile 是根据 trace create 的。），这时候，就需要用 using backup controlfile 。 恢复就不会受 &quot; 当前 controlfile 所纪录的 SCN&quot; 的限制。只是想告诉数据库 , 我这个 controlfile 是旧的 , 这个时候数据库就会不断应用归档日志 , 它也不知道哪一个是最后的归档和当前日志 , 需要限制就来自于你的语句 (until time , until scn) ，或者可用的 archive log(until cancel) 。

=========================================================

1\. 首先这里我们先介绍四个 SCN 概念。 1.1 、系统检查点 scn 当一个检查点动作完成后， Oracle 就把系统检查点的 SCN 存储到控制文件中。 select checkpoint_change# from v$database ； 1.2 ，数据文件检查点 scn 当一个检查点动作完成后， Oracle 就把每个数据文件的 scn 单独存放在控制文件中。 select name,checkpoint_change# from v$datafile ； 1.3 ，启动 scnOracle 把这个检查点的 scn 存储在每个数据文件的文件头中，这个值称为启动 scn ，因为它用于在数据库实例启动时， 检查是否需要执行数据库恢复。 select name,checkpoint_change# from v$datafile_header1.4 、终止 scn 每个数据文件的终止 scn 都存储在控制文件中。 select name,last_change# from v$datafile

以下条件需要使用 using backup controlfile1 ）、使用备份控制文件 2 ）、重建 resetlogs 控制文件 , 如果重建立 noresetlogs 不必要使用 using backup controlfile

2 、 alter database open resetlog 指定 RESETLOGS 将重设当前 LOG sequence number 为 1 ，抛弃所有日志信息。 以下条件需要使用 resetlog1 ）在不完全恢复（介质恢复） 2 ）使用备份控制文件 使用 resetlogs 打开数据库后无必完整地备份一次数据库。 3 、 create controlfile resetlogs/noresetlogs1 ）．用 Noresetlogs 重建控制文件时，控制文件中 datafile Checkpoint 来自 Online logs 中的 Current log 头 2 ）．用 Resetlogs 重建控制文件时，控制文件中 datafile Checkpoint 来自各数据文件头。

当 system scn,datafile scn,start scn 不全相等，需要介质恢复，如果 stopscn null 需要实例恢复 resetlogs 抛弃所有在上一次恢复没有用到的日志信息，确保不被重新用与恢复。 1 、系统正常关闭： system scn=datafile scn=start scn=stop scn1)system scn=datafile scn=start scn ，不需要介质恢复 2)stopscn not null, 不需要实例恢复 2 、系统异常关闭： system scn=datafile scn=start scn ， stop scn null1)system scn=datafile scn=start scn ，不需要介质恢复 2)stopscn null, 需要实例恢复 3 、旧数据文件 system scn=datafile scn&gt;start scn ， stop scn null/notnull1)system scn=datafile scn&gt;start scn ，需要介质恢复成 system scn=datafile scn=start scn2)stopscn null, 需要实例恢复， not null 不需要实例恢复 4 、备份控制文件 system scn=datafile scn&lt;=start scn( 当数据文件为旧的相等 ) ， stop scn notnull/null1)system scn=datafile scn&lt;=start scn ，需要使用 using backup controlfile 介质恢复成 system scn=datafile scn=start scn=current log scn( 当前日志最大 SCN)2) 为保证上一次恢复没有用到 log 日志不被使用，必须 resetlogs5 、重建 noresetlogs 控制文件 控制文件中 datafile Checkpoint 来自 Online logs 中的 Current log 头 current log scn=system scn=datafile scn&gt;=start scn,stop scn not null/null1)current log scn=system scn=datafile scn&gt;=start scn ，需要介质恢复成 system scn=datafile scn=start scn=redolog scn( 当前日志最大 SCN),stop scn not null2)stopscn not null 不需要实例恢复 6 、重建 resetlogs 控制文件 控制文件中 datafile Checkpoint 来自各数据文件头。 system scn&gt;=datafile scn=start scn,stop scn not null/null1)system scn&gt;=datafile scn=start scn ，需要使用 using backup controlfile 介质恢复成 system scn=datafile scn=start scn( 当前日志最大 SCN),stop scn not null2)stop notnull ，因为 SCN 已经为 redolog scn ， log 已经不能使用，必须 resetlogs 。