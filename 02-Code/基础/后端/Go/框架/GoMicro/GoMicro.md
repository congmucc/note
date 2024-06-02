安装go-micro v4

步骤一：安装go-micro命令行工具

```shell
go install github.com/go-micro/cli/cmd/go-micro@latest
```

这会自动安装最新版本的go-micro命令行工具。如果您想安装特定版本，可以替换`@latest`为具体的版本号。步骤二：创建一个新的服务
使用`micro new`命令行创建一个新的服务。例如，创建一个名为`newSer`的服务：

```shell
micro new service newSer
```

这将自动为您生成一个基于go-micro的服务项目结构。