# InnoDB存储引擎

#### 字段数据类型

没太大区别 .

#### INNODB的锁机制

innodb的锁是行锁 , 也有表锁 . 

查询行级锁争用情况 `show status like 'innodb_row_lock%';`

![](/assets/innodbsuo.png)



* Innodb\_row\_lock\_current\_waits - 当前的等待锁的数量
* Innodb\_row\_lock\_time - 锁的总时间
* Innodb\_row\_lock\_time\_avg - 锁的平均时间
* Innodb\_row\_lock\_time\_max - 锁的最大时间
* Innodb\_row\_lock\_waits - 等待的数量



