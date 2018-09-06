### install {#install}

1.install required packages(RHEL5)

yum install binutils compat-libstdc++-33 elfutils-libelf elfutils-libelf-devel elfutils-libelf-devel-static gcc gcc-c++ glibc glibc-common glibc-devel glibc-headers kernel-headers ksh libaio libaio-devel libgcc libgomp libstdc++ libstdc++-devel make numactl-devel sysstat

binutils-2.17.50.0.6

compat-libstdc++-33-3.2.3

elfutils-libelf-0.125

elfutils-libelf-devel-0.125

elfutils-libelf-devel-static-0.125

gcc-4.1.2

gcc-c++-4.1.2

glibc-2.5-24

glibc-common-2.5

glibc-devel-2.5

glibc-headers-2.5

kernel-headers-2.6.18

ksh-20060214

libaio-0.3.106

libaio-devel-0.3.106

libgcc-4.1.2

libgomp-4.1.2

libstdc++-4.1.2

libstdc++-devel-4.1.2

make-3.81

numactl-devel-0.9.8.i386

sysstat-7.0.2

2.

/etc/security/limits.conf

oracle              soft    nproc   2047

oracle              hard    nproc   16384

oracle              soft    nofile  1024

oracle              hard    nofile  65536

oracle              soft    stack   10240

3.

fs.aio-max-nr = 1048576

fs.file-max = 6815744

kernel.shmall = 2097152

kernel.shmmax = 536870912

kernel.shmmni = 4096

kernel.sem = 250 32000 100 128

net.ipv4.ip_local_port_range = 9000 65500

net.core.rmem_default = 262144

net.core.rmem_max = 4194304

net.core.wmem_default = 262144

net.core.wmem_max = 1048586

5\. 配置/etc/pam.d/login  session  required  pam_limits.so    

6.创建组 oinstall dba oper    

[root@oracle ~]# /usr/sbin/groupadd oinstall# /usr/sbin/groupadd dba      

7.创建oracle用户：

[root@oracle ~]#  /usr/sbin/useradd -g oinstall -G dba oracle        

8.创建必要的安装路径

[root@oracle ~]# mkdir -p /opt/oracle11g

[root@oracle ~]# chown -R oracle:oinstall /opt/oracle11g

[root@oracle ~]# chmod -R 775 /opt/oracle11g    

9.配置环境变量：[root@oracle ~]# vi ~oracle/.bash_profile      ORACLE_BASE      ORACLE_HOME      ORACLE_SID      

 ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

环境变量实例:  

export ORACLE_BASE=/opt/oracle11g

export ORACLE_HOME=$ORACLE_BASE/product/11.2.0/db_1

export PATH=$PATH:$ORACLE_HOME/bin

export ORACLE_SID=oracle0

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++