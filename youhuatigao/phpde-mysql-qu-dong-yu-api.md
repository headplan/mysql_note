# PHP的MySQL驱动与API

#### MySQL驱动

php5.3之前的mysql驱动是libmysql.dll或.so , 之后版本的驱动是mysqlnd

**之前的版本**

`/ext/mysql /ext/mysqli /pdo/mysql`都是用C写出来的扩展 , 也叫PHP的API , 这三者都是用了libmysql\(MySQL Client Library\) . 

**mysqlnd**

Mysqlnd 不是一个新的 PHP 扩展 , 也不是一个新的 API , 它是新的 C 级库代码 . mysqlnd 库提供的功能与 libmysql 几乎相同 . 两个 C 库都实现了 mysql 通信协议 , 可用于连接到 mysql 服务器 . 

它是php源码数的一部分 , 是专门针对php而写的 , 当然也是采用了php的license .

**总结**

php有多套操作数据库的API ,  包括mysql ,mysqli ,pdo而在这套之下 , 有两个数据库驱动 , 分别是libmysql和mysqlnd , 具体是哪个驱动 , 有编译的时候决定 . 

#### PHP的API

mysql , mysqli , pdo\_mysql

**mysql扩展**

这是设计开发允许PHP应用与MySQL数据库交互的早期扩展 . mysql扩展提供了一个面向过程的接口 , 并且是针对MySQL4.1.3或更早版本设计的 . 因此 , 这个扩展虽然可以与MySQL4.1.3或更新的数据库服务端进行交互 , 但并不支持后期MySQL服务端提供的一些特性

**mysqli扩展**

相对于mysql扩展的提升主要有 : 

* 面向对象接口
* **prepared语句**支持\(关于prepare请参阅mysql相关文档\) 预处理 . 
* **多语句执行**支持
* **事务**支持
* 增强的调试能力
* 嵌入式服务支持

**PDO扩展**

PDO提供了一个统一的API接口可以使得你的PHP应用不去关心具体要连接的数据库服务器系统类型 . 也就是说 , 如果你使用PDO的API , 可以在任何需要的时候无缝切换数据库服务器 , 比如从Firebird 到MySQL , 仅仅需要修改很少的PHP代码 . 

其他数据库抽象层的例子包括Java应用中的JDBC以及Perl中的DBI . 

当然 , PDO也有它自己的先进性 , 比如一个干净的 , 简单的 , 可移植的API , 它最主要的缺点是会限制让你不能使用后期MySQL服务端提供所有的数据库高级特性 . 比如 , PDO不允许使用MySQL支持的多语句执行 . 



