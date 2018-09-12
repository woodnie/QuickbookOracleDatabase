### Concept {#concept}

**USER_SYNONYMS**

USER_SYNONYMS describes theprivate synonyms (synonyms owned by the current user). Its columns () are thesame as those in ALL_SYNONYMS.

About Dynamic Performance Views

Oracle contains a setof underlying views that are maintained by the database server and accessibleto the database administrator user SYS. These views are calleddynamicperformance views because they are continuously updated while a database isopen and in use, and their contents relate primarily to performance.

Although these viewsappear to be regular database tables, they are not. These views provide data oninternal disk structures and memory structures. You can select from theseviews, but you can never update or alter them.

**V$ Views**

The actual dynamic performance views are identified bythe prefix V_$. Public synonyms for these views have the prefixV$. Database administrators and other users should access only theV$objects, not theV_$ objects.

**USER_OBJECTS**

USER_OBJECTS describes all objects owned by the current user. Its columns are the sameas those in&quot;ALL_OBJECTS&quot;.

**About Static Data Dictionary Views**

Data dictionary tables are not directly accessible, but you can access information in them through data dictionary views. To list the data dictionary views available to you, query the view DICTIONARY.

Many data dictionary tables have three corresponding views:

·        An **ALL_view** displays all the information accessible to the current user, including information from the current user&amp;apos;s schema as well as information from objectsin other schemas, if the current user has access to those objects by way of grants of privileges or roles.

·        A **DBA_view** displays all relevant information in the entire database. DBA_ views are intended only for administrators. They can be accessed only by users with the SELECT ANY TABLE privilege. This privilege is assigned to the DBA role when the system is initially installed.

·        A **USER_view** displays all the information from the schema of the current user. Nospecial privileges are required to query these views.

The columns of the **ALL_, DBA_, and USER_ views** corresponding to a single data dictionary table are usually nearlyidentical. Therefore, these views are described in full only once in thischapter, at their first occurrence alphabetically, and are listed without fulldescriptions at their other occurrences.

#### View {#view}

[http://blog.csdn.net/tianlesoftware/article/details/5530618](http://blog.csdn.net/tianlesoftware/article/details/5530618)

一. 视图的定义

      视图(view)，也称虚表, 不占用物理空间，这个也是相对概念，因为视图本身的定义语句还是要存储在数据字典里的。视图只有逻辑定义。每次使用的时候, 只是重新执行SQL.

      视图是从一个或多个实际表中获得的，这些表的数据存放在数据库中。那些用于产生视图的表叫做该视图的基表。一个视图也可以从另一个视图中产生。

      视图的定义存在数据库中，与此定义相关的数据并没有再存一份于数据库中。通过视图看到的数据存放在基表中。

      视图看上去非常象数据库的物理表，对它的操作同任何其它的表一样。当通过视图修改数据时，实际上是在改变基表中的数据；相反地，基表数据的改变也会自动反映在由基表产生的视图中。由于逻辑上的原因，有些Oracle视图可以修改对应的基表，有些则不能（仅仅能查询）。

      还有一种视图：物化视图（MATERIALIZED VIEW ），也称实体化视图，快照 （8i 以前的说法） ，它是含有数据的，占用存储空间。 关于物化视图，具体参考我的blog：

      Oracle 物化视图

      [http://blog.csdn.net/tianlesoftware/archive/2009/10/23/4713553.aspx](http://blog.csdn.net/tianlesoftware/archive/2009/10/23/4713553.aspx)

#### MATERIALIZED VIEW {#materialized-view}