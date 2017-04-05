# 基本概念

> 操作版本:5.7

**版本概念**

MySQL Community Server - 社区版,开源免费

MySQL Enterprise Edition - 企业版,付费

MySQL Cluster - 集群免费版

MySQL Cluster CGE - 集群收费版

**下载地址**

[https://www.mysql.com/downloads/](https://www.mysql.com/downloads/)

选择Yum Repository

直接在下面下载rpm包即可.

**安装文档**

[https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/](https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)

**密码修改与设置**

在配置文件中添加`skip-grant-tables`跳过密码验证

新版本的用户密码字段变为:authentication\_string

> 老版本的user表中的password字段代表了用户的密码

**修改密码方式**

```
update mysql.user set
authentication_string=PASSWORD('123456')
where user='root';
# 刷新权限
flush privileges;
```

**设置远程连接权限**

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
FLUSH PRIVILEGES;
# 如果报错,说明使用了上面不正规的密码修改方式,使用下面的方式重新修改密码,然后再设置远程连接权限
ALTER USER USER() INDENTIFIED BY '123456';
```

**这里使用到了一个函数user\(\),查看当前的用户**

```
SELECT user();
```

使用工具新建数据库.

**数据库引擎**

InnoDB和MyISAM,暂时知道两点

1.前者支持外键和事务,后者不支持

2.事务性表应该使用InnoDB.频繁读取,如select操作很频繁的应该使用MyISAM引擎

**查看该库所有表的基本状态,包括引擎**

```
show table status from 数据库名
show ENGINES; # 查看数据库支持的引擎
```

新建两张表,一个是InnoDB一个是MyISAM.\(小技巧,导数据时,用MyISAM,然后再改成InnoDB\)

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

**创建索引**

名称,字段,索引类型,索引方法

索引类型

Normal - 默认的普通索引

Unique - 唯一索引

FullText - 一般不用,用其他第三方工具

Spatial - 空间索引\(5.7.5以后的版本支持\)

索引方法

Btree , Hash



