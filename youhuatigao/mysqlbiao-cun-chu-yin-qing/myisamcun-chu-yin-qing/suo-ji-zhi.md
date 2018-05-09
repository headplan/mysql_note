# MyISAM锁机制

**什么是锁机制?**

数据库锁定机制简单来说就是数据库为了保证数据的一致性而使各种共享资源在被并发访问访问变得有序所设计的一种规则 . 

如果同时三个连接进来了 , 一个在执行

* a.update table  set  pos = 1  where  id =250;
* b.select pos from table where id =250;
* c.insert into table \(pos\) values \(12\);

那会发生什么事情?

#### MYISAM存储引擎的锁机制

MySQL有三种锁的级别：页级、表级、行级。

* MyISAM和MEMORY存储引擎采用的是表级锁\(table-level locking\)
* BDB存储引擎采用的是页面锁\(page-level locking\) , 但也支持表级锁
* InnoDB存储引擎既支持行级锁\(row-level locking\) , 也支持表级锁 , 但默认情况下是采用行级锁

MySQL这3种锁的特性可大致归纳如下 : 

* 表级锁 : 开销小 , 加锁快 . 不会出现死锁 . 锁定粒度大 , 发生锁冲突的概率最高 , 并发度最低 . 
* 行级锁 : 开销大 , 加锁慢 . 会出现死锁 . 锁定粒度最小 , 发生锁冲突的概率最低 , 并发度也最高 . 
* 页面锁 : 开销和加锁时间界于表锁和行锁之间 . 会出现死锁 . 锁定粒度界于表锁和行锁之间 , 并发度一般 . 





