# git多用户配置

## 需求场景

公司项目使用公司的邮箱地址，自己做的项目使用自己的邮箱地址。由于git会默认使用一个全局的用户名和邮箱，所以需要进行多用户设置来区分自己的项目和公司的项目。


## 查看当前全局用户配置

```bash

git config --global user.name
git config --global user.email

```

## 规划多用户配置

配置规划如下表：

<div align = "center">

||公司项目|自己项目|
|----|----|----|
|用户名|公司项目用的邮箱地址|自己项目用的邮箱地址|
|邮箱|ljtnono@163.com|935188400@qq.com|

</div>


## 生成密钥对

密钥的保存位置默认在 **~/.ssh** 目录下，可以自定义生成密钥的路径。

```bash

ssh-keygen -t rsa -C "ljtnono@163.com"

```

按下ENTER键后，会有如下提示：

```bash

Generatingpublic/privatersa key pair.Enter fileinwhich to save the key（/Users/lingjiatong/.ssh/id_rsa）：

```

这里输入私钥的完整路径。为了区分两个不同的环境，公司的私钥保存在/Users/lingjiatong/<私钥名>。

## 在公司的gitlab下添加相应的公钥

这个就不多赘述了。

## 将私钥添加到本地中。

```bash

ssh-add ~/.ssh/id_rsa
ssh-add ~/.ssh/<公司gitlab私钥名>

```

添加完毕后，可以通过执行 **ssh-add -l** 验证下，如果能显示两条信息，那么应该没啥问题。


## 管理密钥

通过以上步骤，公钥、密钥分别被添加到git服务器和本地了。但是还需要在本地创建一个密钥配置文件，通过该文件，实现根据仓库的remote链接地址自动选择合适的私钥。

编辑 **~/.ssh**目录下的 **config** 文件，如果没有，请创建

```bash

# github email address
Host github.com
  Hostname ssh.github.com
  User lingjiatong
  Port 443
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_rsa

# gitlab email address
# 公司内网地址
Host gitlab
  Hostname <公司gitlab域名>
  PreferredAuthentications publickey
  User lingjiatong
  IdentityFile ~/.ssh/<公司gitlab对应的私钥名>

```


## 仓库配置

完成以上配置之后，基本上完成了所有的配置。但是存在一个问题就是，如果当你提交仓库修改，你会发现提交的用户名变成了你的系统主机名。

这是因为git的配置分为三个级别，**System**-->**Global**-->**Local**。System即系统级别，Global为配置的全局，Local为仓库级别的配置，优先级是Local>Global>System。

因此我们需要为每个仓库单独配置用户名信息，假设我们要配置github的某个仓库，进入该仓库后，执行：

```bash

git config --local user.name "用户名"
git config --local user.email "邮箱"

```