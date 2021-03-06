# MyISAM锁机制

#### MyISAM存储引擎的锁机制

MyISAM存储引擎只支持表锁 .

**查询表级锁争用情况**

可以通过检查`table_locks_waited`和`table_locks_immediate`状态变量来分析系统上的表锁定争夺 :

show status like 'table%' 查询锁的争用情况

Table\_locks\_waited的值如果比较高 , 说明存在着较严重的表级锁争用情况 . 怎么才算高? 常用的算法是 :

`table_locks_immediate/table_locks_waited < 5000`说明有比较验证的表锁的争夺情况 , 这时候可能就需要变更查询结构 , 存储引擎 , 或者增加机器等 .

![](/assets/biaosuochaxun.png)

**锁模式**

MySQL的表级锁有两种模式 : **表共享读锁**\(Table Read Lock\)和**表独占写锁**\(Table Write Lock\) . 可以简单的理解为读锁和写锁 .

MyISAM在执行查询语句\(SELECT\)前 , 会自动给涉及的所有表加读锁 , 在执行更新操作\(UPDATE、DELETE、INSERT等\)前 , 会自动给涉及的表加写锁 .

所以对MyISAM表进行操作 , 会有以下情况 :

* a.对MyISAM表的读操作\(加读锁\) , 不会阻塞其他进程对同一表的读请求 , 但会阻塞对同一表的写请求 . 只有当读锁释放后 , 才会执行其它进程的写操作 . 
* b.对MyISAM表的写操作\(加写锁\) , 会阻塞其他进程对同一表的读和写操作 , 只有当写锁释放后 , 才会执行其它进程的读写操作 .

简单来说就是 :

读锁可以被读操作共享 , 不影响 , 但读锁会影响写操作 , 等读完了才能写 .

写锁会影响所有的读写操作 , 就是我写的时候 , 得等我写完了之后 , 你才能写或者读 .

常在网上看到的说MyISAM引擎适合读比较多的 , 写比较少的 , 就是因为**表共享读锁** .

#### 并发插入

原则上数据表有一个读锁时 , 其它进程无法对此表进行更新操作 , 但在一定条件下 , MyISAM表也支持查询和插入操作的并发进行 . MyISAM存储引擎有一个系统变量`concurrent_insert`, 专门用以控制其并发插入的行为 , 其值分别可以为0 , 1或2 .

* 当concurrent\_insert设置为0时 , 不允许并发插入 . 
* 当concurrent\_insert设置为1时 , 如果MyISAM表中没有空洞\(即表的中间没有被删除的行\) , MyISAM允许在一个进程读表的同时 , 另一个进程从表尾插入记录 . 这也是MySQL的默认设置 . 
* 当concurrent\_insert设置为2时 , 无论MyISAM表中有没有空洞 , 都允许在表尾并发插入记录 . 

`concurrent_insert`可以在my.ini中设置 .

#### MyISAM的锁调度

由于MySQL认为写请求一般比读请求要重要 , 所以如果有读写请求同时进行的话 , MYSQL将会优先执行写操作 . 这样MyISAM表在进行大量的更新操作时\(特别是更新的字段中存在索引的情况下\) , 会造成查询操作很难获得读锁 , 从而导致查询阻塞 .

我们可以通过一些设置来调节MyISAM的调度行为 :

a.通过指定启动参数low-priority-updates , 使MyISAM引擎默认给予读请求以优先的权利 .

b.通过执行命令SET LOW\_PRIORITY\_UPDATES=1 , 使该连接发出的更新请求优先级降低 .

