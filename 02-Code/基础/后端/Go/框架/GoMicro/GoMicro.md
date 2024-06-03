[Go-Micro安装 - wustjq - 博客园 (cnblogs.com)](https://www.cnblogs.com/wustjq/p/16426631.html)

# 环境准备

## 安装go-micro v4

**步骤一：安装go-micro命令行工具**

```shell
go install github.com/go-micro/cli/cmd/go-micro@latest
```

这会自动安装最新版本的go-micro命令行工具。如果您想安装特定版本，可以替换`@latest`为具体的版本号。步骤二：创建一个新的服务
使用`micro new`命令行创建一个新的服务。例如，创建一个名为`newSer`的服务：

如果这个不行就去[go-micro/cli: Go Micro command line interface (github.com)](https://github.com/go-micro/cli)找命令

```shell
go install github.com/go-micro/cli/cmd/go-micro@latest
```

使用下面的命令创建新的service

```shell
go-micro new service newSer
```

这将自动为您生成一个基于go-micro的服务项目结构。

 以后命令都是go-micro，有的人命令是micro是因为以前的版本，这个如果想改成micro可以去GOPATH\bin下修改go-micro.exe名字为micro.exe

根据GRPC进行查看proto怎么安装的。
