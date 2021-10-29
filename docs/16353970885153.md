# linux常用命令

## 网络管理相关

### curl命令

curl命令是一个利用URL规则在命令行下工作的文件传输工具。它支持文件的上传和下载，所以是中和传输工具，但按传统，习惯称curl为下载工具。作为一款枪里工具，curl支持包括HTTP、HTTPS、ftp等众多协议，还支持POST、cookies、认证、从指定偏移处下载部分文件、用户代理字符串、限速、文件大小、进度条等特征。

语法：

> curl（选项）（参数）

| -a/--append                             | 上传文件时，附加到目标文件                                   |
| --------------------------------------- | ------------------------------------------------------------ |
| -A/--user-agent <string>                | 设置用户代理发送给服务器                                     |
| -anyauth                                | 可以使用“任何”身份验证方法                                   |
| -b/--cookie <name=string/[file]>        | cookie字符串或文件读取位置                                   |
| --basic                                 | 使用HTTP基本验证                                             |
| -B/--use-ascii                          | 使用ASCII /文本传输                                          |
| -c/--cookie-jar <file>                  | 操作结束后把cookie写入到这个文件中                           |
| -C/--continue-[at]<offset>              | 断点续转                                                     |
| -d/--data <data>                        | HTTP POST方式传送数据                                        |
| --data-ascii <data>                     | 以ascii的方式post数据                                        |
| --data-binary <data>                    | 以二进制的方式post数据                                       |
| --negotiate                             | 使用HTTP身份验证                                             |
| --digest                                | 使用数字身份验证                                             |
| --disable-eprt                          | 禁止使用EPRT或LPRT                                           |
| --disable-epsv                          | 禁止使用EPSV                                                 |
| -D/--[dump]-header <file>               | 把header信息写入到该文件中                                   |
| --egd-file <file>                       | 为随机数据(SSL)设置EGD socket路径                            |
| --tcp-nodelay                           | 使用TCP_NODELAY选项                                          |
| -e/--referer                            | 来源网址                                                     |
| -E/--cert <cert[:[passwd]>              | 客户端证书文件和密码 (SSL)                                   |
| --cert-[type] <type>                    | 证书文件类型 (DER/PEM/ENG) (SSL)                             |
| --key <key>                             | 私钥文件名 (SSL)                                             |
| --key-type <type>                       | 私钥文件类型 (DER/PEM/ENG) (SSL)                             |
| --pass <pass>                           | 私钥密码 (SSL)                                               |
| --engine <eng>                          | 加密引擎使用 (SSL). "--engine list" for list                 |
| --cacert <file>                         | CA证书 (SSL)                                                 |
| --capath <directory>                    | CA目录 (made using c_rehash) to verify peer against (SSL)    |
| --ciphers <list>                        | SSL密码                                                      |
| --compressed                            | 要求返回是压缩的形势 (using deflate or [gzip](http://man.linuxde.net/gzip)) |
| --connect-timeout <seconds>             | 设置最大请求时间                                             |
| --create-[dirs]                         | 建立本地目录的目录层次结构                                   |
| --crlf                                  | 上传是把LF转变成CRLF                                         |
| -f/--fail                               | 连接失败时不显示http错误                                     |
| --ftp-create-dirs                       | 如果远程目录不存在，创建远程目录                             |
| --ftp-method [multicwd/nocwd/singlecwd] | 控制CWD的使用                                                |
| --ftp-pasv                              | 使用 PASV/EPSV 代替端口                                      |
| --ftp-skip-pasv-[ip]                    | 使用PASV的时候,忽略该IP地址                                  |
| --ftp-ssl                               | 尝试用 SSL/TLS 来进行ftp数据传输                             |
| --ftp-ssl-reqd                          | 要求用 SSL/TLS 来进行ftp数据传输                             |
| -F/--form <name=content>                | 模拟http表单提交数据                                         |
| --form-string <name=string>             | 模拟http表单提交数据                                         |
| -g/--globoff                            | 禁用网址序列和范围使用{}和[]                                 |
| -G/--get                                | 以get的方式来发送数据                                        |
| -H/--header <line>                      | 自定义头信息传递给服务器                                     |
| --ignore-content-length                 | 忽略的HTTP头信息的长度                                       |
| -i/--include                            | 输出时包括protocol头信息                                     |
| -I/--[head]                             | 只显示请求头信息                                             |
| -j/--junk-session-cookies               | 读取文件进忽略session cookie                                 |
| --interface <interface>                 | 使用指定网络接口/地址                                        |
| --krb4 <level>                          | 使用指定安全级别的krb4                                       |
| **-k/--insecure**                       | 允许不使用证书到SSL站点                                      |
| -K/--config                             | 指定的配置文件读取                                           |
| -l/--list-only                          | 列出ftp目录下的文件名称                                      |
| --limit-rate <rate>                     | 设置传输速度                                                 |
| --local-port<NUM>                       | 强制使用本地端口号                                           |
| -m/--max-[time]<seconds>                | 设置最大传输时间                                             |
| --max-redirs <num>                      | 设置最大读取的目录数                                         |
| --max-filesize <bytes>                  | 设置最大下载的文件总量                                       |
| -M/--manual                             | 显示全手动                                                   |
| -n/--netrc                              | 从netrc文件中读取用户名和密码                                |
| --netrc-optional                        | 使用 .netrc 或者 URL来覆盖-n                                 |
| --ntlm                                  | 使用 HTTP NTLM 身份验证                                      |
| -N/--no-buffer                          | 禁用缓冲输出                                                 |
| -o/--output                             | 把输出写到该文件中                                           |
| -O/--remote-name                        | 把输出写到该文件中，保留远程文件的文件名                     |
| -p/--proxytunnel                        | 使用HTTP代理                                                 |
| --proxy-anyauth                         | 选择任一代理身份验证方法                                     |
| --proxy-basic                           | 在代理上使用基本身份验证                                     |
| --proxy-digest                          | 在代理上使用数字身份验证                                     |
| --proxy-ntlm                            | 在代理上使用ntlm身份验证                                     |
| -P/--ftp-port <address>                 | 使用端口地址，而不是使用PASV                                 |
| -q                                      | 作为第一个参数，关闭 .curlrc                                 |
| -Q/--quote <cmd>                        | 文件传输前，发送命令到服务器                                 |
| -r/--range <range>                      | 检索来自HTTP/1.1或FTP服务器字节范围                          |
| --range-file                            | 读取（SSL）的随机文件                                        |
| -R/--remote-time                        | 在本地生成文件时，保留远程文件时间                           |
| --retry <num>                           | 传输出现问题时，重试的次数                                   |
| --retry-delay <seconds>                 | 传输出现问题时，设置重试间隔时间                             |
| --retry-max-time <seconds>              | 传输出现问题时，设置最大重试时间                             |
| -s/--silent                             | 静默模式。不输出任何东西                                     |
| -S/--show-error                         | 显示错误                                                     |
| --socks4 <[host][:port]>                | 用socks4代理给定主机和端口                                   |
| --socks5 <host[:port]>                  | 用socks5代理给定主机和端口                                   |
| --stderr <file>                         |                                                              |
| -t/--[telnet]-option <OPT=val>          | Telnet选项设置                                               |
| --trace <file>                          | 对指定文件进行debug                                          |
| --trace-ascii <file>                    | Like --跟踪但没有hex输出                                     |
| --trace-time                            | 跟踪/详细输出时，添加时间戳                                  |
| -T/--upload-file <file>                 | 上传文件                                                     |
| --url <URL>                             | Spet URL to work with                                        |
| -u/--user <user[:password]>             | 设置服务器的用户和密码                                       |
| -U/--proxy-user <user[:password]>       | 设置代理用户名和密码                                         |
| -[w]/--[write]-out [format]             | 什么输出完成后                                               |
| -x/--proxy <host[:port]>                | 在给定的端口上使用HTTP代理                                   |
| -X/--request <[command]>                | 指定什么命令                                                 |
| -y/--speed-time                         | 放弃限速所要的时间，默认为30                                 |
| -Y/--speed-limit                        | 停止传输速度的限制，速度时间                                 |

### iptables

iptables是一个包过滤防火墙，主要包含4张表（tables），所以叫做iptables。这四张表为：raw，mangle，nat，filter。

每张表都有一个专门写规则的地方，也就是chain（链）。常用的filter表有三个chain（INPUT，OUTPUT，FORWARD）。

FORWARD 转发规则链（当源地址以及目标地址都不是本机时使用本链，即代理功能）

Iptables的基本语法

```bash
iptables [-t 表名] 选项 [链名] [条件] [-j 控制类型]

# 例如
iptabiles -t filter -I INPUT -p icmp -j ACCEPT
```

注意事项：

* 不指定表时，默认指filter表
* 不指定链名时，默认指表内的所有链
* 除非设置链的默认策略，否则必须制定匹配条件
* 选项、链名、控制类型使用大写字母，其余均为小写

数据包常见的控制类型

- ACCPET：允许通过
- DROP：直接丢弃
- REJECT：拒绝通过，必要时会给出提示
- LOG：记录日志信息，然后传给下一条规则继续匹配



## 磁盘管理相关

### fdisk命令

Fdisk 命令用于观察硬盘实体使用情况，可以用来列出机器中所有磁盘的个数，也能列出所有磁盘分区情况，也可对硬盘分区（适用于 2T 以下磁盘，高于 2T 磁盘使用 parted)。

语法：

> fdisk（选项）（参数）

选项：

> -b<分区大小>：指定每个分区的大小；
>
> -l：列出指定的外围设备的分区表状况；
>
> -s<分区编号>：将指定的分区大小输出到标准输出上，单位为区块；
>
> -u：搭配"-l"参数列表，会用分区数目取代柱面数目，来表示每个分区的起始地址；
>
> -v：显示版本信息

参数：

> 设备文件：指定要进行分区或者显示分区的硬盘设备文件。







