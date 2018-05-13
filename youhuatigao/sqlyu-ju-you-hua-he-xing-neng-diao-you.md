# SQL语句优化和性能调优

首先分析一下 : 一个sql查询通过php的api扔给mysql后 , 到最后php收到返回结果 , 这段时间的长短会直接影响到整个代码的执行时间 , 而这些时间都包含哪些呢?

1.从php到mysql的网络层

2.mysql解析语句 , 优化 , 生成执行计划

**3.执行**

4.返回结果

而整个周期中执行和返回结果算是重中之重了 , 执行阶段会有很多操作 , 包括锁等待 , 资源不足是io瓶颈等等 , 先来看看执行阶段 .

执行 , 就是SQL语句的执行逻辑 , 看一下下面SQL的执行逻辑 :

```
select u.name i.expression 
    from user u 
    left join userinfo i 
    on u.id=i.uid 
    where u.id in (1,3,4,55,67,76) 
    order by u.id 
    limit 10;
```

1.FROM子句 - 对其后面的左表user和右表执userinfo行笛卡尔积 , 产生虚拟表VT1

2.ON子句 - 对VT1中的数据根据ON的条件进行过滤 , 产生虚拟表VT2 .

3.JOIN子句 - 将未符合条件的保留表中的数据添加到VT2中 , 剩下的形成VT3

4.WHERE子句 - 对VT3中的数据进行WHERE条件过滤 , 形成VT4

5.GROUP BY子句 - 对VT4中的数据进行分组操作 , 然后形成VT5

6.CUBE \| ROLLUP子句 - 进行操作形成VT6

7.HAVING - 对VT6中的数据进行HAVING条件过滤 , 然后形成VT7

8.SELECT - 从VT7中选择要获取的字段 , 然后形成VT8

9.DISTINCT - 去重数据 , 形成VT9

10.ORDER BY - 对VT9的结果排序后 , 形成VT10

11.LIMIT - 从VT10中取出指定的数据 , 形成VT11 , 返回给用户

#### SQL语句优化

**explain**

显示了mysql如何使用索引来处理select语句以及连接表 . 可以帮助选择更好的索引和写出更优化的查询语句 .

```
EXPLAIN SELECT s.uid,s.username,s.name,f.email,f.mobile,f.phone,f.postalcode,f.address
FROM uchome_space AS s,uchome_spacefield AS f
WHERE 1 
AND s.groupid=0
AND s.uid=f.uid
```

![](/assets/explain.png)**1. id**

SELECT识别符 . 这是SELECT查询序列号 . 这个不重要 , 查询序号即为sql语句执行的顺序 , 看下面这条sql

`EXPLAIN SELECT * FROM (SELECT*FROMuchome_spaceLIMIT10) AS s`

它的执行结果为

![](/assets/selectid.png)

可以看到这时的id变化了

