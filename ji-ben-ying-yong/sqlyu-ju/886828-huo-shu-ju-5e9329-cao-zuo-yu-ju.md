# 表\(或数据库\)操作语句

#### 查询表（或数据库）

* 获取所有可用的数据库：`SHOW DATABASES;`
* 选择数据库：`USE customers;`
* 用于显示数据库服务器的状态信息：`SHOW STATUS;`
* 用来显示授权用户的安全权限：`SHOW GRANTS;`
* 用来显示数据库服务器或警告信息：`SHOW ERRORS;` 或者 `SHOW WARNINGS;`
* 用于显示创建数据库时的创建语句：`SHOW CREATE DATABASE customers;`
* 用于显示创建表时的创建语句：`SHOW CREATE TABLE customers;`
* 获取当前所选的数据库中所有可用的表：`SHOW TABLES;`
* 获取表中所有列的信息：`SHOW COLUMNS FROM tableName;`同时DESCRIBE语句有相同的效果：`DESCRIBE tableName;`

#### 新建表（或）数据库

1.新建数据库：`CREATE DATABASE customers;`

2.创建表可以使用CREATE TABLE语句 :

```SQL
CREATE TABLE test_table (
    test_id INT NOT NULL AUTO_INCREMENT,
    test_name CHAR(50) NOT NULL,
    test_age INT NULL DEFAULT 18,
    PRIMARY KEY (test_id)
) ENGINE=INNODB;
```

注意细节 :

* 允许NULL值 , 则说明在插入行数据时允许不给出该列的值 , 而NOT NULL则表示在插入或者更新该列数据 , 必须明确给出该列的值;
* DEFAULT表示该列的默认值 , 在插入行数据时 , 若没有给出该列的值就会使用其指定的默认值;
* PRIMARY KEY用于指定主键 , 主键可以指定一列数据 , 而可以由多列数据组合构成 , 如`PRIMARY KEY(cust_id,cust_name);`
* ENGINE用于指定引擎类型 . 常见的引擎类型有这些：
  * InnoDB是一个支持可靠的事务处理的引擎 , 但是不支持全文本搜索 , 5.6.4版本以后支持;
  * MyISAM是一个性能极高的引擎 , 它支持全文本搜索 , 但是不支持事务处理;
  * MEMORY在功能上等同于MyISAM , 但由于数据存储在内存中 , 速度很快\(特别适合于临时表\);

3.在创建表的时候可以使用`FOREIGN KEY`来创建外键 , 即一个表中的`FOREIGN KEY`指向另一个表中PRIMARY KEY . 外键`FOREIGN KEY`用于约束破坏表的联结动作 , 保证两个表的数据完整性 . 同时也能防止非法数据插入外键列 , 因为该列值必须指向另一个表的主键

```SQL
CREATE TABLE orders (
  orders_id INT NOT NULL AUTO_INCREMENT,
  orders_name VARCHAR(50) NOT NULL,
  orders_age INT NULL DEFAULT 20,
  test_orders_id INT NOT NULL,
  PRIMARY KEY(orders_id),
  FOREIGN KEY(test_orders_id) REFERENCES test_table(test_id)
) ENGINE=INNODB;
```

#### 删除表（或数据库）

* 删除数据库：`DROP DATABASE customers;`
* 删除表 , 使用DROP TABLE子句：`DROP TABLE customers;`

#### 更新表

1.更新表结构信息可以使用ALTER TABLE子句 , 如为表增加一列 : `ALTER TABLE vendors ADD vend_name CHAR(20);` , 另外经常用于定义外键 , 如 : 

```
ALTER TABLE test_table
ADD test_message VARCHAR(50) NOT NULL, # 添加字段
ADD test_sex TINYINT(1) NULL DEFAULT 0,
ADD test_test VARCHAR(255) NULL DEFAULT NULL,
CHANGE test_age test_age_change INT, # 修改字段名,需添加类型
MODIFY test_name VARCHAR(50), # 修改字段类型
;
ALTER TABLE test_table
DROP test_test # 删除字段
;
ALTER TABLE test_table
ADD CONSTRAINT test_orders # 添加外键
FOREIGN KEY(test_id) REFERENCES orders (order_id)
;
```

2.重命名表 , 使用RENAME子句 . `RENAME TABLE backup_customers TO customers,backup_vendors TO vendors;`更改多个表名 , 之间用逗号间隔 . 

```
ALTER TABLE test_table
ADD test_message VARCHAR(50) NOT NULL,
ADD test_sex TINYINT(1) NULL DEFAULT 0,
ADD test_test VARCHAR(255) NULL DEFAULT NULL,
CHANGE test_age test_age_change INT,
MODIFY test_name VARCHAR(50),
;
ALTER TABLE test_table
DROP test_test
;
ALTER TABLE test_table
ADD CONSTRAINT test_orders
FOREIGN KEY(test_id) REFERENCES orders (order_id)
;
RENAME TABLE user_sys TO user_sys_one,user_sys2 TO user_sys_two;
```



