#### 8.Concurrency {#8-concurrency}

Locks

1\. ROW SHARE ：允许并发访问锁定的表，但禁止会话锁定整个表进行独占访问。

2\. ROW EXCLUSIVE：与ROW SHARE 相同，但是还禁止以SHARE 模式锁定。更新、插入或删除数据时会自动获取ROW EXCLUSIVE 锁定。

3\. SHARE：允许并发查询，但禁止更新锁定的表。需要有（并且会自动请求）SHARE锁定才能创建表的索引。

4\. SHARE ROW EXCLUSIVE：用于查询整个表，允许其他人查询表中的行，但禁止其他人在SHARE 模式下锁定表或更新行。

5\. EXCLUSIVE：允许查询锁定表，但禁止对锁定表执行任何其它活动。需要有EXCLUSIVE 锁定才能删除表。

DML Locks

DML 锁定,每个DML 事务处理会获取两个锁定：

1\. 针对正在更新的一行或多行的EXCLUSIVE 行锁定

2\. 针对要更新的表的ROW EXCLUSIVE 表级锁定。这可避免在进行更改时另一会话

锁定整个表（可能会删除或截断表）。