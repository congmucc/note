- [Kubernetes 中文文档](https://kubernetes.io/zh-cn/docs/home/)
## 1.1 特性
kubernetes具有以下特性：  
- **服务发现和负载均衡**
    Kubernetes 可以使用 DNS 名称或自己的 IP 地址来暴露容器。 如果进入容器的流量很大， Kubernetes 可以负载均衡并分配网络流量，从而使部署稳定。
- **存储编排**
    Kubernetes 允许你自动挂载你选择的存储系统，例如本地存储、公共云提供商等。
- **自动部署和回滚**
    你可以使用 Kubernetes 描述已部署容器的所需状态， 它可以以受控的速率将实际状态更改为期望状态。 例如，你可以自动化 Kubernetes 来为你的部署创建新容器， 删除现有容器并将它们的所有资源用于新容器。
- **自动完成装箱计算**
    你为 Kubernetes 提供许多节点组成的集群，在这个集群上运行容器化的任务。 你告诉 Kubernetes 每个容器需要多少 CPU 和内存 (RAM)。 Kubernetes 可以将这些容器按实际情况调度到你的节点上，以最佳方式利用你的资源。
- **自我修复**
    Kubernetes 将重新启动失败的容器、替换容器、杀死不响应用户定义的运行状况检查的容器， 并且在准备好服务之前不将其通告给客户端。
- **密钥与配置管理**
    Kubernetes 允许你存储和管理敏感信息，例如密码、OAuth 令牌和 SSH 密钥。 你可以在不重建容器镜像的情况下部署和更新密钥和应用程序配置，也无需在堆栈配置中暴露密钥。
- **批处理执行** 除了服务外，Kubernetes 还可以管理你的批处理和 CI（持续集成）工作负载，如有需要，可以替换失败的容器。
- **水平扩缩** 使用简单的命令、用户界面或根据 CPU 使用率自动对你的应用进行扩缩。
- **IPv4/IPv6 双栈** 为 Pod（容器组）和 Service（服务）分配 IPv4 和 IPv6 地址。
- **为可扩展性设计** 在不改变上游源代码的情况下为你的 Kubernetes 集群添加功能。 
  

Kubernetes 为你提供了一个可弹性运行分布式系统的框架。 Kubernetes 会满足你的扩展要求、故障转移、部署模式等。 例如，Kubernetes 可以轻松管理系统的 Canary 部署。

## 1.2 流程和概念

![](assets/Pasted%20image%2020240514214327.png)

> 概念：

> - **Ingress** ： 是一种用于外网络访问 Kubernetes 服务的规则集。它提供了一种统一的方式来定义和管理入站流量，允许外部客户端通过 HTTP 或 HTTPS 协议访问内部的服务。Ingress 不是一个独立的实体，而是依赖于 Ingress 控制器来实现其功能。常见的 Ingress 控制器有 Nginx、Traefik 和 Contour 等。
> 
> - **Service** 是 Kubernetes 中的一种抽象，它定义了一个逻辑集合的 Pod，并提供了稳定的服务发现和负载均衡的方法。Kubernetes 中 Service 是 将运行在一个或一组 [Pod](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/) 上的网络应用程序公开为网络服务的方法
> 
> - **Deployment** 是一种控制器，用于声明式地管理 Pod 的创建、更新和删除，使Pod拥有多副本，自愈，扩缩容等能力。
> 
> - **Pod** 是 Kubernetes 中的最小部署单元，它可以包含一个或多个紧密相关的容器。这些容器共享同一个网络命名空间，这意味着它们共享同一个 IP 地址和端口范围。Pod 保证了容器的运行环境，包括卷（volumes）、存储资源、网络配置等。



# 2 使用

## 2.1 安装

### 2.1.1 安装

正常安装比较繁琐，可以使用github开源安装教程[K8S安装_管理界面](https://kuboard.cn/)
> 注意，1.19以上的将docker舍弃了，需要下载低版本的。



### 2.1.2 镜像站

[Hub · DaoCloud](https://hub.daocloud.io/)

安装如下：

```shell
kubectl run nginx --image=daocloud.io/library/nginx:1.9.1 -n test
```

> 创建一个名为nginx的pod，从daocloud镜像站，并且命名空间为test





## 2.2 命令

### 2.2.1 nameplace

查询所有的命名空间：

```shell
# 查询所有的命名空间
kubectl get namespace

# 生成命令空间
kubectl create ns hello

# 删除命名空间
kubectl delete ns hello
```

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: hello
```
两者效果一样，这里需要注意的是，yaml文件修改完要apply一下。


### 2.2.2 Pod

```shell
# 运行一个名为nginx版本为latest的docker镜像到pod
kubectl run mynginx --image=nginx

# 查看default名称空间的Pod
kubectl get pods  -A
# -A : 查看所有namespace下的所有pod
# -n namespace : 查看指定容器内的pod


# 描述
kubectl describe pod 你自己的Pod名字 (-n nameplace)

# 删除
kubectl delete pod Pod名字

# 查看Pod的运行日志
kubectl logs Pod名字 (-n nameplace)

# 进入pod中的nginx容器内
kubectl exec -it nginx -n nameplace --bash

# 每个Pod - k8s都会分配一个ip
kubectl get pod -owide

# 使用Pod的ip+pod里面运行容器的端口
curl 192.168.169.136
```



```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: myapp
  name: myapp
  nameplcae: myapp
spec:
  containers:
  - image: nginx
    name: nginx
  - image: tomcat:8.5.68
    name: tomcat
```

> 创建一个命名为myapp的pod，里面有nginx和tomcat的docker容器
>
> 记得apply一下



### 2.2.3 



## 3 应用

通过 [1.2 流程和概念](# 1.2 流程和概念)可以得到一些概念。

[Kubernetes一小时轻松入门_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Se411r7vY/)

> 生产环境下：
>
> - 一个pod对应一个容器
> - 使用service进行对外公开ip，然后设置`spec.ports.nodePort`的值 (必须在30000到32767之间) 和`spec.type`的值为NodePort（一般都是这个）
