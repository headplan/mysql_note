# InnoDB存储引擎的事务

#### 事务理解

从ATM机上取钱,我们的操作步骤通常是 插卡/输入密码/选取款/选金额/收钱/取卡 , 经过一些列的操作之后 , 在钱取到了 , 才会扣钱 , 中间的所有以外 , 都会回到原来的初始化 .

**事务就是为了让数据库从一种一致的状态切换到另一种一致的状态 . **

前面ATM的例子 , 就是这一系列的操作比如 取款和卡中余额减少,要么都成功,要门都不成功 .

**如果让你来实现事务的逻辑 , 会怎么操作?**

一个旅行购票的例子 :

1. 先买北京到成都的机票 当天
2. 再买成都到绵阳的火车票/汽车票 当天
3. 再买绵阳到九寨沟的大巴票 第二天

如果绵阳到九寨沟的大巴票第二天买不到了 , 那就直接回滚了 , 全退了 .

这是事务的**扁平化事务** , 只要一个步骤不行了 , 全部重头再来 .

```
BEGIN WORK
action # 判断订购到成都的机票
action # 判断订购到绵阳的火车票
action # 判断订购到九寨沟的汽车票
COMMIT WORK
```

如果第三个步骤有可选项 , 如果第二天买不到的 , 第三天的也行 , 这时候要是扁平化事务的时候 , 代价会有点大 .

这时候其实可以用**待保存点的扁平化事务**

```
BEGIN WORK
SAVEPOINT  first(保存点)
action #订购到成都的机票
SAVEPOINT  second(保存点)
action #订购到绵阳的火车票
SAVEPOINT  third(保存点)
action #订购到九寨沟的汽车票
ROLLBACK TO second(保存点)
COMMIT WORK
```

走到第三部时 , 失败了 , 返回到second\(保存点\) , 不必重头开始 .

除了前两种 , 还有一种叫**分布式事务**

比如 , 网银转账的过程 , 比如从工行转到农行1w , 步骤如下 :

* A.网银发出转账操作
* B.工行余额减少1w
* C.农行余额增加1w
* D.网银提示操作成功

数据库不是同一个 , 就用到了分布式事务 .

**事务类型INNODB支持 扁平化事务 /带保存点的扁平化事务 / 链式事务 / 分布式事务**

在实际应用操作过程中 , 用到的最多最频繁的就是**扁平化事务**了

基本命令 :

```
BEGIN;
ACTION;
ACTION;
COMMIT/ROLLBACK;
```

> 在INNODB存储引擎中** , **任何一个SQL语句 , 数据库都默认加了BEGIN和COMMIT , 也就是隐式提交 , 当然定义了BEGIN等就是显示提交

---

### 事务的隔离级别

事务的隔离级别有四种 :

* 非提交读 read uncommitted 会带来脏读 
* 提交读     read committed  有版本控制,但会不重复读
* 可重复读 read \(默认\) 会幻读
* 序列化     serializable 符合事务的四大原则,但是读的时候会加锁,一般会认为级别越过效率越低.

版本控制是在执行某个sql的时候 , 通过索引找到数据集 , 产生一个快照或者说版本

只有提交读和可重复读会采用mvcc机制

非提交读 , 序列化会加锁 , 让数据不可变

**SQL操作**

```
show variables like 'tx_isolation' 查看隔离界别设置
select @@tx_isolation; 查看当前回话的隔离级别
select @@global.tx_isolation 查看系统隔离界别

设置隔离级别
set session transaction isolation level xxx 设置当前回话隔离级别
set global transaction isolation level xxx 设置系统的隔离级别
set global tx_isolation='READ-UNCOMMITTED'; 未提交读
set global tx_isolation='READ-COMMITTED';  
set global tx_isolation='REPEATABLE-READ';
set global tx_isolation='SERIALIZABLE';
```

#### **图标理解**

**未提交读（READ-UNCOMMITTED）**

| 操作类型 | 事务提交前其他人是否可以看到 | 其他人更新记录后是否影响这次事务结果 | 后果 |
| :--- | :--- | :--- | :--- |
| 插入 | 可以 | 会 | 脏读，不可重复读、幻读 |
| 更新 | 可以 | 会 |  |
| 删除 | 看不到记录 | 会 |  |

结论 : 安全性最低，性能最快，一般不用这个级别，会导致很多问题

**提交读/不可重复读（READ-COMMITTED/NOREPEATABLE READ）**

| 操作类型 | 事务提交前其他人是否可以看到 | 其他人更新记录后是否影响这次事务结果 | 后果 |
| :--- | :--- | :--- | :--- |
| 插入 | 不可以 | 会 | 不可重复读、幻读 |
| 更新 | 不可以 | 会 |  |
| 删除 | 还能看到记录 | 会 |  |

结论：安全性略低，性能一般，很多数据用这个级别（mysql不是）

**可重复读（REPEATABLE-READ）**

| 操作类型 | 事务提交前其他人是否可以看到 | 其他人更新记录后是否影响这次事务结果 | 后果 |
| :--- | :--- | :--- | :--- |
| 插入 | 不可以 | 不会 | 幻读（实际数据没了或改变了，但这个事务的数据未提交前不会变化） |
| 更新 | 不可以 | 不会 |  |
| 删除 | 还能看到记录 | 不会 |  |

结论 : 安全性一般，性能较快，mysql默认这个级别

**串行化（SERIALIZABLE）**

| 操作类型 | 事务提交前其他人是否可以看到 | 其他人更新记录后是否影响这次事务结果 | 后果 |
| :--- | :--- | :--- | :--- |
| 插入 | 不可以 | 阻塞 | 安全性最高，性能最差 |
| 更新 | 不可以 | 阻塞 |  |
| 删除 | 还能看到记录 | 阻塞 |  |

结论 : 应用于安全度很高的项目
