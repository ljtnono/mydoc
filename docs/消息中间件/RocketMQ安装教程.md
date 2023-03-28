# RocketMQ安装教程

本教程基于k8s部署RocketMQ单机版本和集群版本。

> 参考文档：
> 
> [https://blog.csdn.net/scjava/article/details/122618946](https://blog.csdn.net/scjava/article/details/122618946)
> [https://blog.csdn.net/u012691139/article/details/123738294](https://blog.csdn.net/u012691139/article/details/123738294)

## 准备工作

### 制作docker镜像

操作系统：docker镜像centos7
rocketmq版本号：4.8.0

1、下载必要的二进制安装包并解压

```bash

# 下载rocketmq安装包
wget https://archive.apache.org/dist/rocketmq/4.8.0/rocketmq-all-4.8.0-bin-release.zip

# 下载jdk安装包
wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u141-b15/336fa29ff2bb4ef291e347e091f7f4a7/jdk-8u141-linux-x64.tar.gz"

```

2、解压压缩包

```bash

unzip rocketmq-all-4.8.0-bin-release.zip
tar zxvf jdk-8u141-linux-x64.tar.gz
rm -rf rocketmq-all-4.8.0-bin-release.zip
rm -rf jdk-8u141-linux-x64.tar.gz
mv rocketmq-all-4.8.0-bin-release rocketmq4.8.0

```


2、编写Dockerfile文件

```Dockerfile

FROM centos:7
COPY jdk1.8.0_141 /usr/jdk1.8.0_141
COPY rocketmq4.8.0 /usr/rocketmq4.8.0

RUN rm -f /etc/localtime \
&& ln -sv /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
&& echo "Asia/Shanghai" > /etc/timezone \
&& mkdir -p /data/rocketmq/store
 
#jdk enviroment
ENV LANG en_US.UTF-8
ENV JAVA_HOME=/usr/jdk1.8.0_141
ENV ROCKETMQ_HOME=/usr/rocketmq4.8.0
ENV PATH=$ROCKETMQ_HOME/bin:$JAVA_HOME/bin:$PATH
 
CMD ["/bin/bash"]

```

构建镜像并推送到dockerhub：

```bash

docker build -t ljtnono/rocketmq:4.8.0 .
docker push ljtnono/rocketmq:4.8.0

```

## 单机版部署


```yml

# deployment 这里一个pod里面包含了namesrv、broker和console三个镜像
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rocketmq
  namespace: lingjiatong
spec:
  replicas: 1
  selector:
    matchLabels:
      app: rocketmq
  template:
    metadata:
     labels:
       app: rocketmq
    spec:
      containers:
        # namesrv
        - name: namesrv
          image: ljtnono/rocketmq:4.8.0
          command: ["sh","/usr/rocketmq4.8.0/bin/mqnamesrv"]
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 9876
        # broker
        - name: broker
          image: ljtnono/rocketmq:4.8.0
          command: ["sh","/usr/rocketmq4.8.0/bin/mqbroker", "-n","localhost:9876"]
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 10909
            - containerPort: 10911
        # dashboard
        - name: dashboard
          image: apacherocketmq/rocketmq-dashboard:latest
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 8080
          env:
            - name: JAVA_OPTS
              value: -Drocketmq.namesrv.addr=localhost:9876 -Dcom.rocketmq.sendMessageWithVIPChannel=false

---
# svc
apiVersion: v1
kind: Service
metadata:
  name: rocketmq-console-service
  namespace: lingjiatong
spec:
  type: NodePort
  ports:
  - nodePort: 31765
    port: 8080
    protocol: TCP
    targetPort: 8080
  selector:
    app: rocketmq
    
```


## 集群版部署

