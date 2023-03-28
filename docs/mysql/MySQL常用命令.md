# MySQL常用命令

## 设置不校验外键

```mysql
# 关闭外键校验
SET FOREIGN_KEY_CHECKS=0;
# 打开外键校验
SET FOREIGN_KEY_CHECKS=1;
```

## 设置主键自增值

```mysql
ALTER TABLE table_name AUTO_INCREMENT = value;
```

## 初始化某个表

```mysql
TRUNCATE table_name;
```

## 查看binary log

```mysql
show binary logs;
```

## 删除所有binary log

```mysql
RESET MASTER;
```

## 快速导出大量数据

```shell
mysql -uroot -p'Xmirror!@#123' xmirror_oss -e "SELECT * INTO OUTFILE '/oss/software/mysql/a.sql' FIELDS TERMINATED BY ',' FROM a"
```

## 快速导入大量数据

```shell
mysql -uroot -p'Xmirror!@#123' xmirror_oss -e "LOAD DATA INFILE 'a.sql' INTO TABLE a FIELDS TERMINATED BY ','"
```

## mysqldump导出数据

```bash

# 1、导出指定表的数据

mysqldump -t <database> -u <username> -p <password> --tables <table_name1> ... <table_name2> > xxx.sql

# 2、导出指定表的结构

mysqldump -d <database> -u <username> -p <password> --tables <table_name1> ... <table_name2> > xxx.sql

# 3、导出表的数据及结构

mysqldump <database> -u <username> -p <password> --tables <table_name1> ... <table_name2> > xxx.sql

```


## 删除某个表的所有索引

```sql

DROP PROCEDURE IF EXISTS `drop_all_indices`;
DELIMITER $$
CREATE DEFINER=`root`@`%` PROCEDURE `drop_all_indices`(IN t_name VARCHAR(255))
BEGIN
	#声明结束标识
	DECLARE end_flag BOOLEAN DEFAULT 0;
	DECLARE idx_name VARCHAR(255);
	#声明游标 album_curosr
	DECLARE idx_cursor CURSOR FOR SELECT INDEX_NAME FROM INFORMATION_SCHEMA.STATISTICS i WHERE TABLE_SCHEMA = 'xmirror_oss' AND TABLE_NAME = t_name AND INDEX_NAME <> 'PRIMARY' GROUP BY INDEX_NAME;
	#设置终止标志
	DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET end_flag=1;
	#打开游标
	OPEN idx_cursor;
	#遍历游标
	REPEAT
		FETCH idx_cursor INTO idx_name;
		IF NOT end_flag THEN
			SET @STMT :=CONCAT("ALTER TABLE ",t_name," DROP index " ,idx_name,";");
			PREPARE STMT FROM @STMT;
			EXECUTE STMT;
		END IF;
	# 根据 end_flag 判断是否结束
	UNTIL end_flag END REPEAT;
	#关闭游标
	CLOSE idx_cursor;
END$$
DELIMITER ;

```

