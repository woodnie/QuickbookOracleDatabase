#### analyze {#analyze}

analyze table t compute statistics = analyze table t compute statistics for table for all indexes for all columns

for table 的统计信息存在于视图：user_tables 、all_tables、dba_tables

for all indexes的统计信息存在于视图: user_indexes 、all_indexes、dba_indexes

for all columns的统计信息存在于试图：user_tab_columns、all_tab_columns、dba_tab_columns

当analyze table t delete statistics 会删除所有的statistics