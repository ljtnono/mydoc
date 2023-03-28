# MySQL高级教程（尚硅谷宋红康）

## 第1章 Linux下MySQL的安装与使用

### 1 安装前说明

#### 1.1 Linux系统及工具的准备

* 安装并启动好两台虚拟机：**CentOS 7**
* 安装shell访问工具
* 修改主机名便于区分

#### 1.2 查看是否安装过MySQL

* 如果使用rpm安装，检查一下RPM PACKAGE：

```bash

rpm -qa | grep -i mysql # -i 忽略大小写

```

* 检查mysql.service：

```bash

systemctl status mysqld.service

```

* 如果存在mysql-libs的旧版本包，显示如下：

<div align = "center">

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16432057493165.jpg)

</div>

* 如果不存在mysql-lib的版本，显示如下：

<div align = "center">

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16432058119063.jpg)

</div>

#### 1.3 Linux系统下安装mysql

1. 关闭mysql服务

```bash

systemctl stop mysqld.service

```

2. 查看当前mysql安装状况

```bash

rpm -qa | grep -i mysql

# 或

yum list installed | grep mysql

```

3. 卸载上述命令查询出的已安装程序

```bash

yum remove mysql-xxx mysql-xxx

```

1. 删除mysql相关文件

* 查找相关文件

```bash

find / -name mysql

```

* 删除上述命令查找出相关文件

```bash

rm -rf xxx

```

5. 删除my.cnf

```bash

rm -rf /etc/my.cnf

```

### 2 MySQL的Linux版安装

#### 2.1 MySQL的四大版本

> * **MySQL Community Server 社区版本**，开源免费，自由下载，但不提供官方技术支持，适用于大多数普通用户。
> * **MySQL Enterprise Edition 企业版本**，需付费，不能在线下载，可以试用30天。提供了更多的功能和更完备的技术支持，更适合于对数据库的功能和可靠性要求较高的企业客户。
> * **MySQL Cluster 集群版**，开源免费。用于架设集群服务器，可将几个MySQL Server封装成一个Server。需要在社区版或企业版的基础上使用。
> * **MySQL Cluster CGE 高级集群版**，需付费。

#### 2.3 CentOS7检查MySQL依赖

1. 检查/tmp临时目录权限（必不可少）

由于mysql安装过程中，会通过mysql用户在/tmp目录下新建tmp_db文件，所以请给/tmp较大的权限。执行：

```bash

chmod -R 777 /tmp

```

1. 安装前，检查依赖

需要检查libaio和net-tools两个依赖项

```bash

rpm -qa | grep libaio

rpm -qa | grep net-tools

```

* 如果存在libaio包如下：

<div align = "center">

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16432086702992.jpg)

</div>

* 如果不存在则需要使用**yum install**命令进行安装

#### 2.4 CentOS7下MySQL安装过程

以MySQL8.0.25为例（mysql5.7可能有部分步骤不一致）

1. 去官网下载相关rpm整合压缩包

2. 解压压缩包并按照以下顺序安装rpm包

```bash

rpm -ivh mysql-community-common-8.0.25-1.el7.x86_64.rpm 
rpm -ivh mysql-community-client-plugins-8.0.25-1.el7.x86_64.rpm 
rpm -ivh mysql-community-libs-8.0.25-1.el7.x86_64.rpm 
rpm -ivh mysql-community-client-8.0.25-1.el7.x86_64.rpm 
rpm -ivh mysql-community-server-8.0.25-1.el7.x86_64.rpm 

```

3. 查看MySQL版本

```bash

mysql --version

```

4. 服务初始化


```bash

mysql --initialize --user=mysql

```

查看密码：

```bash

cat /var/log/mysqld.log

```

5. 启动MySQL并修改密码

```bash

mysql -uroot -p'xxx'

ALTER USER 'root'@'localhost' IDENTIFIED BY 'xxxx';

exit

systemctl enable mysqld

systemctl restart mysqld

```

6. 开启MySQL远程连接

```bash

# 开启防火墙端口

firewall-cmd --add-port=3306/tcp --zone=public --permanent

firewall-cmd --reload

# 开启root用户远程访问

mysql -uroot -p'xxx'

use mysql;

UPDATE user SET Host = '%' WHERE User = 'root';

FLUSH PRIVILEGES;

ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'abc123';

```

### 5 字符集的相关操作

#### 5.1 修改MySQL5.7字符集

