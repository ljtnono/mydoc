# MySQL8 主从环境搭建

## 主服务器配置

1、在主服务器创建从服务器登陆用户，并授予同步数据的权限

```bash

# 登陆到主服务器的mysql，并执行如下语句创建用户，授予权限：

CREATE USER '<username>'@'%' IDENTIFIED BY '<password>';

GRANT ALL ON *.* TO '<username>'@'%';

FLUSH PRIVILEGES;

```

2、配置主服务器my.cnf文件

```bash

[mysqld]
# 开启bin日志 （必填）
log-bin = mysql-bin
# server-id  (必填，而且主从服务器的server-id值不能一样)
server-id = 1
# 需要同步的数据库 （可选，不填默认同步所有数据库）
binlog-do-db = db_name
# 不需要同步的数据库 （可选）
binlog-ignore-db = mysql
binlog-ignore-db = information_schema
binlog-ignore-db = performance_schema

```

3、重启主服务器

4、使用mysqldump等手段保证主从数据库要的数据是一致的（如果没有数据可跳过此步骤）

5、配置从服务器my.cnf文件

```bash

[mysqld]
# 开启bin日志 （必填）
log-bin = mysql-bin
# server-id  (必填，而且主从服务器的server-id值不能一样)
server-id = 2
# 需要同步的数据库 （可选，不填默认同步所有数据库）
binlog-do-db = db_name
# 不需要同步的数据库 （可选）
binlog-ignore-db = mysql

```

6、重启从数据库

7、登陆从数据库并设置主从信息

```bash

mysql> CHANGE MASTER TO MASTER_HOST='<master_ip>',
    -> MASTER_PORT=<master_port>,
    -> MASTER_USER='<username>',
    -> MASTER_PASSWORD='<password>',
    -> MASTER_LOG_FILE='mysql-bin.具体数值', MASTER_LOG_POS=具体数值, GET_MASTER_PUBLIC_KEY=1;

mysql> start slave;

mysql> show slave status\G;

# 如果发现 Slave_IO_Running: Yes 和 Slave_SQL_Running: Yes 则说明配置成功

# 以上内容中的具体数值可以通过登陆主数据库使用 show master status来获取，
其中GET_MASTER_PUBLIC_KEY=1 是因为mysql8使用安全密码插件，如果没有使用安全密码插件，则可以不开启此选项

```

