#### tkprof {#tkprof}

TKPROF 的使用

    tkprof 的目的是将sql trace 生成的跟踪文件转换成用户可以理解的格式

   1\. 格式

     tkprof inputfile outputfile [optional | parameters ]

   参数和选项：

    explain=user/password 执行explain命令将结果放在SQL trace的输出文件中

    table=schema.table 指定tkprof处理sql trace文件时临时表的模式名和表名

    insert=scriptfile 创建一个文件名为scriptfile的文件，包含了tkprof存放的输出sql语句

    sys=[yes/no] 确定系统是否列出由sys用户产生或重调的sql语句

    print=number 将仅生成排序后的第一条sql语句的输出结果

    record=recordfile 这个选项创建一个名为recorderfile的文件，包含了所有重调用的sql语句

    sort=sort_option 按照指定的方法对sql trace的输出文件进行降序排序

            sort_option 选项

               prscnt  按解析次数排序

               prscpu  按解析所花cpu时间排序

               prsela  按解析所经历的时间排序

               prsdsk  按解析时物理的读操作的次数排序

               prsqry  按解析时以一致模式读取数据块的次数排序

               prscu   按解析时以当前读取数据块的次数进行排序

               execnt  按执行次数排序

               execpu  按执行时花的cpu时间排序

               exeela  按执行所经历的时间排序

               exedsk  按执行时物理读操作的次数排序

               exeqry  按执行时以一致模式读取数据块的次数排序

               execu   按执行时以当前模式读取数据块的次数排序

               exerow  按执行时处理的记录的次数进行排序

               exemis  按执行时库缓冲区的错误排序

               fchcnt  按返回数据的次数进行排序

               fchcpu  按返回数据cpu所花时间排序

               fchela  按返回数据所经历的时间排序

               fchdsk  按返回数据时的物理读操作的次数排序

               fchqry  按返回数据时一致模式读取数据块的次数排序

               fchcu   按返回数据时当前模式读取数据块的次数排序

               fchrow  按返回数据时处理的数据数量排序

2 sql trace 的输出结果

        count：提供OCI过程的执行次数

        CPU：  提供执行CPU所花的时间单位是秒

        Elapsed：提供了执行时所花的时间。单位是秒。这个参数值等于用户响应时间

        Disk：提供缓存区从磁盘读取的次数

        Query：以一致性模式从缓存区获得数据的次数

        Current：以当前模式从缓存区获得数据的次数

        ROWs： 返回调用或执行调用时，处理的数据行的数量。

3：举例：

        sql&gt;alter session set sql_trace=true                

        SQL&gt;select * from dba_users;

        SQL&gt;show parameter user_dump_dest

        user_dump_dest     string  /oracle/oracle/diag/rdbms/orcl/orcl/trace

        SQL&gt;exit

        cd /oracle/oracle/diag/rdbms/orcl/orcl/trace

        tkprof orcl_ora_11066.trc /oracle/oracle/trace1.out sys=yes

        vi trace.out