1. 修改步骤

在MySQL8.0版本之前，默认字符集为**latin1**，utf8字符集指向的是utf8mb3。网站开发人员在数据库设计的时候往往会将编码修改为utf8字符集。如果遗忘修改默认的编码，就会出现乱码的问题。从MySQL8.0开始，数据库的默认编码将改为**utf8mb4**，从而避免乱码问题。

**操作1：查看默认使用的字符集**

```bash

SHOW VARIABLES LIKE '%CHARACTER%'

```

* MySQL8.0中执行：

<div align = "center">

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16432113083970.jpg)


</div>

* MySQL5.7中执行：

<div align = "center">

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16432113611709.jpg)


</div>

MySQL5.7默认的服务端用的**latin1**,不支持中文，保存中文会报错：

```bash

CREATE TABLE emp1(
    id int,
    lname VARCHAR(15)
);

INSERT INTO emp1(id, lname) VALUES (1, 'Tom'), (2, '测试数据');

# 报错

[HY000][1366] Incorrect string value: '\xE6\xB5\x8B\xE8\xAF\x95...' for column 'lname' at row 2

```

**操作2：修改字符集**

```bash

vim /etc/my.cnf

```

在MySQL5.7或之前的版本中，在文件最后加上中文字符集配置：

```bash

[mysqld]
character_set_server = utf8mb4

```

**操作3：重新启动MySQL服务**

```bash

systemctl restart mysqld

```

> 但是原库，原表的设定不会发生变化，参数修改只对新建的数据库生效，所以如果使用MySQL5.x版本，最后在安装完毕之后立刻将字符集修改为utf8


2. 已有库&表字符集的变更

MySQL5.7版本中，以前创建的库，创建的表字符集还是latin1。

修改已创建的数据库的字符集

```bash

ALTER DATABASE dbtest1 CHARACTER SET 'utf8';

```

修改已创建表的字符集

```bash

ALTER TABLE emp1 COVERT TO CHARACTER SET 'utf8';

```

> 注意：但是原有的数据如果使用非'utf8'编码的话，数据本身编码不会发生改变。已有数据需要导出或删除，然后重新插入。


#### 5.2 各级别的字符集

MySQL有4个级别的字符集和比较规则，分别是：

* 服务器级别
* 数据库级别
* 表级别
* 列级别

执行如下SQL语句：

```bash

SHOW VARIABLES LIKE 'character%';

```

<div align = "center">

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16432126563770.jpg)

</div>

* character_set_server：服务器级别的字符集
* **character_set_database：当前数据库的字符集**
* character_set_client：服务器解码请求时使用的字符集
* character_set_connection：服务器处理请求时会把请求字符串从character_set_client转为character_set_connection
* character_set_results：服务器向客户端返回数据时使用的字符集

##### 1. 服务器级别

* **character_set_server**：服务器级别的字符集。

我们可以在启动服务器程序是通过启动选项或者在服务器程序运行过程中使用**SET**语句修改这两个变量的值。比如我们可以在配置文件中这样写：

```bash

[server]
character_set_server=gbk # 默认字符集
collation_server=gbk_chinese_ci # 对应的默认的比较规则

```
当服务器启动的时候读取这个配置文件后这两个系统变量的值便修改了。

##### 2. 数据库级别

* **character_set_database**：当前数据库的字符集

我们在创建和修改数据库的时候可以指定该数据库的字符集和比较规则，具体语法如下：

```bash

CREATE DATABASE 数据库名
    [[DEFAULT] CHARACTER SET 字符集名称]
    [[DEFAULT] COLLATE 比较规则名称]

ALTER DATABASE 数据库名
    [[DEFAULT] CHARACTER SET 字符集名称]
    [[DEFAULT] COLLATE 比较规则名称]

```

##### 3. 表级别

我们也可以在创建和修改表的时候指定表的字符集和比较规则，语法如下：

```bash

CREATE TABLE 表名 (列信息)
    [[DEFAULT] CHARACTER SET 字符集名称]
    [COLLATE 比较规则名称]

ALTER TABLE 表名
    [[DEFAULT] CHARACTER SET 字符集名称]
    [COLLATE 比较规则名称]

```

**如果创建和修改表的语句中没有指明字符集和比较规则，将使用该表所在数据库的字符集和比较规则作为该表的字符集和比较规则。**

##### 4. 列级别

