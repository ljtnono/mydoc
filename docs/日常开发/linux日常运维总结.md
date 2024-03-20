# linux日常运维总结

## Centos7网卡配置

```bash
cd /etc/sysconfig/network-scripts
# 在此目录下找到一个具有如下内容的文件 一般为 ifcfg-ens33 其中 ens33为网卡名
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
#BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
#IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=c656103e-e2e0-4d49-914f-00b7cbab9568
DEVICE=ens33
ONBOOT=yes
# 网络配置
BOOTPROTO=static # 静态ip
IPADDR=192.168.172.107 # ip地址
NETMASK=255.255.255.0 # 子网掩码
GATEWAY=192.168.172.1 # 网关
DNS1=8.8.8.8 # DNS
DNS2=192.168.172.1 # DNS
ZONE=public

# 重启网络服务
systemctl restart network
```

## Debian10网卡配置

在 Debian 下最简单的网络配置方式是使用 `networking` 服务，默认配置文件位于 `/etc/network/interfaces`

#### 配置 DHCP 动态 IP 地址

编辑 `/etc/network/interfaces` 网卡配置文件

```
auto enp0s3
iface enp0s3 inet dhcp
```

#### 配置 STATIC 静态 IP 地址

编辑 `/etc/network/interfaces` 网卡配置文件

```
auto enp0s3
iface enp0s3 inet static
address 192.168.1.100
netmask 255.255.255.0
gateway 192.168.1.1
dns-nameservers 8.8.8.8,8.8.4.4
```

#### 重启网络服务，使配置生效

修改网卡配置文件之后，需要重启 `networking` 服务使配置生效

```
$ systemctl restart networking
```

## Debian配置apt源

```bash

########## debian10 ##########

# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free

########## debian11 ##########

# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free

# 更新源
apt-get update

```


## Fedora37配置dnf源

1、备份

```bash

mv /etc/yum.repos.d/fedora.repo /etc/yum.repos.d/fedora.repo.backup
mv /etc/yum.repos.d/fedora-updates.repo /etc/yum.repos.d/fedora-updates.repo.backup


```

2、下载新的fedora.repo和fedora-updates.repo到/etc/yum.repos.d/

```bash

# 使用wget
wget -O /etc/yum.repos.d/fedora.repo http://mirrors.aliyun.com/repo/fedora.repo

wget -O /etc/yum.repos.d/fedora-updates.repo http://mirrors.aliyun.com/repo/fedora-updates.repo

# 使用curl

curl -o /etc/yum.repos.d/fedora.repo http://mirrors.aliyun.com/repo/fedora.repo

curl -o /etc/yum.repos.d/fedora-updates.repo http://mirrors.aliyun.com/repo/fedora-updates.repo

```

3、生成缓存

```bash

sudo yum makecache

```

## Debian10配置ssh

由于debian10默认是没有安装ssh服务的，所以需要手动安装ssh服务

```bash
# 安装ssh服务之前需要更新apt源，原始的debian源无法安装openssh-server
apt-get update
apt-get install -y openssh-server

# 配置/etc/ssh/sshd_config
PasswordAuthentication yes
PermitRootLogin yes

# 启动sshd服务
systemctl start sshd
```

## Debian10配置普通用户使用sudo

```bash
# Debian10 默认没有安装sudo，需要先切换到root用户使用apt安装sudo
apt-get install -y sudo
# 修改/etc/sudoers
<用户名> ALL=(ALL:ALL) NOPASSWD:ALL
# 后面NOPASSWD:ALL设置了就可以在使用超级管理员权限的时候不需要输入密码，没有设置需要输入密码
```

## firewall 相关操作

### 永久开启某个端口

```shell
firewall-cmd --zone=public --add-port=27017/tcp --permanent
```

### 永久关闭某个端口

```shell
firewall-cmd --remove-port=80/tcp --permanent
```

### 查看已开放的端口号

```shell
firewall-cmd --list-ports
```

### 重新加载防火墙

```shell
firewall-cmd --reload
```

## iptables相关操作

### 开放端口

```bash
iptables -I INPUT -p tcp --dport 8888 -j ACCEPT
```

### 永久开放端口

```bash

iptables -I INPUT -p tcp --dport 8080 -j ACCEPT

service iptables save

```

## Centos7 配置yum为阿里源

1、备份原.repo文件

```bash
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

2、下载新的.repo文件

```bash
CentOS 6
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo

CentOS 7
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo    

