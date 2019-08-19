# （三）、mysql-5.7安装文档

- 本示例为CentOS7系统
- 下载的源码包均放在/usr/local/src目录下


### 1. 下载通用二进制发行版包

这个版本不需要我们编译，只需要配置一下就可以使用了。

```
# 推荐在浏览器上下载速度快些
wget http://mirrors.163.com/mysql/Downloads/MySQL-5.7/mysql-5.7.27-el7-x86_64.tar.gz

# 解压
tar -zxvf mysql-5.7.27-el7-x86_64.tar.gz

# 更改目录名
mv mysql-5.7.27-el7-x86_64 mysql-5.7

```

### 2. 安装脚本

vim /usr/local/src/mysql_install.sh

```

#!/bin/sh

REMOVE=`rpm -qa | grep -i mariadb-libs`
# 编译
yum remove $REMOVE -y
yum install libaio -y
yum -y install numactl.x86_64

DIRNAME='mysql-5.7'

# 下载
mv $DIRNAME mysql
mv mysql /usr/local/

useradd -s/sbin/nlogin -M mysql
id mysql

mkdir /usr/local/mysql/{data,log}
chown -R mysql.mysql /usr/local/mysql/

# 编辑my.cnf
cat << EOF > /etc/my.cnf
[client]
port = 3306
socket = /tmp/mysql.sock
 
[mysqld]
server_id=10
port = 3306
user = mysql
character-set-server = utf8mb4
default_storage_engine = innodb
log_timestamps = SYSTEM
socket = /tmp/mysql.sock
basedir = /usr/local/mysql
datadir = /usr/local/mysql/data/
pid-file = /usr/local/mysql/data/mysql.pid
max_connections = 1000
max_connect_errors = 1000
table_open_cache = 1024
max_allowed_packet = 128M
open_files_limit = 65535
log-bin=mysql-bin
#####====================================[innodb]==============================
innodb_buffer_pool_size = 1024M
innodb_file_per_table = 1
innodb_write_io_threads = 4
innodb_read_io_threads = 4
innodb_purge_threads = 2
innodb_flush_log_at_trx_commit = 1
innodb_log_file_size = 512M
innodb_log_files_in_group = 2
innodb_log_buffer_size = 16M
innodb_max_dirty_pages_pct = 80
innodb_lock_wait_timeout = 30
innodb_data_file_path=ibdata1:1024M:autoextend
 
#####====================================[log]==============================
log_error = /usr/local/mysql/log/mysql-error.log 
slow_query_log = 1
long_query_time = 1 
slow_query_log_file = /usr/local/mysql/log/mysql-slow.log
 
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
EOF
 
# 初始化
/usr/local/mysql/bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --innodb_undo_tablespaces=3 --explicit_defaults_for_timestamp

# 授权
chown -R mysql:mysql /usr/local/mysql
cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql

chmod +x /etc/init.d/mysql
cp /usr/local/mysql/bin/* /usr/local/sbin/

mkdir -p /etc/systemd/system/mysqld.service.d
cat << EOF > /etc/systemd/system/mysqld.service.d/mysqld.service

# systemctl启动脚本
[Service]
LimitNOFILE=max_open_files
PIDFile=/usr/local/mysql/data/mysql.pid
Nice=nice_level
LimitCore=core_file_limit
Environment="TZ=time_zone_setting"
EOF

systemctl daemon-reload

## 启动服务并查看
/etc/init.d/mysql start
ps -ef |grep mysql
grep "password" /usr/local/mysql/log/mysql-error.log 

```

添加执行权限

```

chmod +x /usr/local/src/mysql_install.sh

```

执行

```

/usr/local/src/mysql_install.sh

```

### 3. 设置root的登录

通过第二步的安装脚本，最后打印了mysql初始化时设置的临时密码。例如：xhuEw4uG94_l

```

2019-08-19T10:43:48.999107-00:00 1 [Note] A temporary password is generated for root@localhost: xhuEw4uG94_l

```
使用这个临时密码登录

```

mysql -uroot -p

```

修改root密码


```

mysql>set password =password('123456');

```

## 安装示例

- [lnmp环境搭建](./README.md)