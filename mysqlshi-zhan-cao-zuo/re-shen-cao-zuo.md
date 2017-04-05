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