CentOS 8
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-8.repo
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-8.repo
```

3、重构缓存

```bash
yum clean all
yum makecache
```

4、执行更新

```bash
yum update -y
```



## 临时开启docker端口

```
临时开启docker 端口

1. 获取容器ip地址
   docker inspect <container_id> | grep IPAddress

2. nat转发
   iptables -t nat -A DOCKER -p tcp --dport <host_port> -j DNAT --to-destination <container_ip>:<docker_port>
   iptables -t nat -A POSTROUTING -j MASQUERADE -p tcp --source <container_ip> --destination <container_ip> --dport <docker_port>
   iptables -A DOCKER -j ACCEPT -p tcp --destination <container_ip> --dport <docker_port>

3. 参数说明
   container_ip：要做端口映射的容器ip
   host_port：要映射到宿主机的端口
   docker_port：要映射的端口，即容器中的端口
```



## 离线rpm包以及其依赖包

1、安装yumdownloader

```shell
yum install yum-utils -y
```

2、使用yumdownloader下载离线安装包

```shell
yumdownloader --resolve --destdir <path> <package>
# 其中 path代表下载的包存储路径，package代表要下载的包名
```



## 关闭selinux

Dockerfile构建时出现类似没有共享库权限的问题时，可以尝试关闭selinux

```bash
setenforce 0
# 永久关闭 修改/etc/sysconfig/selinux文件设置
sed -i 's/SELINUX=permissive/SELINUX=disabled/' /etc/sysconfig/selinux
sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
```



## elasticsearch安装及启动异常解决方式

elasticsearch.yml配置文件

```yaml
bootstrap.memory_lock: false
bootstrap.system_call_filter: false
cluster.initial_master_nodes: ["node-1"]
# 数据存储路径
path.data: /xxx/xx/data
path.logs: /xxx/xx/logs
network.host: 0.0.0.0
http.port: 9200
http.cors.enabled: true
http.cors.allow-origin: "*"
```

* **max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]**

  ```shell
  # 临时修改
  sysctl -w vm.max_map_count=262144
  # 永久修改
  echo "vm.max_map_count=262144" >> /etc/sysctl.conf
  sysctl -p /etc/sysctl.conf
  ```

* **max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]**

  ```shell
  vim /etc/security/limits.conf
  # 添加以下内容 其中elastic是启动elasticsearch的用户名
  elastic hard nofile 65536
  elastic soft nofile 65536
  ```

配置索引最大返回值

```bash
## 配置分页最大值
PUT <indexName>/_settings
{
  "index":{
    "max_result_window":10000000
  }
}
```

## 扩展linux磁盘空间（给某个已挂载目录增加空间）

假设一下操作已经通过虚拟机将磁盘空间新增了200G的情况

1、使用fdisk查看系统磁盘分区情况

```bash
fdisk -l
######################### 以下数据可能有误，只是为了说明一些数字的含义 ######################## 
Disk /dev/sda: 429.5 GB, 429496729600 bytes, 838860800 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000a64f9

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200   419430399   208665600   8e  Linux LVM

Disk /dev/mapper/centos-root: 68.4 GB, 268435456000 bytes, 524288000 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-swap: 8455 MB, 8455716864 bytes, 16515072 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-home: 151.5 GB, 151523426304 bytes, 295944192 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

# Device：分区的设备文件名。
# Boot：是否为启动引导分区，在这里 /dev/sda1 为启动引导分区。
# Start：起始柱面，代表分区从哪里开始。
# End：终止柱面，代表分区到哪里结束。
# Blocks：分区的大小，单位是 KB。
# id：分区内文件系统的 ID。在 fdisk 命令中，可以 使用 "i" 查看。
# System：分区内安装的系统是什么。

# 以上可以看到硬盘/dev/sda总共429.5GB，分为两个分区 /dev/sda1  /dev/sda2
# 其中/dev/sda1 为主分区，安装的是linux操作系统，/dev/sda2安装的是逻辑卷管理系统
# 逻辑挂载了三个目录，分别为/dev/mapper/centos-root, /dev/mapper/centos-swap, /dev/mapper/centos-home, 相关信息显示在下面
```

2、创建一个新分区

```bash
fdisk /dev/sda
# 会显示交互问答界面（输入m显示命令选项）
Command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   g   create a new empty GPT partition table ## 创建一个新的空GPT分区表
   G   create an IRIX (SGI) partition table
   l   list known partition types ## 列出所有支持的分区系统类型 LVM为8e
   m   print this menu ## 打印此菜单
   n   add a new partition ## 新增一个分区
   o   create a new empty DOS partition table ## 创建一个新的空DOS分区表
   p   print the partition table ## 打印分区表信息
   q   quit without saving changes ## 退出不保存
   s   create a new empty Sun disklabel 
   t   change a partition's system id ## 更改分区的系统id
   u   change display/entry units
   v   verify the partition table ## 验证分区表
   w   write table to disk and exit ## 写入分区表信息
   x   extra functionality (experts only) 