对于存储字符串的列，同一个表中的不同的列也可以有不同的字符集和比较规则。我们在创建和修改列定义的时候可以指定该列的字符集和比较规则，语法如下：

```bash

CREATE TABLE 表名(

    列名 字符串类型 [CHARACTER SET 字符集名称] [COLLATE 比较规则名称]

)

ALTER TABLE 表名 MODIFY 列名 字符串类型 [CHARACTER SET 字符集名称] [COLLATE 比较规则名称]

```

##### 小结

* 如果**创建或修改列**时没有显示指定字符集和比较规则，则该列**默认用表的**字符集和比较规则
* 如果**创建或修改表**时没有显示指定字符集和比较规则，则该表**默认用数据库的**字符集和比较规则
* 如果**创建数据库**时没有显示指定字符集和比较规则，则该数据库**默认用服务器的**字符集和比较规则

#### 5.3 字符集和比较规则

##### 1. utf8与utf8mb4

**utf8**字符集表示一个字符需要使用1～4个字节，但是我们常用的一些字符使用1-3个字节就可以表示了。而字符集表示一个字符所用的最大字节长度，在某些方面会影响系统的存储和性能，所以设计MySQL的设计者定义了两个概念：

* **utf8mb3**：阉割过的utf8字符集，只使用1～3个字节表示字符。
* **utf8mb4**：正宗的utf8字符集，使用1～4个字节表示字符。

#### 5.4 请求到响应过程中字符集的变化

<div align = "center" style="margin: auto">

|系统变量|描述|
|:-:|:-:|
|**character_set_client**|服务器解码请求时使用的字符集|
|**character_set_connection**|服务器处理请求时会把请求字符串从**character_set_client**转为**character_set_connection**|
|**character_set_client**|服务器向客户端返回数据时使用的字符集|


![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16432680018123.jpg)

</div>


#### 7 sql_mode的合理设置

##### 7.1 宽松模式 VS 严格模式

**宽松模式**

如果设置的是宽松模式，那么我们在插入数据的时候，即便是给了一个错误的数据，也可能会被接受，并且不报错。

**举例**：在创建一个表的时候，该表中有一个字段为name，给name设置的字段类型为**char(10)**，如果我在插入数据的时候，其中name这个字段对应的有一条数据的**长度超过了10**，例如'1234567890abc'，超过了设定的字段长度10，那么不会报错，并且取前10个字符存上，也就是说你这个数据被存为了'1234567890'，而'abc'就没有了。但是，我们给的这条数据是错误的，因为超过了字段长度，但是并没有报错，并且mysql自行处理并接受了，这就是宽松模式的效果。

**应用场景**：通过设置sql mode为宽松模式，来保证大多数sql符合标准的sql语法，这样应用在不同数据库之间进行**迁移**时，则不需要对业务sql进行较大的修改。

**严格模式**

出现上面宽松模式的错误，应该报错才对，所以MySQL5.7版本就将sql_mode默认值改为了严格模式。所以在**生产等环境中**，必须采用的是严格模式，进而**开发、测试环境**的数据库也必须要设置，这样在开发测试阶段就可以发现问题。

改为严格模式可能会出现的问题：

若设置模式中包含了**NO_ZERO_DATE**，那么MySQL数据库不允许插入零日期，插入零日期会抛出错误而不是警告。例如，表中包含字段TIMESTAMP列（如果未声明未NULL或显示DEFAULT子句）将自动分配DEFAULT'0000-00-00 00:00:00'（零时间戳），这显然不满足sql_mode中的NO_ZERO_DATE而报错。

**sql_mode详解**

