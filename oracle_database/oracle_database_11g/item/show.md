#### show {#show}

1 。首先，我们先要知道，sqlplus怎样通过show parameter获得参数的。通过sql_trace的方式可以发现。

sqlplus serol/luo

alter session set sql_trace=true;

show parameter optimizer

NAME TYPE VALUE

------------------------------------ -------------------------------- ------------------------------

optimizer_dynamic_sampling integer 1

optimizer_features_enable string 9.2.0.1

optimizer_index_caching integer 0

optimizer_index_cost_adj integer 1

optimizer_max_permutations integer 2000

optimizer_mode string RULE

exit

到udump底下找到trace文件，里面的最主要的语句是：

SELECT NAME name_col_plus_show_param,DECODE(TYPE,1,&amp;apos;boolean&amp;apos;,2,&amp;apos;string&amp;apos;,3,&amp;apos;integer&amp;apos;,4,&amp;apos;file&amp;apos;,6,&amp;apos;big

integer&amp;apos;,&amp;apos;unknown&amp;apos;) TYPE,VALUE value_col_plus_show_param FROM V$PARAMETER WHERE UPPER(NAME) LIKE UPP

ER(&amp;apos;%optimizer%&amp;apos;) ORDER BY name_col_plus_show_param,ROWNUM

可以看到，sqlplus的show parameter实际上就是查询V$PARAMETER .

2\. 看看 v$parameter的定义。

SELECT * FROM v$fixed_view_definition

WHERE view_name = &amp;apos;V$PARAMETER&amp;apos;;

select NUM , NAME , TYPE , VALUE , ISDEFAULT , ISSES_MODIFIABLE , ISSYS_MODIFIABLE , ISMODIFIED , ISADJUSTED , DESCRIPTION, UPDATE_COMMENT from GV$PARAMETER where inst_id = USERENV(&amp;apos;Instance&amp;apos;)

SELECT * FROM v$fixed_view_definition

WHERE view_name = &amp;apos;GV$PARAMETER&amp;apos;;

select x.inst_id,x.indx+1,ksppinm,ksppity,ksppstvl,ksppstdf,

decode(bitand(ksppiflg/256,1),1,&amp;apos;TRUE&amp;apos;,&amp;apos;FALSE&amp;apos;),

decode(bitand(ksppiflg/65536,3),1,&amp;apos;IMMEDIATE&amp;apos;,2,&amp;apos;DEFERRED&amp;apos;,

3,&amp;apos;IMMEDIATE&amp;apos;,&amp;apos;FALSE&amp;apos;), decode(bitand(ksppstvf,7),1,&amp;apos;MODIFIED&amp;apos;,4,&amp;apos;SYSTEM_MOD&amp;apos;,&amp;apos;FALSE&amp;apos;),

decode(bitand(ksppstvf,2),2,&amp;apos;TRUE&amp;apos;,&amp;apos;FALSE&amp;apos;), ksppdesc, ksppstcmnt

from x$ksppi x, x$ksppcv y

where (x.indx = y.indx) and ( (translate(ksppinm,&amp;apos;_&amp;apos;,&amp;apos;#&amp;apos;) not like &amp;apos;#%&amp;apos;) or (ksppstdf = &amp;apos;FALSE&amp;apos;) )

看到上面最关键的 (translate(ksppinm,&amp;apos;_&amp;apos;,&amp;apos;#&amp;apos;) not like &amp;apos;#%&amp;apos;)的条件了吗？，就是它把开头为&quot;_&quot; 的隐含参数给过滤掉了。当然，ksppstdf = &amp;apos;FALSE&amp;apos; 的条件是为了保证如果已经手动改过隐含参数，show parameter还会显示改隐含参数