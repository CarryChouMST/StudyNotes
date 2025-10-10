- 希望将多个表的查询结果放在一张表里，如果是纵向合并，用UNION ALL；如果是横向合并，用JOIN。

- ~~~sql
  WITH t AS (
  SELECT id, timestamp FROM partitions WHERE partition_key = 'xxx' LIMIT 1
  )
  SELECT a.*, t.timestamp
  FROM main_table a
  JOIN t ON a.partition_id = t.id;
  
  如果希望有多个中间表，可以用多个WITH，中间用逗号（，），保证语句的唯一一条。
  ~~~

- 