|sql_mode值|描述|
|----------|---|
|ONLY_FULL_GROUP_BY|对于GROUP BY聚合操作，如果在SELECT中的列，没有在GROUP BY中出现，那么这个SQL是不合法的，因为列不在GROUP BY从句中|
|NO_AUTO_VALUE_ON_ZERO|该值影响自增长列的插入。默认设置下，插入0或NULL代表生成下一个自增长值。如果用户 希望插入的值为0，而该列又是自增长的，那么这个选项就有用了。|
|STRICT_TRANS_TABLES|在该模式下，如果一个值不能插入到一个事务表中，则中断当前的操作，对非事务表不做限制|
|NO_ZERO_IN_DATE|在严格模式下，不允许日期或月份为零，只要日期的月或日中含有0值都报错，但是‘0000-00-00'除外|
|NO_ZERO_DATE|设置该值，mysql数据库不允许插入零日期，插入零日期会抛出错误而不是警告。年月日中任何一个不为0都符合要求，只有‘0000-00-00'会报错|
|ERROR_FOR_DIVISION_BY_ZERO|在INSERT或UPDATE过程中，如果数据被零除，则产生错误而非警告。如果未给出该模式，那么数据被零除时MySQL返回NULL，update table set num = 5 / 0 ; 设置该模式后会报错，不设置则修改成功，num的值为null|
|NO_AUTO_CREATE_USER|禁止GRANT创建密码为空的用户|
|NO_ENGINE_SUBSTITUTION|如果需要的存储引擎被禁用或未编译，那么抛出错误。不设置此值时，用默认的存储引擎替代，并抛出一个异常|
|PIPES_AS_CONCAT|将双竖线视为字符串的连接操作符而非或运算符，这和Oracle数据库是一样的，也和字符串的拼接函数Concat相类似|
|ANSI_QUOTES|启用ANSI_QUOTES后，不能用双引号来引用字符串，因为它被解释为识别符|



## 第2章 MySQL的数据目录

### 1 MySQL8的主要目录

#### 1.1 数据库文件的存放路径

**MySQL数据库文件的存放路径：/var/lib/mysql**

<div align = "center">

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16432472920694.jpg)


</div>

#### 1.2 相关命令目录

**相关命令目录：/usr/bin（mysqladmin、mysqlbinlog、mysqldump等命令）和/usr/bin**

#### 1.3 配置文件目录

**配置文件目录：/usr/share/mysql-8.0（命令及配置文件），/etc/mysql/my.cnf**

### 2 数据库和文件系统的关系

#### 2.1 查看默认数据库

```bash

mysql> SHOW DATABASES;

```

可以看到有4个数据库是属于MySQL自带的系统数据库。

* **mysql**

MySQL系统自带的核心数据库，它存储了MySQL的用户账户和权限信息，一些存储过程、事件的定义信息，一些运行过程中产生的日志信息，一些帮助信息以及时区信息等。

* **information_schema**

MySQL系统自带的数据库，这个数据库保存着MySQL服务器**维护的所有其他数据库的信息**，比如有哪些表、哪些视图、哪些触发器、哪些列、哪些索引。这些信息并不是真实的用户数据，而是一些描述性信息，有时候也称为**元数据**。在系统数据库**information_schema**中提供了一些以**innodb_sys**(mysql5)开头的表，用于表示内部系统表。

* **performance_schema**

MySQL系统自带的数据库，这个数据库里主要保存MySQL运行过程中的一些状态信息，可以用来**监控MySQL服务的各类性能指标**。包括统计最近执行了哪些语句，在执行过程的每个阶段都花费了多长时间，内存的使用情况等信息。

* **sys**

MySQL系统自带的数据库，这个数据库主要是通过**视图**的形式把**information_schema**和**performance_schema**结合起来，帮助系统管理员和开发人员监控MySQL的技术性能。


#### 2.3 表在文件系统中的表示

##### 2.3.1 InnoDB存储引擎模式

**1 表结构**

为了保存表结构，**InnoDB**在**数据目录**下对应的数据库子目录下创建一个专门用于**描述表结构的文件**，文件名是这样：

```bash

表名.frm

```

比如说在dbtest1数据库下创建一个名为**test**的表：

```mysql

CREATE TABLE test(c1 INT);

```

那在数据库**dbtest1**对应的子目录下就会创建一个名为**test.frm**的用于描述
表结构的文件。.frm文件的格式在不同的平台上都是相同的。这个后缀名为.frm是以二机制格式存储的，直接打开是乱码。

**2 表中的数据和索引**

1、系统表空间（system tablespace）

默认情况下，InnoDB会在数据目录下创建一个名为**ibdata1**、大小为**12M**的文件，这个文件就是对应的**系统表空间**在文件系统上的表示。这个文件是**自扩展文件**，当不够用的时候它会自己增加文件大小。

2、独立表空间（file-per-table tablespace）

在MySQL5.6.6以及之后的版本中，InnoDB并不会默认的把各个表的数据存储到系统表空间中，而是为**每个表建立一个独立表空间**，也就是说我们创建了多少个表，就有多少个独立表空间。使用**独立表空间**来存储表数据的话，会在该表所属数据库对应的子目录下创建一个表示该独立表空间的文件，文件名和表名相同，完整的文件名称：

