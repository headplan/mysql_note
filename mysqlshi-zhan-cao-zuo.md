# 实际应用

#### 安装

> 操作版本:5.7

**版本概念**

MySQL Community Server - 社区版,开源免费

MySQL Enterprise Edition - 企业版,付费

MySQL Cluster - 集群免费版

MySQL Cluster CGE - 集群收费版

**下载地址**

[https://www.mysql.com/downloads/](https://www.mysql.com/downloads/)

进入下面的社区版

[https://dev.mysql.com/downloads/](https://dev.mysql.com/downloads/)

**选择MySQL Community Server是编译安装**

* 选择对应版本下载安装 , 选择Source Code下的通用版 , 包含Boost Headers : 
  * \*\*Generic Linux \(Architecture Independent\), Compressed TAR Archive  
    Includes Boost Headers\*\*
  * `wget -c https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-boost-5.7.22.tar.gz`

依赖工具 : CMake

`yum install cmake -y`

> [https://cmake.org/](https://cmake.org/)

还依赖C++的Boost类库 , 这里下载的带有Boost Headers , 就不用安装了 , 具体可以查看 :

> [https://www.boost.org/](https://www.boost.org/)

其他依赖 : 也是在cmake时可能会报的错 .

```
yum install git -y
yum install gcc gcc-c++ -y
yum install ncurses ncurses-devel -y
yum install bison -y

# 搜索准确的名称
yum search c++
```

> 网上找的依赖总结 :
>
> ```
> 安装确保以下系统相关库文件
> gcc gcc-c++ autoconf automake zlib* libxml* ncurses-devel libmcrypt* libtool*(libtool-ltdl-devel*)
>
> # yum –y install gcc gcc-c++ autoconf automake zlib* libxml* ncurses-devel libmcrypt* libtool* cmake
> ```

建立mysql安装目录及数据存放目录

```
# mkdir /usr/local/mysql
# mkdir -p /data/mysql/data
```

创建用户和用户组

```
# groupadd mysql
# useradd -g mysql mysql -M -s /sbin/nologin
-g 用户组
-M 代表不生成Home目录
-s 代表这个用户不能登录
```

赋予数据存放目录权限

```
# chown mysql.mysql -R /data/mysql
```

编辑MySQL :

```
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DMYSQL_DATADIR=/data/mysql/data \
-DMYSQL_UNIX_ADDR=/tmp/mysqld.sock \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DEXTRA_CHARSETS=all \
-DENABLED_LOCAL_INFILE=1 \
-DWITH_BOOST=boost
```

```
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql # 指定安装路径
-DMYSQL_UNIX_ADDR=/tmp/mysqld.sock      # MySQL进程间通信的套接字的位置
-DDEFAULT_CHARSET=utf8                  # 默认字符集 
-DDEFAULT_COLLATION=utf8_general_ci     # 默认的字符集排序规则
-DEXTRA_CHARSETS=all                    # 安装所有扩展字符集
-DWITH_MYISAM_STORAGE_ENGINE=1          # 安装MyIASM引擎
-DWITH_INNOBASE_STORAGE_ENGINE=1        # 安装InnoDB引擎
-DWITH_MEMORY_STORAGE_ENGINE=1          # 安装MEMORY引擎
-DWITH_READLINE=1                       # 使用readline库,就是命令行操作的ctrl+a,ctrl+e
-DENABLED_LOCAL_INFILE=1                # 允许才能够本地导入数据
-DMYSQL_DATADIR=/data/mysql/data        # 数据文件存放路径
-DMYSQL_USER=mysql \                    # 数据库用户
-DMYSQL_TCP_PORT=3306                   # TCP端口
-DWITH_BOOST=boost                      # 使用boost类库,因为前面下载的带有Boost Headers
```

> 更多参数
>
> [http://www.linuxeye.com/Linux/MySQL-cmake-options.html](http://www.linuxeye.com/Linux/MySQL-cmake-options.html)

**编译安装**

    # 查看cpu是几核的
    cat /proc/cpuinfo | grep processor | wc -l
    # 比如是4核的
    # make -j 4
    # make -j `cat /proc/cpuinfo | grep processor | wc -l`

    # 最后
    make install

**变更权限**

```
# chown mysql.mysql -R /usr/local/mysql
```

**配置文件**

```
# 默认配置文件会根据前面的配置,在/etc/my.cnf
# 对其进行配置
配置文件详解在后面的优化部分.
```

**启动脚本**

```
cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
# 给权限
# chmod a+x /etc/init.d/mysqld
# 开机启动
chkconfig --level 345 mysqld on
```

**设置环境变量**

```
echo "export PATH=/usr/local/mysql/bin/:$PAHT" >> /etc/profile
source /etc/profile

# 改错了/bin/vi /etc/profile,把错的改回来.
```

**初始化MySQL**

```
mysqld --initialize-insecure --user=mysql --basedir=/usr/local/mysql --datadir=/data/mysql/data
# 这里初始化使用--initialize会随机生成一个密码
```

**启动MySQL**

```
service mysqld start
```

---

**选择Yum Repository是YUM安装**

* 选择对应版本下载安装

> **安装文档**
>
> [https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/](https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)

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

**使用工具新建数据库.**

