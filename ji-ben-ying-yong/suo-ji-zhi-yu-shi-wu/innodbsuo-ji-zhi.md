# InnoDB的锁机制

innodb的锁是行锁 , 也有表锁 .

查询行级锁争用情况 `show status like 'innodb_row_lock%';`

![](/assets/innodbsuo.png)

* Innodb\_row\_lock\_current\_waits - 当前的等待锁的数量
* Innodb\_row\_lock\_time - 锁的总时间
* Innodb\_row\_lock\_time\_avg - 锁的平均时间
* Innodb\_row\_lock\_time\_max - 锁的最大时间
* Innodb\_row\_lock\_waits - 等待的数量

innodb的锁分为**共享锁**和**排他锁**

* 共享锁\(S\) : 允许一个事务去读一行 , 阻止其他事务获得相同数据集的排他锁 . 
* 排他锁\(X\) : 允许获得排他锁的事务更新数据 , 阻止其他事务取得相同数据集的共享读锁和排他写锁 . 

另外 , 为了允许行锁和表锁共存 , 实现多粒度锁机制 , InnoDB还有两种内部使用的意向锁\(Intention Locks\) , 这两种意向锁都是表锁 . 意向锁是innodb自动处理 , 无需人工干预 .

* 意向共享锁\(IS\) : 事务打算给数据行加行共享锁 , 事务在给一个数据行加共享锁前必须先取得该表的IS锁 . 
* 意向排他锁\(IX\) : 事务打算给数据行加行排他锁 , 事务在给一个数据行加排他锁前必须先取得该表的IX锁 . 

**解释 : **

从sql语句上来说update , delete操作 , 会自动给涉及到的数据集加上排他锁 , 而普通的select则不加任何锁 .

所谓的数据集 , 如 update的操作 , where的id是3 , 如果id没有加索引 , 这种情况优化器无法确认索引 , 全表都会加上排它锁 , 有索引则自动给涉及到的数据加排它锁 . 普通的select则不加任何锁 .

**总结 , 在update和delete操作时 , 排他锁不影响select , 一样可以查数据 . **

**那insert会加什么锁呢 ? **

主键用的是自增 , 会在内存中保存自增的值 , 插入的时候用的表锁 , 也就是说 , 只要用了自增 , 插入的时候就会锁表 . 除非不用 , 或用程序控制 .

配置选项 innodb\_autoinc\_lock\_mode 可以用来在使用auto\_increment自增的情况下调整锁策略的 :

* innodb\_autoinc\_lock\_mode = 0 \(“traditional” lock mode\)      \# 仍然是表锁
* innodb\_autoinc\_lock\_mode =  1 \(“consecutive” lock mode\)  \# 默认方式, 只锁分配自增的过程 , 可一次分配多个
* innodb\_autoinc\_lock\_mode = 2 \(“interleaved” lock mode\)     \# 只锁分配自增的过程 , 来一个分配一个

#### 总结

1.innodb引擎有表锁和行锁

2.行锁不影响读操作 , 只影响写操作 , 这里只是有多个update操作才会有锁 , 读不影响 .

3.当执行某写操作 , 如果有索引 , 则根据索引锁住数据所在的行 , 如果没有索引 , 则锁住整张表 . 表锁不是由InnoDB存储引擎层管理的 , 而是由其上一层的MySQL Server负责的 , 仅当autocommit=0、innodb\_table\_locks=1（默认设置）时 , InnoDB层才能知道MySQL加的表锁 , MySQL Server也才能感知InnoDB加的锁 , 这种情况下 , InnoDB才能自动识别涉及表级锁的死锁 . 否则 , InnoDB将无法自动检测并处理这种死锁 . 这种就需要人工介入了 .

4.mvcc , 当有个写锁存在的时候 , 读操作会去快照取数据 , 只要写操作没有commit前 , 都会取到写操作之前的数据 .

5.表锁不影响读操作