```bash

# 表名.ibd
# 例如：
test.frm
test.ibd

```

##### 2.3.2 MyISAM存储引擎模式

在MyISAM中的索引全部都是**二级索引**，该存储引擎的**数据和索引是分开存放的**。所以在文件系统中也是使用不同的文件来存储数据文件和索引文件，同时表数据都存放在对应的数据库子目录下。

```bash

test.frm 存储表结构
test.MYD 存储数据
test.MYI 存储索引

```


#### 2.4 小结

举例：**数据库a，表b**

1、如果表b采用**InnoDB**，data\a中会产生1个或者2个文件：

* **b.frm**：描述表结构文件，字段长度等
* 如果采用**系统表空间**模式的，数据信息和索引信息都存储在**ibdata1**中
* 如果采用**独立表空间**模式的，data\a中还会产生**b.ibd文件**（存储数据信息和索引信息）

此外：

1、MySQL5.7中会在data/a的目录下生成**db.opt**文件用于保存数据库的相关配置。比如：字符集、比较规则。而MySQL8.0不再提供db.opt文件。

2、MySQL8.0中不在单独提供b.frm，而是合并在b.ibd文件中。

3、如果表b采用**MyISAM**，data\a中会产生3个文件：

* MySQL5.7中：**b.frm**：描述表结构文件，字段长度等
  MySQL8.0中：**b.xxx.sdi**：描述表结构文件，字段长度等
* **b.MYD(MYData)**：数据信息文件，存储数据信息（如果采用独立表存储模式）
* **b.MYI(MYIndex)**：存放索引文件



## 第3章 用户与权限管理



### 6 配置文件的适应


## 第4章 逻辑架构

### 1 服务器处理客户端请求

#### 1.1 服务器处理客户端请求

那服务器进程对客户端进程的请求做了什么处理，才能产生最后的处理结果呢？这里以查询请求为例展示：

<div align = "center">

![client_request](media/16421754723998/client_request.png)


![](media/16421754723998/16435109250203.jpg)


</div>

#### 1.3 第一层：连接层

系统（客户端）访问**MySQL**服务器前，做的第一件事就是建立**TCP**连接。

经过三次握手建立连接成功后，**MySQL**服务器对**TCP**传输过来的账号密码做身份认证、权限获取。

* 用户名或密码不对，会收到一个Access denied for user错误，客户端程序结束执行
* 用户名密码认证通过，会从权限表查出账号拥有的权限与连接关联，之后的权限判断逻辑，都将依赖于此时读到的权限

**TCP**连接收到请求后，必须要分配给一个线程专门与这个客户端的交互。所以还会有个线程池，去走后面的流程。每一个连接从线程池中获取线程，省去了创建和销毁线程的开销。

#### 1.4 第二层：服务层

* **SQL Interface: SQL接口**
    * 接收用户的SQL命令，并且返回用户需要查询的结构。比如SELECT...FROM就是调用SQL Interface
    * MySQL支持DML（数据操作语言）、DDL（数据定义语言）、存储过程、视图、触发器、自定义函数等多种SQL语言接口
* **Parser：解析器**
    * 在解析器中对SQL语句进行**语法分析**、**语义分析**。将SQL语句分解成数据结构数据结构，并将这个结构传递到后续步骤，以后SQL语句的传递和处理就是基于这个结构的。如果在分解构成中遇到错误，那么就说明这个SQL语句是不合理的。
    * 在SQL命令传递到解析器的时候会被解析器验证和解析，并为其创建**语法树**，并根据数据字典丰富查询语法树，会**验证该客户端是否具有执行该查询的权限**。创建好语法树后，MySQL还会对SQL查询进行语法上的优化，进行查询重写。
* **Optimizer：查询优化器**
    * SQL语句在语法解析之后、查询之前会使用查询优化器确定SQL语句的执行路径、生成一个**执行计划**。
    * 这个执行计划表明应该**使用哪些索引**进行查询，表之间的连接顺序如何，最后会按照执行计划中的步骤调用存储引擎提供的方法来真正的执行查询，并将查询结果返回给用户。
    * 它使用**选取-投影-连接**策略进行查询。例如：
    
    ```mysql
    
    SELECT id, name FROM student WHERE gender = '女';
    
    ```
    
    这个SELECT查询先根据WHERE语句进行**选取**，而不是将表全部查询出来以后再进行gender过滤。这个SELECT查询先根据id和name进行属性**投影**，而不是将属性全部查询出来以后再进行过滤，将这两个查询条件**连接**起来生成最终查询结果。

