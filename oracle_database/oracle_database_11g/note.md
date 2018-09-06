### NOTE {#note}

/opt/oracle11g/product/11.2.0/db_1/sqlplus/admin/glogin.sql

#SET SQLPROMPT &quot;_USER&amp;apos;@&amp;apos;_CONNECT_INDENTIFIER :_DATE&gt;&quot;

alter user scott identified by tiger account unlock

. orenv #change database

list

set line NU

define_editrt=vi

edit

sqlplu user/p @**.sql

函数：

round 四舍五入

trunc 截取

mod 取余数

select 0/0 from dual;

select mod(0,0) drom dual;

047

042

043

SCOTT@oracle1 21-OCT-12 &gt;select replace (phone,substr(phone,4,4),&amp;apos;xxxx&amp;apos;) from phone;

REPLACE(PHONE,SUBSTR(PHONE,4,4),&amp;apos;XXXX&amp;apos;)

--------------------------------------------------------------------------------

135xxxx8787

135xxxx8789