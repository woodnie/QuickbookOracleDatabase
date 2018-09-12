#### rlwrap tool {#rlwrap-tool}

[root@oracle ~]# yum install  readline*  

[root@oracle ~]# wget [http://utopia.knoware.nl/~hlub/rlwrap/rlwrap-0.37.tar.gz](http://utopia.knoware.nl/~hlub/rlwrap/rlwrap-0.37.tar.gz)

[root@oracle ~]# tar -xvzf rlwrap-0.37.tar.gz

[root@oracle ~]# cd rlwrap-0.37

[root@oracle rlwrap-0.37]# ./configure

[root@oracle rlwrap-0.37]# make

[root@oracle rlwrap-0.37]# make install

[oracle@oracle ~]$ vi .bashrc # 添加以下内容

alias sqlplus=&amp;apos;rlwrap sqlplus&amp;apos;

alias rman=&amp;apos;rlwrap rman&amp;apos;