* **Caches && Buffers：查询缓存组件**
    * MySQL内部维护着一些Cache和Buffer，比如Query Cache用来缓存一条SELECT语句的执行结果，如果能够在其中找到对应的查询结果，那么就不必再进行查询解析、优化和执行的整个过程了，直接将结果反馈给客户端。
    * 这个缓存机制是由一系列小缓存组成的。比如表缓存，记录缓存，key缓存，权限缓存等。
    * 这个查询缓存可以在**不同客户端之间共享**。
    * 从MySQL5.7.20开始，不推荐使用查询缓存，并在**MySQL8.0中删除**。


#### 1.5 第三层：存储引擎层

插件式存储引擎层，**真正负责了MySQL中数据的存储和提取，对物理服务器级别维护的底层数据执行操作**，服务器通过API与存储引擎进行通信。不同的存储引擎具有的功能不同，这样我们可以根据自己的实际需要进行选取。

MySQL8.0.25默认支持的存储引擎如下：

```bash

mysql> SHOW ENGINES;

```

<div align = "center">

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16433304206281.jpg)


</div>


#### 1.7 小结

MySQL架构图本节开篇所示。下面为了熟悉SQL执行流程方便，我们可以简化如下：


简化为三层架构：

1. 连接层：客户端和服务器端建立连接，客户端发送SQL至服务器端；
2. SQL层（服务层）：对SQL语句进行查询处理；与数据库文件的存储方式无关；
3. 存储引擎层：与数据库打交道，负责数据的存储和读取。


### 2 SQL执行流程

#### 2.1 MySQL中的SQL执行流程



相关步骤解释：

1. **查询缓存**：Server如果在查询缓存中发现了这条SQL语句，就会直接将结果返回给客户端；如果没有，就进入解析器阶段。需要说明的是，因为查询缓存往往效率不高，所以在MySQL8.0之后就抛弃了这个功能。

   查询缓存是提前把查询结果缓存起来，这样下次不需要执行就可以直接拿到结果。需要说明的是，在MySQL中的查询缓存，不是缓存查询计划，而是查询对应的结果。这就意味着查询匹配的**鲁棒性大大降低**，只有**相同的查询操作才会命中查询缓存**。两个查询请求在任何字符上的不同（例如：空格、注释、大小写），都会导致缓存不会命中。因此MySQL的**查询缓存命中率不高**。
   
   同时，如果查询请求中包含某些系统函数、用户自定义变量和函数、一些系统表。如mysql、information_schema、performance_schema数据库中的表，那么这个请求就不会被缓存。以某些系统函数举例，可能同样的函数的两次调用会产生不一样的结果，比如函数**NOW()**，每次调用都会产生最新的当前时间，如果在一个查询请求中调用了这个函数，那即便查询请求的文本信息都一样，也不会将结果缓存。
   
   此外，既然是缓存，那就有它**缓存失效的时候**。MySQL的缓存系统会监测涉及到的每张表，只要该表的结构或者数据被修改，如对该表使用了**INSERT**、**UPDATE**、**DELETE**、**TRUNCATE TABLE**、**ALTER TABLE**、**DROP TABLE**或**DROP DATABASE**语句，那使用该表的所有高速缓存都将变为无效并从高速缓存中删除！对于**更新压力大的数据库**来说，查询缓存的命中率非常低。

2. **解析器**：在解析器中对SQL进行语法分析、语义分析。
   
   分析器先做“**词法分析**”。 你输入的是由多个字符串和空格组成的一条SQL语句，MySQL需要识别出里面的字符串分别是什么，代表什么。MySQL从你输入的"SELECT"这个关键字识别出来，这是一个查询语句。它也要把字符串“T”识别成“表名T”，把字符串“ID”识别成“列ID”。
   
   接着，要做“**语法分析**”。根据词法分析的结果，语法分析器会根据语法规则，判断你输入的这个SQL语句是否**满足MySQL语法**。
   
   语法树图示：
   
