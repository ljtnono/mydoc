# jira安装

本教程使用docker-compose安装jira，理论上可参考本教程安装Atlassian的各种软件。

## 环境准备

操作系统：centos7

mysql驱动程序jar包：

```bash

wget https://repo1.maven.org/maven2/mysql/mysql-connector-java/8.0.30/mysql-connector-java-8.0.30.jar

```

这里并不需要下载补丁包，因为已经将破解补丁集成到docker镜像里面去了。


## 编写docker-compose.yml文件

```yml

version: '3'

networks:
  jira-network:

services:

  mysql:
    image: mysql:8.0.30
    restart: always
    tty: true
    hostname: mysql
    ports: 
      - 30044:3306
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    volumes:
      - ./mysql/my.cnf:/etc/my.cnf
      - ./mysql/data:/var/lib/mysql

  jira:
    image: ljtnono/jira:latest
    restart: always
    tty: true
    hostname: jira
    ports:
      - 30045:8080
    volumes:
      - ./jira/data:/var/atlassian/application-data/jira
      - ./libs/mysql-connector-java-8.0.30.jar:/opt/atlassian/jira/atlassian-jira/WEB-INF/lib/mysql-connector-java-8.0.30.jar

```

这里定义了两个服务，mysql和jira，上面下载的两个jar包也通过数据卷挂载到jira容器中了。

## 创建相应的目录

```bash

# 创建libs文件夹，将mysql驱动存放在这个目录下
mkdir libs
mv mysql-connector-java-8.0.30.jar libs

# 创建mysql和jira数据目录，用于保存mysql和jira的数据
mkdir -p jira/data
mkdir -p mysql/data


# 在mysql目录下创建my.cnf，内容如下

[mysqld]

default-storage-engine=INNODB
character_set_server=utf8mb4
innodb_default_row_format=DYNAMIC
innodb_log_file_size=2G


```


## 启动服务

```bash

docker-compose up -d

```

## 查看容器是否启动成功

```bash

docker-compose ps

```

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16734074068008.jpg)


## 创建jira数据库

连接docker-compose配置的数据库，并执行如下SQL语句：

```sql

-- 创建jira数据库及用户
DROP DATABASE IF EXISTS jira;
CREATE USER 'jira' IDENTIFIED BY 'jira#mysql';
CREATE DATABASE jira CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,REFERENCES,ALTER,INDEX on jira.* TO 'jira'@'%';
FLUSH PRIVILEGES;

```


## 访问jira进行配置

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16734073733852.jpg)

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16734074885993.jpg)

## 破解步骤

配置进入到如下页面时，需要进行破解

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16734089535816.jpg)

进入到jira所在docker容器执行命令：

```bash

# 这里需要事先查看一下jira的docker容器的名称，我这里是jira-jira-1
# 进入容器
docker exec -it jira-jira-1 bash
# 进入破解jar包所在目录
cd /opt/atlassian/jira
# 执行命令
java -jar atlassian-agent.jar -d -m test@test.com -n BAT -p jira -o http://localhost:8081 -s BYV4-KGF7-D4GO-0Y4Y

```

执行成功会返回许可证，填入到jira的配置框里面即可。



## 安装插件

1、在插件市场点击插件立即购买

2、在应用管理中找到该应用程序，并复制该应用程序的应用秘钥

3、在容器内执行命令

```bash

java -jar atlassian-agent.jar -m test@test.com -n BAT -p <应用密钥> -o http://:localhost:8081 -s BYV4-KGF7-D4GO-0Y4Y

```

4、更新插件密钥
