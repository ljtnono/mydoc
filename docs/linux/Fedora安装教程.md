# Fedora安装教程

Fedora系统介绍

> Fedora 为硬件、云和容器创建了一个创新、免费和开源的平台，使软件开发人员和社区成员能够为他们的用户构建量身定制的解决方案。

## 下载Fedora Spins版本

Fedora默认使用Gnome桌面，为了安装KDE桌面，可选择使用Spins版本的Fedora。Fedora提供了默认安装各种桌面环境的iso镜像文件，包括KDE、GNOME、XFACE、LXQT、MATE等。

这里使用KDE版本的Fedora，下载地址：[https://download.fedoraproject.org/pub/fedora/linux/releases/37/Spins/x86_64/iso/Fedora-KDE-Live-x86_64-37-1.7.is](https://download.fedoraproject.org/pub/fedora/linux/releases/37/Spins/x86_64/iso/Fedora-KDE-Live-x86_64-37-1.7.is)


## 安装流程

1、选择英文进行安装

如果不选择英文安装的话，那么自动生成的文件夹名会变成中文名，由于可能存在各种软件不支持中文文件夹的问题，建议使用英文安装，安装完毕之后再安装中文语言。

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16697037994360.jpg)


2、自定义分区设置


![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16697039254646.jpg)


点击Installation Destination选项，点击如下三个选项进行自定义设置。

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16697047333665.jpg)

选择标准分区

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16697048422250.jpg)

创建挂载点/boot，大小为1G

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16697049233096.jpg)

创建swap分区，大小为16GiB

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16697049674245.jpg)

创建 / 挂载点，大小为25GiB

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16697050007834.jpg)


创建一个大小为1MiB的biosboot分区

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16697052186443.jpg)


剩余空间都分配给/home挂载点

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16697052498364.jpg)


![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16697052684714.jpg)


3、配置用户信息

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16697053153787.jpg)


![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16697053498193.jpg)

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16697053727115.jpg)


4、等待安装完成

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16697054055164.jpg)



## 安装中文和处理中文乱码

按照以上安装流程安装完毕之后，系统默认会使用英文作为默认语言，调整为中文的步骤如下：

打开系统设置--语言和区域设置，修改第一项为简体中文，然后重启即可

![](http://f.lingjiatong.cn:30090/rootelement/articleQuote/16720221094983.jpg)


安装完中文之后，可能会出现终端出现中文乱码的问题，解决方案如下：

```bash

cat >> /etc/profile << EOF

export LC_ALL=zh_CN.UTF-8

EOF

```

