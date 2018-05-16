# 基本内容

#### 数据准备

**查看该库所有表的基本状态,包括引擎**

```
show table status from 数据库名
show ENGINES; # 查看数据库支持的引擎
```

新建两张表,一个是InnoDB一个是MyISAM.\(小技巧,导数据时,用MyISAM,然后再改成InnoDB\)

    CREATE TABLE `user_system_1` (
      `id` INT UNSIGNED NOT NULL AUTO_INCREMENT,
      `username` VARCHAR(30) NOT NULL,
      `password` VARCHAR(30) NOT NULL,
      `register_data` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
      PRIMARY KEY(`id`),
      UNIQUE KEY `index_username` (`username`) USING BTREE
    ) ENGINE=Innodb DEFAULT CHARSET=utf8;

    CREATE TABLE `user_sytsem_2` (
      `id` INT UNSIGNED NOT NULL AUTO_INCREMENT,
      `username` VARCHAR(30) NOT NULL,
      `password` VARCHAR(30) NOT NULL,
      `register_data` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
      PRIMARY KEY(`id`),
      UNIQUE KEY `index_username` (`username`) USING BTREE
    ) ENGINE=MyISAM DEFAULT CHARSET=utf8;

然后写一个存储过程,插入一些数据

```
BEGIN
    SET @num=1;
    WHILE @num<20 DO
        if t=1 then
        insert into user_sys (user_name,user_pass) VALUES (CONCAT('user',@num),'123');
        else
        insert into user_sys2 (user_name,user_pass) VALUES (CONCAT('user',@num),'123');
        end if;
        set @num=@num+1;
    END WHILE;
END
```

两张表都准备100万的数据.

```
# 跑数据时候有个奇怪的问题
Error: Lost connection to MySQL server during query
错误之后,存储过程依然继续,直到数据到达100万.这里的错误修改了几处地方
# show variables like '%timeout%';
# show variables like 'max_allowed_packet'
# set global wait_timeout=60000;
# set global max_allowed_packet = 2*1024*1024

注:其中修改wait_timeout时,需要修改的是interactive_timeout字段,wait_timeout展示的也是interactive_timeout字段的值
```

**创建索引**

名称,字段,索引类型,索引方法

索引类型

Normal - 默认的普通索引

Unique - 唯一索引

FullText - 一般不用,用其他第三方工具

Spatial - 空间索引\(5.7.5以后的版本支持\)

索引方法

Btree , Hash

**完成一个功能**

用户登录,写一个存储过程

1.判断用户名和密码是否匹配,如果匹配则返回该行数据,如果不匹配,则返回一个错误.

2.不管成功不成功都记录一次日志

**MySQL的变量**

会话变量\(还有全局变量\)

```
set @num=1;
select @num;
#==========
set @gid=0;
set @user_name='';
select id,user_name into @gid,@user_name from user_sys where user_name='user666';
select @gid,@user_name;
```

**写一个存储过程规范返回结果**

不管是否匹配成功,都返回一行数据,该行的第一个字段表示了执行结果集的状态.

```
BEGIN
    set @gid=0;
    set @user_name='';
    set @_result='login success';
    select id,user_name into @gid,@user_name from user_sys where user_name=_user_name and user_pass=_user_pass limit 1;
    if @gid=0 then
    set @_result='login error';
    end if;
    select * from (select @_result as _result) a,(select @gid,@user_name) b;
END
call sp_user_login('user666','123');
```

**不管成功不成功,都记录一次日志**

更新刚才的存储过程

```
BEGIN
    set @gid=0;
    set @user_name='';
    set @_result='login success';
    select id,user_name into @gid,@user_name from user_sys where user_name=_user_name and user_pass=_user_pass limit 1;
    if @gid=0 then
    set @_result='login error';
    end if;
    insert into user_log(user_id,user_name,log_type) values(@gid,@user_name,@_result);
    select * from (select @_result as _result) a,(select @gid,@user_name) b;
END
```

如果日志中还需要用户名,可以添加一个冗余字段,减少关联查询,也算是一种优化.

```
# 这样的关联查询,比直接查冗余字段稍慢
select a.user_name,a.id,b.log_date from user_sys a,user_log b 
where a.id=b.user_id order by b.id desc limit 0,10
select * from user_log order by id desc limit 0,10
```

**创建一个存储过程,存入500万登录日志**

```
BEGIN
    SET @num=1;
    WHILE @num<=5000000 DO
    INSERT INTO user_log(log_type,user_id,user_name) VALUES('login success',FLOOR(RAND()*1000000),CONCAT('user',FLOOR(RAND()*1000000)));
    set @num=@num+1;
    END WHILE;
END
```