3. **优化器**：在优化器中会确定SQL语句的执行路径，比如是根据**全表检索**，还是根据**索引检索**等。

   举例：如下语句是执行两个表的join：
   
   ```mysql
   
   SELECT * FROM test1 JOIN test2 USING(ID)
   WHERE test1.name = 'zhangwei' AND test2.name = 'mysql高级课程';
   
   ```
   
   ```bash
   
   方案1: 可以先从表test1里面取出name = 'zhangwei'的记录的ID值，再根据ID值关联到表test2，再判断test2里面name的值是否等于'mysql高级课程'。
   
   方案2：可以先从表test2里面取出name = 'mysql高级课程'的记录的ID值，再根据ID值关联到test1，再判断test1里面的name的值是否等于zhangwei。
      
   ```
   
   在查询优化器中，可以分为**逻辑查询**优化阶段和**物理查询**优化阶段。
   
4. **执行器**：

   截止到现在，还没有真正去读写真实的表，仅仅只是产生了一个执行计划。于是就进入了**执行器阶段**。
   
   例如：**SELECT * FROM test WHERE id = 1;**
   
   执行流程可能是这样的：
   
   ```bash
      
   调用InnoDB引擎接口取这个表的第一行，判断ID值是不是1，如果不是则跳过，如果是则将这行存在结果集中；调用引擎接口取“下一行”，重复相同的判断逻辑，直到取到这个表的最后一行。
   
   执行器将上述遍历过程中所有满足条件的行组成的记录集作为结果集返回给客户端。
   
   ```
   
   
   
   
   
   ## 第8章 索引的创建与设计原则
   
   ### 1 索引的声明与使用
   
   #### 1.1 索引的分类
   
   MySQL的索引包括普通索引、唯一索引、全文索引、单列索引、多列索引和空间索引等。
   
   * 从**功能逻辑**上说，索引主要有4种，分别是普通索引、唯一索引、主键索引、全文索引。
   * 按照**物理实现方式**，索引可以分为2种：聚簇索引和非聚簇索引。
   * 按照**作用字段个数**进行划分，分成单列索引和联合索引。

   **1. 普通索引**
   
   在创建普通索引时，不附加任何限制条件，只是用于提高查询效率。这类索引可以创建在**任何数据类型中**，其值是否唯一和非空，要由字段本身的完整性约束条件决定。建立索引以后，可以通过索引进行查询。例如，在表**student**的字段**name**上建立一个普通索引，查询记录时就可以根据该索引进行查询。
   
   **2. 唯一性索引**
   
   使用**UNIQUE参数**可以设置索引为唯一性索引，在创建唯一性索引时，限制该索引的值必须是唯一的，但允许有空值。在一张数据表里**可以有多个**唯一索引。
   
   例如，在表**student**的字段**email**中创建唯一性索引，那么字段**email**的值就必须是唯一的。通过唯一性索引，可以更快速的确定某条记录。
   
   **3. 主键索引**
   
   主键索引就是一种**特殊的唯一索引**，在唯一索引的基础上增加了不为空的约束，也就是NOT NULL + UNIQUE，一张表里**最多只有一个**主键索引。
   
   **4. 单列索引**
   
   在表中的单个字段上创建索引。单列索引只根据该字段进行索引。单列索引可以是普通索引，也可以是唯一性索引，还可以是全文索引。只要保证该索引只对应一个字段即可。一个表可以**有多个**单列索引。
   
   **5. 多列索引（联合索引）**
   
   多列索引是在表的**多个字段组合**上创建一个索引。该索引指向创建时对应的多个字段，可以通过这几个字段进行查询，但是只有查询条件中使用了这些字段中的第一个字段时才会被使用。例如：在表的字段id、name和gender上建立一个多列索引**idx_id_name_gender**，只有在查询条件中使用了字段id时该索引才会被使用。使用组合索引时遵循**最左前缀集合**。
   
   **6. 全文索引**
   
   全文索引是目前**搜索引擎**使用的一种关键技术。它能够利用**分词技术**等多种算法智能分析出文本文字中关键词的频率和重要性，然后按照一定的算法规则智能地筛选出我们想要的搜索结果。全文索引非常适合大型数据集，对于小的数据集，它的用处比较小。
   
   使用参数**FULLTEXT**可以设置索引为全文索引。在定义索引的列上支持值的全文查找，允许在这些索引列中插入重复值和空值。全文索引只能创建在**CHAR**、**VARHCHAR**、或**TEXT**类型及其系列类型的字段上，**查询数据量较大的字符串类型的字段时，使用全文索引可以提高查询速度。**例如，表**student**的字段**information**是**TEXT**类型，该字段包含了很多文字信息。在字段上建立全文索引后，可以提高查询字段的速度。
   
   全文索引典型的有两种类型：自然语言的全文索引和布尔全文索引。
   
   * 自然语言搜索引擎将计算每一个文档对象和查询的相关度。这里，相关度是基于匹配的关键词的个数，以及关键词在文档中出现的次数。**在整个索引中出现次数越少的词语，匹配时的相关度就越高**。相反，非常常见的单词将不会被搜索，如果一个词语的在超过50%的记录中都出现了，那么自然语言的搜索将不会搜索这类词语。
   
   MySQL数据库从3.23.23版开始支持全文索引，但MySQL5.6.4以前只有**MyISAM支持**，5.6.4版本以后**innoDB才支持**，但是官方版本不支持**中文分词**，需要第三方分词插件。在5.7.6版本，MySQL内置了**ngram全文解析器**，用来支持亚洲语种的分词。测试或使用全文索引时，要先看一下自己的MySQL版本、存储引擎和数据类型是否支持全文索引。
   
   随着大数据时代的到来，关系型数据库应对全文索引的需求已力不从心，逐渐被**solr**、**ElasticSearch**等专门的搜索引擎所替代。
   
   **7. 空间索引**
   
   使用**参数SPATIAL**可以设置**空间索引**。空间索引只能建立在空间数据类型上，这样可以提高系统获取空间数据的效率。MySQL中的空间数据类型包括**GEOMETRY**、**POINT**、**LINESTRING**和**POLYGON**等。目前只有MyISAM存储引擎支持空间索引，而且索引的字段不能为空值。对于初学者来说，这类索引很少会用到。
   
   **小结：**
   
   **不同的存储引擎支持的索引类型也不同**
   
   **InnoDB**：支持B-tree、Full-text等索引，不支持Hash索引；
   
   **MyISAM**：支持B-tree、Full-text等索引，不支持Hash索引；
   
   **Memory**：支持B-tree、Hash等索引，不支持Full-text索引；
   
   **NDB**：支持Hash索引、不支持B-tree、Full-text索引;
   
   **Archive**：不支持B-tree、Hash、Full-text等索引;
   
   #### 1.2 创建索引
   
   MySQL支持多种方式在单个或者多个列上创建索引；在创建表的定义语句**CREATE TABLE**中指定索引列，使用**ALTER TABLE**语句在存在的表上创建索引，或者使用**CREATE INDEX**语句在已存在的表上添加索引。
   
   ##### 1 创建表的时候创建索引
   
   使用CREATE TABLE创建表时，除了可以定义列的数据类型外，还可以定义主键约束、外键约束或者唯一性约束，而不论创建哪种约束，在定义约束的同时相当于在指定列上创建了一个索引。
   
   举例：
   
   ```mysql
   
   CREATE TABLE dept(
       dept_id INT PRIMARY KEY AUTO_INCREMENT,
       dept_name VARCHAR(20)
   );
   
   CREATE TABLE emp(
    emp_id INT PRIMARY KEY AUTO_INCREMENT,
    emp_name VARCHAR(20) UNIQUE,
    dept_id INT,
    CONSTRAINT emp_dept_id_fk FOREIGN KEY (dept_id) REFERENCES  dept(dept_id)
    );
   
   ```
   
   显示创建表时创建索引，基本语法如下：
   
   ```mysql
   
   CREATE TABLE table_name (
     ... 数据字段描述
     
     [UNIQUE | FULLTEXT | SPATIAL] [INDEX | KEY] [index_name] (col_name [length]) [ASC | DESC]
   )
   
   ```
   
   * **UNIQUE、FULLTEXT和SPATIAL**为可选参数，分别表示唯一索引、全文索引和空间索引。

   
   
   
   
   ### 3. 索引的设计原则
   
   
   
   
   
   
   
   ## 第13章 事务基础知识
   
   ### 1 数据库事务概述
   
   事务是数据库区别于文件系统的重要特性之一，当我们
   
   
   #### 1.1 存储引擎支持情况
   
   `SHOW ENGINES`命令来查看当前MySQL支持的存储引擎都有哪些，以及这些存储引擎是否支持事务。
   
   ![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16493782368723.jpg)
   
   
   能看出在MySQL中，只有InnoDB是支持事务的。
   
   #### 1.2 基本概念
   
   
   
   
   #### 1.4 事务的状态
   
   
   
   
   ## 第14章MySQL事务日志
   
   