# 这里再次输入n,来创建一个新的分区
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p # 这里选择p为主分区
Partition number (1-4, default 1): # 分区号，从/dev/sda[number] 往后累加，不输入自动处理
First sector (2048-10485759, default 2048): # 不输入自动处理
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-10485759, default 10485759): +3G

Created a new partition 1 of type 'Linux' and of size 3 GiB.
# 到此处已经成功创建了一个分区
```

3、刷新分区表

```bash
# 创建分区之后，需要通知系统刷新分区表
partprobe
# 再次查看分区表
fdisk -l
# 此时会发现多了一个/dev/sda3分区
```

4、给分区创建文件系统

```bash
# 虚拟机默认使用的xfs文件系统
mkfs.xfs /dev/sda3
# 如果是ext4
# mkfs.ext4 /dev/sda3
```

5、初始化物理分区为物理卷

```bash
pvcreate /dev/sda3
```

6、将新的物理卷加入到卷组

```bash
# 使用vgs可以查看卷组
vgs
##### 
VG     #PV #LV #SN Attr   VSize   VFree
  centos   2   3   0 wz--n- 198.99g    0
## 发现只有一个centos卷组，并且只有200G，现在要将/dev/sda3分区的空间加入到centos卷组
### 
vgextend centos /dev/sda3 # 将/dev/sda3物理卷加入到centos卷组
```

7、扩展需要扩展的目录的空间

```bash
# 使用df -h 命令可以查看目录在卷组下的名字
df -h
###########
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root  250G   31G  219G  13% /
devtmpfs                 3.8G     0  3.8G   0% /dev
tmpfs                    3.9G     0  3.9G   0% /dev/shm
tmpfs                    3.9G  4.0M  3.9G   1% /run
tmpfs                    3.9G     0  3.9G   0% /sys/fs/cgroup
/dev/sda1               1014M  146M  869M  15% /boot
/dev/mapper/centos-home  142G   79G   63G  56% /home
tmpfs                    781M     0  781M   0% /run/user/0
overlay                  142G   79G   63G  56% /home/docker/overlay2/748232bfef2b57e1a3b3532c9a9c83e1a83d3646acbba1d0d7087ec9906a2279/merged
shm                       64M     0   64M   0% /home/docker/containers/cfd58739e47639da703ad88a0d0d33396646a00a23092d46ccd0117882a56b66/shm
##########
lvextend -l +100%free /dev/mapper/centos-root # 给/dev/mapper/centos-root也就是/目录扩展卷组剩余的全部空间
```

8、通知系统同步文件系统

```bash
xfs_growfs /dev/mapper/centos-root
```

## Centos7安装python3环境

1、在centos7上默认安装了python2.7.5环境,使用python --version命令可以查看相关信息

```bash
python --version
```

2、查看linux默认安装python的位置

```bash
whereis python
```

3、安装python3需要的依赖包

```bash
yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make libffi-devel
```

4、下载源码包

```bash
wget https://www.python.org/ftp/python/3.9.0/Python-3.9.0.tgz
```

5、解压安装

```bash
# 解压压缩包
tar -zxvf Python-3.9.0.tgz

# 进入文件夹
cd Python-3.9.0

# 配置安装位置
./configure --prefix=<pythondir>

# 安装
make && make install
```

如果最后没提示出错，就代表正确安装了

6、建立软连接

```bash
#添加python3的软链接 
ln -s <pythondir>/bin/python3.9 /usr/bin/python3 

#添加 pip3 的软链接 
ln -s <pythondir>/bin/pip3.9 /usr/bin/pip3
```


## 更新Centos7内核

```bash

导入elrepo gpg key
# rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org

```

```bash

安装elrepo YUM源仓库
# yum -y install https://www.elrepo.org/elrepo-release-7.0-4.el7.elrepo.noarch.rpm

```


```bash

安装kernel-ml版本，ml为长期稳定版本，lt为长期维护版本
# yum --enablerepo="elrepo-kernel" -y install kernel-ml.x86_64

```

```bash

设置grub2默认引导为0
# grub2-set-default 0

```


```bash

