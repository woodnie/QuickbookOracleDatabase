## Oracle patch {#oracle-patch}

[http://blog.csdn.net/launch_225/article/details/7620811](http://blog.csdn.net/launch_225/article/details/7620811)

oracle打补丁步骤简介

1、了解opatch

opatch是用于维护&quot;个别&quot;补丁的,有人称其为interim path或是one-off patch

该命令的存放位置在$ORACLE_HOME下的OPatch目录下.

使用&quot;-help&quot;参数可以获得opatch命令的帮助信息

    [oracle@ora OPatch]$ ./opatch -help

    Usage: opatch [ -help ] [ -r[eport] ] [ command ]

               command := apply

                          lsinventory

                          prereq

                          query

                          rollback

                          util

                          version

    &lt;global_arguments&gt; := -help       Displays the help message for the command.

                          -report     Print the actions without executing.

    example:

      &amp;apos;opatch -help&amp;apos;

      &amp;apos;opatch apply -help&amp;apos;

      &amp;apos;opatch lsinventory -help&amp;apos;

      &amp;apos;opatch prereq -help&amp;apos;

      &amp;apos;opatch rollback -help&amp;apos;

      &amp;apos;opatch util -help&amp;apos;

    [oracle@ora OPatch]$ ./opatch lsinventory -h

    DESCRIPTION

        List the inventory for a particular $ORACLE_HOME or display all

        installations that can be found.

    SYNTAX

    opatch lsinventory [-all ] [-all_nodes] [-delay &lt;value&gt; ] [-detail ]

                       [-invPtrLoc &lt;Path to oraInst.loc&gt; ] [-jre &lt;LOC&gt; ]

                       [-oh] [-oh &lt;ORACLE_HOME&gt; ] [-patch]

                       [-ptlConnect  &lt;portal connect string&gt; ]

                       [-ptlPassword &lt;portal password&gt; ]

                       [-ptlSchema &lt;portal schema&gt; ]

                       [-property_file &lt;path to property file&gt;]

         [-retry &lt;value&gt; ]

    OPTIONS

           -all

                  Report the name and installation directory for each

                  $ORACLE_HOME found.

           -all_nodes

             Reports the patches installed on the given Oracle Home

                  in all nodes of the RAC system. It also prints the oracle

                  binary&amp;apos;s size and checksum on all nodes. This option cannot

                  be used in conjunction with -all, -detail, -patch.

           -delay

                  If -retry is specified, this option tells OPatch how

                  many seconds it should wait before attempting to lock

                  inventory again in case of a previous failure.

           -detail(s)

                  Report installed products and other details. This

                  option cannot be used in conjunction with all option.

           -invPtrLoc

                  Used to locate the oraInst.loc file. When the

                  installation used the invPtrLoc flag. This should be

                  the path to the oraInst.loc file

           -jre

                  This option tells OPatch to use JRE (java) from the

                  specified location instead of the default location

                  under Oracle Home

           -oh

                  The oracle home to work on. This takes precedence over

                  the environment variable ORACLE_HOME

           -patch

                  Lists the patch IDs installed in the oracle home

           -ptlConnect

                  This option is used to specify the connect string credentials

                  of the portal schema

           -ptlPassword

                  This option is used to specify the password of the portal

                  schema

           -ptlSchema

                  This option is used to specify the schema of the portal

                  repository

           -property_file

                  The user defined property file for OPatch to use. The

                  path to the property file should be absolute. This

                  property file takes precedence over the one that is

                  supplied with OPatch

           -retry

                  This option tells OPatch how many times it should

                  retry in case of an inventory lock failure      

    PARAMETERS

           This operation does not have any required parameters.

2、下载补丁.

需要metalink的用户名口令  

3、关闭数据库及监听.

4、安装补丁(可以参照metalink补丁说明)

    Patch Installation Instructions:                                                

    --------------------------------                                                

    Make sure that all instances running under the ORACLE_HOME being patched        

    are cleanly shutdown before installing this patch. Also ensure that              

    the tool used to terminate the instance(s) has exited cleanly.            

    1\. To apply the patch, unzip the PSE container file:                            

    p7650993_111070_Linux-x86.zip                                                    

    2\. PreInstall steps:                                                            

    3\. Set your current directory to the directory where the patch                  

    is located:                                                                      

       % cd 7650993                                                                                            

       Ensure that the directory containing the opatch script. appears in            

       your $PATH; then enter the following command:                                

       % opatch apply                            

    4\. PostInstall Steps:                                  

    Note:                                                                            

       If the Oracle inventory is not setup correctly this utility will              

       fail. To check accessibility to the inventory you can use the                

       command                                                                      

       % opatch lsinventory                                                          

       If you have any problem installing this PSE or are not sure                  

       about the inventory setup, please call Oracle support.

5、启动实例，运行相关脚本

    SQL&gt; startup

    ORACLE instance started.

    Total System Global Area  281018368 bytes

    Fixed Size                  1218968 bytes

    Variable Size              83887720 bytes

    Database Buffers          192937984 bytes

    Redo Buffers                2973696 bytes

    Database mounted.

    Database opened.

   检查无效对象：

   SQL&gt; SELECT OBJECT_NAME FROM DBA_OBJECTS WHERE STATUS = &amp;apos;INVALID&amp;apos;;

6、用inventory命令查看已经安装的patch

   opatch lsinventory

7、卸载补丁.

    1.关闭数据库及监听

    2.卸载补丁.

    Patch Deinstallation Instructions:                                              

    ----------------------------------                                              

    Use the following command:                                                      

      % cd 7650993                                                                  

      % opatch rollback -id 7650993  

    检查无效对象：

    SQL&gt; SELECT OBJECT_NAME FROM DBA_OBJECTS WHERE STATUS = &amp;apos;INVALID&amp;apos;;