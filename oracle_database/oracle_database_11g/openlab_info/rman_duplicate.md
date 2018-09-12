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