重新生成grub2引导文件
# grub2-mkconfig -o /boot/grub2/grub.cfg

```

```bash

更新后，需要重启，使用升级的内核生效。
# reboot

```

```bash

重启后，需要验证内核是否为更新对应的版本
# uname -r

```




## 在容器外执行sql命令

```bash
# docker exec <container_name> mysql -u root -ppassword <mydb> < /path/to/script.sql
# 通常在容器外执行sql文件，需要使用输入重定向这个时候会导致没有报错，但是sql也并没有执行的问题，这是因为docker exec 在执行命令时没有持续打开与容器的输入输出连接。需要加上 -i 参数让docker exec 命令在执行的时候保持与容器的标准输入输出的连接
docker exec -i <container_name> mysql -u root -ppassword <mydb> < /path/to/script.sql
```



## Debian10普通用户访问docker去除sudo

原因（docker手册）

> Manage Docker as a non-root user
>
> The docker daemon binds to a Unix socket instead of a TCP port. By
> default that Unix socket is owned by the user root and other users can
> only access it using sudo. The docker daemon always runs as the root
> user.
>
> If you don’t want to use sudo when you use the docker command, create
> a Unix group called docker and add users to it. When the docker daemon
> starts, it makes the ownership of the Unix socket read/writable by the
> docker group.

docker后台绑定了一个Unix套接字到一个TCP端口。默认这个套接字是root用户所有，其他用户能通过sudo访问，如果不想使用sudo，需要创建一个Unix用户组（名为docker），然后将用户加入到这个用户组。当docker后台启动时，会将套接字的所有者改为这个用户组。

操作命令：

```bash
sudo groupadd docker # 添加docker用户组
sudo gpasswd -a $USER docker # 将登录用户加入到docker用户组中
newgrp docker # 更新用户组
```

## 修改docker数据目录

```bash
# 修改/etc/docker/daemon.json
{
    "data-root": "<your path>"
}
```


## 时区问题



## k8s镜像自动拉取脚本

```bash

#!/bin/bash

set -o errexit
set -o nounset
set -o pipefail

## 这里自定义版本 使用kubeadm config images list 查看镜像版本

KUBE_VERSION="v1.22.4"
KUBE_PAUSE_VERSION="3.5"
ETCD_VERSION="3.5.0-0"
DNS_VERSION="v1.8.4"

# 这是原始仓库名，最后需要改名成这个
GCR_URL=k8s.gcr.io
# 使用的镜像加速url
DOCKERHUB_URL=registry.cn-hangzhou.aliyuncs.com/google_containers

images=(
kube-proxy:${KUBE_VERSION}
kube-scheduler:${KUBE_VERSION}
kube-controller-manager:${KUBE_VERSION}
kube-apiserver:${KUBE_VERSION}
pause:${KUBE_PAUSE_VERSION}
etcd:${ETCD_VERSION}
coredns:${DNS_VERSION}
)

for imageName in ${images[@]} ; do
        docker pull $DOCKERHUB_URL/$imageName
        if [ "coredns:${DNS_VERSION}" == $imageName ]; then
                docker tag $DOCKERHUB_URL/$imageName $GCR_URL/coredns/coredns:${DNS_VERSION}
                docker rmi $DOCKERHUB_URL/$imageName
        else
                docker tag $DOCKERHUB_URL/$imageName $GCR_URL/$imageName
                docker rmi $DOCKERHUB_URL/$imageName
        fi
done

```

## jenkins查看已添加凭证的密码

1、需要先打开jenkins的凭证界面，查看凭证的唯一标识

<div align = "center">

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16428339572460.jpg)


</div>

2、到jenkins主节点上查看加密的其加密的秘闻

在 Jenkins 中，我们设置的凭证都存储在 .jenkins/credentials.xml 文件中，但是进行了加密。具体.jenkins文件夹放在那，如果是tomcat形式运行，那么一般是～/.jenkins。

<div align = "center">

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16428342271605.jpg)

</div>


3、使用jenkins的控制台执行功能执行命令

<div align = "center">

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16428343451262.jpg)

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16428343904527.jpg)


</div>

其中 {AQAAABl5pYu3vTzodW48IBDAreNBl6JJtKfpqpgw==} 就是在.jenkins/credentials.xml 中找到的加密口令.注意，一定要带上 {}，否则只会反会 null 值。


## 联网同步时间

```bash

yum install -y ntp

ntpdate time1.aliyun.com

