# 使用minikube安装k8s集群

minikube 在 macOS、Linux 和 Windows 上快速设置本地 Kubernetes 集群。专注于帮助应用程序开发人员和新的 Kubernetes 用户。

官方文档地址：[https://minikube.sigs.k8s.io/docs/](https://minikube.sigs.k8s.io/docs/)

## 在macos上的安装

### 使用Homebrew安装

```bash

brew install minikube

```

如果安装失败，执行以下命令并重新安装。

```bash

brew unlink minikube

```

### 使用二进制安装

```bash

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
sudo install minikube-darwin-amd64 /usr/local/bin/minikube

```


## 启动集群

```bash

minikube start --image-mirror-country=cn --kubernetes-version=v1.22.12 --memory 8192


```

## 与集群交互

如果你已经安装了 kubectl，你现在可以使用它来访问你闪亮的新集群：

```bash

kubectl get po -A


```

或者，minikube 可以下载合适版本的 kubectl，你应该可以像这样使用它：

```bash

minikube kubectl -- get po -A

```

您还可以通过将以下内容添加到您的 shell 配置中来让您的生活更轻松：


```bash

alias kubectl="minikube kubectl --"


```

最初，某些服务（例如存储供应商）可能尚未处于运行状态。这是集群启动期间的正常情况，会立即自行解决。为了进一步了解您的集群状态，minikube 捆绑了 Kubernetes 仪表板，让您轻松适应新环境：

```bash

minikube dashboard

```

## 管理集群

在不影响部署的应用程序的情况下暂停 Kubernetes：

```bash

minikube pause


```

取消暂停暂停的实例：

```bash

minikube unpause


```

停止集群：

```bash

minikube stop

```

更改默认内存限制（需要重新启动）：

```bash

minikube config set memory 9001

```

浏览易于安装的 Kubernetes 服务的目录：

```bash

minikube addons list

```

创建第二个运行较旧 Kubernetes 版本的集群：

```bash

minikube start -p aged --kubernetes-version=v1.16.1

```

删除所有 minikube 集群：

```bash

minikube delete --all

```