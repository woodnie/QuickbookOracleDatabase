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