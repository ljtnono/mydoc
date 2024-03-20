# MySQL8安装教程

## centos7下MySQL最小安装

1、在官网下载最小安装包，并解压

```shell
wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.25-linux-glibc2.17-x86_64-minimal.tar.xz

tar xvf mysql-8.0.25-linux-glibc2.17-x86_64-minimal.tar.xz
mv mysql-8.0.25-linux-glibc2.17-x86_64-minimal.tar.xz mysql8
```

2、初始化mysql

```bash
groupadd mysql
useradd -r -g mysql -s /bin/false mysql
# 进入mysql安装路径,将常用的my.cnf配置文件拷贝到mysql安装目录中
mkdir logs
mkdir data
touch logs/slow_sql.log
touch logs/mysql.log
chown -R mysql:mysql .
# --datadir为mysql安装目录下的data文件夹
bin/mysqld --initialize --user=mysql --datadir=<basedir>/data
bin/mysql_ssl_rsa_setup --datadir=<basedir>/data
# 修改启动脚本中的basedir为mysql安装目录，datadir为mysql安装目录下的data目录
# vim support-files/mysql.server
# basedir=<basedir>
# datadir=<datadir>
# 启动mysql
support-files/mysql.server start --socket=<basedir>/mysql.sock
```

3、修改root用户密码，设置远程访问

```bash
# 其中<password>是初始化时的root密码，可以在logs/mysql.log文件中查看
bin/mysql -uroot -p'<password>' --socket=<basedir>/mysql.sock
```

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED BY '<你的密码>'
USE mysql;
UPDATE user SET Host = '%' WHERE User = 'root';
FLUSH PRIVILEGES;
```

## macos下mysql最小安装

1、在官网下载最小安装包，并解压到自定义安装目录

下载地址：https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.26-macos11-x86_64.tar.gz

```shell
# 这里使用wget命令下载
cd /Users/lingjiatong/software
wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.26-macos11-x86_64.tar.gz
tar zxvf mysql-8.0.26-macos11-x86_64.tar.gz
mv mysql-8.0.26-macos11-x86_64.tar.gz mysql8
```

2、编辑mysql配置文件，并创建相关的目录文件夹和data文件夹

```bash
mkdir logs
touch logs/mysql.log
touch logs/slow_sql.log
```

在mysql8目录下编辑my.cnf文件，内容如下：

```mysql
# 将以下<baseDir>改为自己的mysql安装路径
[mysqld]
socket = <baseDir>/mysql.sock
basedir = <baseDir>
datadir = <baseDir>/data
# 去除groupBy 需要选择字段
sql_mode = STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
# 慢sql日志
slow_query_log_file = <baseDir>/logs/slow_sql.log
slow_query_log = on
long_query_time = 3
secure_file_priv = <baseDir>
# pid 设置
pid-file = <baseDir>/mysql.pid
[mysqld_safe]
log-error=<baseDir>/logs/mysql.log
```

3、初始化mysql数据库

```bash
cd mysql8
bin/mysqld --initialize --user=lingjiatong # 这里lingjiatong改为自己macos用户名
# 然后系统会打印出来root账户的密码，后面直接按照linux安装教程中的更改root初始密码的流程就完成了
```

4、修改启动脚本

```bash
vim support-files/mysql.server
# 修改如下两个变量的值为相应的值
basedir=<basedir>
datadir=$basedir/data
```

后续跟linux一样，使用support-files/mysql.server 脚本进行开启和关闭mysql



## Debian12中mysql安装

1、在官网下载最小安装包，解压，安装依赖项

```shell
wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.25-linux-glibc2.17-x86_64-minimal.tar.xz
tar xvf mysql-8.0.25-linux-glibc2.17-x86_64-minimal.tar.xz
mv mysql-8.0.25-linux-glibc2.17-x86_64-minimal.tar.xz mysql8
# 安装依赖项
sudo apt-get install -y libaio1 libncurses6 libnuma1 sysv-rc-conf libncurses5
```

2、编辑my.cnf文件

```txt
# 将以下<baseDir>改为自己的mysql安装路径，<username>改为登录用户名
[mysqld]
user = <username>
socket = <baseDir>/mysql.sock
basedir = <baseDir>
datadir = <baseDir>/data
# 去除groupBy 需要选择字段
sql_mode = STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
# 慢sql日志
slow_query_log_file = <baseDir>/logs/slow_sql.log
slow_query_log = on
long_query_time = 3
secure_file_priv = <baseDir>
# pid 设置
pid-file = <baseDir>/mysql.pid
[mysqld_safe]
log-error=<baseDir>/logs/mysql.log
```

3、初始化mysql

```bash
# 由于debian的用户权限非常严格，重新创建一个mysql用户运行软件会产生各种权限问题，这里建议直接用登录的shell用户运行mysql
# 进入mysql安装路径,将常用的my.cnf配置文件拷贝到mysql安装目录中
mkdir logs
mkdir data
touch logs/slow_sql.log
touch logs/mysql.log
chown -R lingjiatong:lingjiatong .
# --datadir为mysql安装目录下的data文件夹
bin/mysqld --initialize --user=lingjiatong fpeXhZ&MB5vq
bin/mysql_ssl_rsa_setup --datadir=<basedir>/data
# 修改启动脚本中的basedir为mysql安装目录，datadir为mysql安装目录下的data目录
# vim support-files/mysql.server
# basedir=<basedir>
# datadir=<datadir>
# 启动mysql
support-files/mysql.server start
```

4、修改root用户密码，设置远程访问

```bash
# 其中<password>是初始化时的root密码，可以在logs/mysql.log文件中查看
bin/mysql -uroot -p'<password>' -P <port> --socket=mysql.sock
```

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED BY '<你的密码>'
USE mysql;
UPDATE user SET Host = '%' WHERE User = 'root';
FLUSH PRIVILEGES;
```



## 常用my.cnf配置文件

```bash
# 将以下<baseDir>改为自己的mysql安装路径
[mysqld]
port = 3306
socket = <baseDir>/mysql.sock
basedir = <baseDir>
datadir = <baseDir>/data
# 去除groupBy 需要选择字段
sql_mode = STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
# 慢sql日志
slow_query_log_file = <baseDir>/logs/slow_sql.log
slow_query_log = on
long_query_time = 3
secure_file_priv = <baseDir>
# pid 设置
pid-file = <baseDir>/mysql.pid
[mysqld_safe]
log-error=<baseDir>/logs/mysql.log
```
