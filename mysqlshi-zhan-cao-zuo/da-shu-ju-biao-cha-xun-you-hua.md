# 大数据表查询优化

```
# 使用前面创建过的user_sys表为例
# 需求设计:查询一个没页20条的用户列表
select * from user_sys2 limit 0,20;
# 检查指标EXPLAIN
explain select * from user_sys2 limit 0,20;
# 这里查询出的指标,只记住几个字段就可以
# table:表
# type:类型
# rows:影响行数
# 类型要重点说一下,
# system > const > eq_ref > ref > fulltext > ref_or_null 
> index_merge > unique_subquery > index_subquery > range > index > ALL
# ALL是全表扫描,至少也要range.
# const:根据主键或唯一索引只取出确定的一行数据,最快的一种.
explain select * from user_sys2 where id=1555;
explain select * from user_sys2 where user_name='user1'; # user_name是唯一索引
# range:索引或主键在某个范围内时

# index:仅仅只有索引被扫描
# ALL是最慢的
explain select * from user_sys2
explain select * from user_sys2 limit 0,20000 # 不管取出多少条也是全表扫描
select * from user_sys2 limit 0,20;
```



