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