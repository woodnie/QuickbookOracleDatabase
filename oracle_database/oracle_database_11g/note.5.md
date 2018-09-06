### note {#note}

alter database mount

select * from v$____

Redo log:

select * from v$logfile;

desc dba_tables;

desc dba_views;

Trouble shooting:

1) Flash recovery space

show parameter dump;

Looking errro message

Find: Flash recovery  error message;

show parameter recover;

SQL&gt; SELECT TABLE_NAME FROM DICT WHERE TABLE_NAME LIKE &amp;apos;%ARCHIVE%&amp;apos;;

SQL&gt; DESC V$ARCHIVED_LOG

SQL&gt; SELECT NAME,TO_CHAR(FIRST_TIME,&amp;apos;YYYY-MM-DD HH24:MI:SS&amp;apos;), BLOCKS*BLOCK_SIZE/1024/1024 SIZE_MB FROM V$ARCHIVED_LOG;

C:\Windows\system32&gt;RMAN target /

RMAN&gt; delete archivelog all;