# MySQL常用优化技巧

## SQL优化技巧

### WHERE中IN条件转换为EXISTS条件

```mysql

-- 用户表

CREATE TABLE USER (
    id INT,
    TIME DATETIME,
    actuin VARCHAR(25)
);
-- 插入数据
INSERT INTO USER VALUE(101,NOW(),'下单');
INSERT INTO USER VALUE(102,NOW(),'下单');
INSERT INTO USER VALUE(103,NOW(),'下单');
INSERT INTO USER VALUE(105,NOW(),'下单');
INSERT INTO USER VALUE(106,NOW(),'下单');
INSERT INTO USER VALUE(107,NOW(),'下单');
INSERT INTO USER VALUE(108,NOW(),'下单');

-- 日活跃表

CREATE TABLE active (
    id INT,
    TIME DATETIME,
    actuin VARCHAR(25)
);
INSERT INTO active VALUE(101,NOW(),'下单');
INSERT INTO active VALUE(102,NOW(),'下单');
INSERT INTO active VALUE(103,NOW(),'下单');
INSERT INTO active VALUE(105,NOW(),'下单');
INSERT INTO active VALUE(106,NOW(),'下单');
INSERT INTO active VALUE(107,NOW(),'下单');
INSERT INTO active VALUE(108,NOW(),'下单');
INSERT INTO active VALUE(998,NOW(),'下单');
INSERT INTO active VALUE(999,NOW(),'下单');

-- 用户表是用户的信息，日活跃表包含老用户和新用户。
-- 问题: 查询日活跃表中的新用户信息。

-- 传统的not in查询

SELECT active.id,active.TIME FROM active WHERE id NOT IN (SELECT id FROM USER);

-- 优化的sql，使用exists

SELECT active.id,active.TIME FROM active WHERE NOT EXISTS (SELECT id FROM USER WHERE user.id = active.id);


-- EXISTS在SQL中的作用是：检验查询是否返回数据。当 where 后面的条件成立，则列出数据，否则为空。

-- 使用exists条件往往可以将in中的元素替换为可以走索引的字段

```


## 打开会话级别的查询优化器信息

```bash

# 查看优化器状态
SHOW VARIABLES LIKE 'optimizer_trace';

# 开启会话级别优化器追踪
SET SESSION OPTIMIZER_TRACE = 'enabled=on', END_MARKERS_IN_JSON = ON;

# 设置优化器追踪的内存大小
SET OPTIMIZER_TRACE_MAX_MEM_SIZE = 1000000;

# 执行自己的SQL

# 查看追踪器信息
SELECT trace FROM information_schema.OPTIMIZER_TRACE;

# 导出追踪器信息


```



## 配置优化技巧

### 设置临时表大小

```properties

[mysqld]
tmp_table_size=100M

```