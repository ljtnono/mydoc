# DBeaver22.1.0破解安装

DBeaver Enterprise（简称DBeaverEE）支持MongoDB、Redis、Apache Hive等，但是需要付费使用。


下载地址：
百度云下载: [https://pan.baidu.com/s/1k1BNHoc5N0M2Z6d0AjYkLQ](https://pan.baidu.com/s/1k1BNHoc5N0M2Z6d0AjYkLQ) 提取码：o36d。


6.2及以上有几点需要注意的：

DBeaver运行需要java，请自行安装！不要使用DBeaver自带的jre，它被人为阉割了。

22.1版本请在dbeaver.ini文件末尾添加一行：

```ini

-Dlm.debug.mode=true

```

21.0版本开始自行安装jdk11，替换dbeaver.ini内由-vm指定的java路径，把地址换成自己安装的！
如果你的dbeaver.ini内没有-vm参数，请在首行添加你安装jdk的java路径：

```ini
-vm 
/path/to/your/bin/java

```

