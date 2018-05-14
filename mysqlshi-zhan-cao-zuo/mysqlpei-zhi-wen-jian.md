# MySQL配置文件

```
[client] 
# 客户端
# 包含端口
# socket文件位置
# 默认字符集,也可以设置为utf8mb4
port = 3306
socket = /tmp/mysql.sock
default-character-set = utf8

[mysql]
# 命令行
# prompt配置命令行前缀显示方式
# MySQL [\d]> 显示:MySQL [use的数据库]>
# mysql(\\u@\\h:\\d)> 显示:mysql(root@localhost:use的数据库)>
# no-auto-rehash 关闭命令补全功能,auto-rehash为开启
prompt="MySQL [\d]> "
no-auto-rehash

[mysqld]
# 服务端
# 包含端口
# socket文件位置
port = 3306
socket = /tmp/mysql.sock

# basedir:安装路径
# datadir:数据文件
# pid-file:存放进程ID的文件
# user:用户
# bind-address:MySQL服务器的IP地址,默认是0000,表示谁都可以访问,前提是端口可以访问
# server-id:表示是本机的序号为1,一般来讲就是master的意思
basedir = /usr/local/mysql
datadir = /data/mysql
pid-file = /data/mysql/mysql.pid
user = mysql
bind-address = 0.0.0.0
server-id = 1

# init-connect:连接初始化,这里设置了utf8,也可以记录登录日志等.
# 例如:'insert into accesslog.accesslog values(null,connection_id(),now(),user(),current_user());'
# 然后还是设置字符集
init-connect = 'SET NAMES utf8'
character-set-server = utf8

# skip-name-resolve:禁止MySQL对外部连接进行DNS解析,使用这一选项可以消除MySQL进行DNS解析的时间.但需要注意,如果开启该选项,
# 则所有远程主机连接授权都要使用IP地址方式,否则MySQL将无法正常处理连接请求.
# skip-networking:开启该选项可以彻底关闭MySQL的TCP/IP连接方式,
# 如果WEB服务器是以远程连接的方式访问MySQL数据库服务器则不要开启该选项,否则将无法正常连接
# back_log:指定MySQL可能的连接数量.当MySQL主线程在很短的时间内接收到非常多的连接请求,该参数生效.
# 主线程花费很短的时间检查连接并且启动一个新线程.
# back_log参数的值指出在MySQL暂时停止响应新请求之前的短时间内多少个请求可以被存在堆栈中.
# 如果系统在一个短时间内有很多连接,则需要增大该参数的值,该参数值指定到来的TCP/IP连接的侦听队列的大小.
# 不同的操作系统在这个队列大小上有它自己的限制.
# 试图设定back_log高于你的操作系统的限制将是无效的.默认值为50.
# 对于Linux系统推荐设置为小于512的整数。
skip-name-resolve
#skip-networking
back_log = 300


max_connections = 332
max_connect_errors = 6000
open_files_limit = 65535
table_open_cache = 128
max_allowed_packet = 500M
binlog_cache_size = 1M
max_heap_table_size = 8M
tmp_table_size = 16M

read_buffer_size = 2M
read_rnd_buffer_size = 8M
sort_buffer_size = 8M
join_buffer_size = 8M
key_buffer_size = 4M

thread_cache_size = 8

query_cache_type = 1
query_cache_size = 8M
query_cache_limit = 2M

ft_min_word_len = 4

log_bin = mysql-bin
binlog_format = mixed
expire_logs_days = 7

log_error = /data/mysql/mysql-error.log
slow_query_log = 1
long_query_time = 1
slow_query_log_file = /data/mysql/mysql-slow.log

performance_schema = 0
explicit_defaults_for_timestamp

#lower_case_table_names = 1

skip-external-locking

default_storage_engine = InnoDB
innodb_file_per_table = 1
innodb_open_files = 500
innodb_buffer_pool_size = 64M
innodb_write_io_threads = 4
innodb_read_io_threads = 4
innodb_thread_concurrency = 0
innodb_purge_threads = 1
innodb_flush_log_at_trx_commit = 2
innodb_log_buffer_size = 2M
innodb_log_file_size = 32M
innodb_log_files_in_group = 3
innodb_max_dirty_pages_pct = 90
innodb_lock_wait_timeout = 120

bulk_insert_buffer_size = 8M
myisam_sort_buffer_size = 8M
myisam_max_sort_file_size = 10G
myisam_repair_threads = 1

interactive_timeout = 28800
wait_timeout = 28800

[mysqldump]
quick
max_allowed_packet = 500M

[myisamchk]
key_buffer_size = 8M
sort_buffer_size = 8M
read_buffer = 4M
write_buffer = 4M
```