```

## 脱机同步硬件时间

```bash
#输入tzselect，进入时区选择，然后依次选择Asia>China>Beijing Time，选择时输入相应的数字即可。最后对信息确认Yes。
sudo tzselect
#复制文件到本地时间内：输入sudo cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
sudo cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

#防止出错先执行命令
timedatectl set-ntp no
timedatectl set-time '2088-12-18 18:08:08'
sudo hwclock -w
#将系统时钟与硬件时钟同步：
sudo hwclock --hctosys
```

## 安装docker-compose

```bash

# 其中v2.6.0是最新版本，如果需要下载其他版本，请到github相关页面查看最新版本号

curl -L https://github.com/docker/compose/releases/download/v2.6.0/docker-compose-`uname -s `-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

docker-compose -v

```


## 生成密钥

```bash

ssh-keygen -t rsa -C "youremail@xxx.com"

```

## crontab常用命令

安装crontab

```bash

yum install crontabs

```

查看定时任务列表

```bash

crontab -l

```

编辑定时任务

```bash

cron -e

# 直接编辑/var/spool/cron/root 也可以

```

crontab服务操作说明

```bash

# 启动服务 
service crond start
# 关闭服务 
service crond stop
# 重启服务 
service crond restart
# 重新载入配置
service crond reload
# 查看crontab服务状态
service crond status

```

## influxdb导出和导入文件

导出命令的语法格式：

```txt

# influx_inspect export --help
Exports TSM files into InfluxDB line protocol format.

Usage: influx_inspect export [flags]
  -compress
        Compress the output
  -database string
        Optional: the database to export
  -datadir string
        Data storage path (default "/root/.influxdb/data")
  -end string
        Optional: the end time to export (RFC3339 format)
  -out string
        Destination file to export to (default "/root/.influxdb/export")
  -retention string
        Optional: the retention policy to export (requires -database)
  -start string
        Optional: the start time to export (RFC3339 format)
  -waldir string
        WAL storage path (default "/root/.influxdb/wal")

```

数据导出demo：

```txt

influx_inspect export -datadir "/var/lib/influxdb/data" -waldir "/var/lib/influxdb/wal" -out "influxdb_dump_out" -database "opsultra" -start "2021-09-10T00:00:00Z"
其中：
  datadir: influxdb的数据存放位置
  waldir: influxdb的wal目录
  out: 输出文件
  database: 导出的db名称
  start: 从什么时间导出

```

导入的命令语法：

```txt
# influx -import --help
Usage of influx:
  -version
       Display the version and exit.
  -host 'host name'
       Host to connect to.
  -port 'port #'
       Port to connect to.
  -socket 'unix domain socket'
       Unix socket to connect to.
  -database 'database name'
       Database to connect to the server.
  -password 'password'
      Password to connect to the server.  Leaving blank will prompt for password (--password '').
  -username 'username'
       Username to connect to the server.
  -ssl
        Use https for requests.
  -unsafeSsl
        Set this when connecting to the cluster using https and not use SSL verification.
  -execute 'command'
       Execute command and quit.
  -format 'json|csv|column'
       Format specifies the format of the server responses:  json, csv, or column.
  -precision 'rfc3339|h|m|s|ms|u|ns'
       Precision specifies the format of the timestamp:  rfc3339, h, m, s, ms, u or ns.
  -consistency 'any|one|quorum|all'
       Set write consistency level: any, one, quorum, or all
  -pretty
       Turns on pretty print for the json format.
  -import
       Import a previous database export from file
  -pps
       How many points per second the import will allow.  By default it is zero and will not throttle importing.
  -path
       Path to file to import
  -compressed
       Set to true if the import file is compressed


```

数据导入demo：

```txt

# influx -import -path=/root/influxdb_dump_out -precision=ns

其中：
  import: 标识导入
  path: 导入文件
  precision: 导入的数据时间精度

```

## influxdb常用命令

```bash
# 登录
influx -username xxx -password xxx
# 删除数据库
DROP DATABASE <database>
```

## 普通用户加入到docker用户组

在debian12等系统上，普通用户需要使用sudo才能访问docker用户组，为了方便可以将普通用户加入到docker用户组，这样就可以不用每次输入sudo了。

1. 创建docker用户组

```bash
 sudo groupadd docker
```

2. 应用用户加入docker用户组

```bash
 sudo usermod -aG docker ${USER}
```

3. 重启docker服务

```bash
 sudo systemctl restart docker
```

4. 切换或者退出当前账户再从新登入

```bash
su root             # 切换到root用户
su ${USER}          # 再切换到原来的应用用户以上配置才生效
```