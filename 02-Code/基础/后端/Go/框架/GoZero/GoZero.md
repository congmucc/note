# 1、介绍

![img](./assets/20231026111717.png)



# 2、安装环境

[golang 安装 | go-zero Documentation](https://go-zero.dev/docs/tasks)



# 3、Etcd

Etcd是一个高可用的分布式键值存储系统，主要用于共享配置信息和服务发现。它采用Raft一致性算法来保证数据的强一致性，并且支持对数据进行监视和更新

## 3.1、为什么要用etcd

主要是用于微服务的配置中心，服务发现0

![img](./assets/20231026105736.png)

在对外api这个应用里面，怎么知道order服务的rpc地址呢？

写在配置文件里面？

如果服务的ip地址变化了怎么办？在传统的配置文件模式，修改配置文件，应用程序是需要重启才能解决的

所以etcd就是来做这个事情的

![img](./assets/20231026110344.png)

至于为什么不用redis，大家只需要记住，**etcd的数据可靠性更强**

## 3.2、安装

**windows安装**

https://github.com/etcd-io/etcd/releases

**linux安装**

源码安装

https://blog.csdn.net/Mr_XiMu/article/details/127923827

yum安装

版本一般比较老

**docker安装**

```Go
docker run --name etcd -d -p 2379:2379 -p 2380:2380 -e ALLOW_NONE_AUTHENTICATION=yes bitnami/etcd:latest
```

这里需要注意的是，windows安装的不是本体，而是执行命令的程序。本体是docker安装的。windows安装完相应的压缩包之后需要将本体安装

具体相关安装可以看官方文档，windows里面有两步。

## 3.3、基本命令

```Go
// 设置或更新值
etcdctl put name 张三
// 获取值
etcdctl get name
// 只要value
etcdctl get name --print-value-only
// 获取name前缀的键值对
etcdctl get --prefix name
// 删除键值对
etcdctl del name
// 监听键的变化
etcdctl watch name
CopyErrorOK!
```

## 参考文档

etcd windows安装 https://www.cnblogs.com/wujuntian/p/12837926.html

etcdctl指令 https://www.jianshu.com/p/67cbef492812

为什么用etcd https://www.elecfans.com/d/1890103.html





# 微服务demo

这个demo是一个用户微服务，一个视频微服务

视频微服务需要提供一个http接口，用户查询一个视频的信息，并且把关联用户id的用户名也查出来

那么用户微服务就要提供一个方法，根据用户id返回用户信息

## 1 编写rpc的proto文件

> 注意路径问题，都是在最外层目录下执行的
>
> 

user/rpc/user.proto

```protobuf
syntax = "proto3";

package user;

option go_package = "./user";

message IdRequest {
  string id = 1;
}


message UserResponse {
  // 用户id
  string id = 1;
  // 用户名
  string name = 2;
  // 用户性别
  string gender = 3;
}


service User {
  rpc getUser(IdRequest) returns(UserResponse);
}
```

执行命令：`goctl rpc protoc ./user/rpc/user.proto --go_out=./user/rpc/ --go-grpc_out=./user/rpc/ --zrpc_out=./user/rpc/ `进行编译

> 命令出处：[goctl rpc | go-zero Documentation](https://go-zero.dev/docs/tutorials/cli/rpc#goctl-rpc-protoc)

执行命令：`go mod tidy`安装全部依赖

执行命令：`go run user.go`运行脚本，注意路径，这里应该使用`go run user/rpc/user.go`