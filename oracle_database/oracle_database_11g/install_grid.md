### install Grid {#install-grid}

binutils-2.17.50.0.6

compat-libstdc++-33-3.2.3

elfutils-libelf-0.125

elfutils-libelf-devel-0.125

elfutils-libelf-devel-static-0.125 gcc-4.1.2

gcc-c++-4.1.2 glibc-2.5-24

glibc-common-2.5

glibc-devel-2.5

glibc-headers-2.5

kernel-headers-2.6.18

ksh-20060214 libaio-0.3.106

libaio-devel-0.3.106

libgcc-4.1.2 libgomp-4.1.2

libstdc++-4.1.2

libstdc++-devel-4.1.2

make-3.81 sysstat-7.0.2

unixODBC-2.2.11

unixODBC-devel-2.2.11

/usr/sbin/groupadd -g 5501oinstall

/usr/sbin/groupadd -g 5502 dba

/usr/sbin/groupadd -g 5503 oper #

/usr/sbin/groupadd -g 5504 asmadmin

/usr/sbin/groupadd -g 5506 asmdba

/usr/sbin/groupadd -g 5505 asmoper

/usr/sbin/useradd -u 5502 -g oinstall -G dba,oper,asmadmin,asmdba,asmoper oracle

uid=5501(oracle) gid=5501(oinstall) groups=5502(dba),5503(oper)

1.Add the following lines to the /etc/security/limits.conf file: (the following example shows the software account owner oracle):

   oracle              soft    nproc   2047

   oracle              hard    nproc   16384

   oracle              soft    nofile  1024

   oracle              hard    nofile  65536

2.Add or edit the following line in the /etc/pam.d/login file, if it does not already exist:

   session    required     pam_limits.so

Oracle Base:

/mount_point/app/software_owner

Oracle Inventory Directory:

For example, if ORACLE_BASE is set to /opt/oracle/11, then the Oracle Inventory directory is created similar to /opt/oracle/oraInventory.

Oracle Home Directory:

oracle_base/product/11.2.0/dbhome_1

/u01/app/oracle/product/11.2.0/grid

export ORACLE_BASE=/u01/app/oracle

export ORACLE_HOME=$ORACLE_BASE/product/11.2.0/grid

export PATH=$PATH:$ORACLE_HOME/bin

export ORACLE_SID=oracle0

/etc/sysctl.conf

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

[root@localhost ~]# oracleasm configure -i

[root@localhost ~]# oracleasm init