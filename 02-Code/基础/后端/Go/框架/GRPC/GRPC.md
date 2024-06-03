# 环境安装





## protobuf

### 安装protobuf

```
https://github.com/protocolbuffers/protobuf
```

> 通过这个网站进行安装，然后配置环境变量
>
> 使用命令`protoc --version`查看安装情况



### 安装protoc-gen-go和protoc-gen-go-grpc

[Protocol Buffer Basics：Go|方案缓冲液文档)](https://protobuf.dev/getting-started/gotutorial/)

```shell
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
```

```shell
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
```

> 这个是新版
>
> 使用命令`protoc-gen-go`查看，如果没有报错就安装成功

例子

```protobuf
syntax = "proto3";

package hello;

option go_package = "./;hello";

message Say{
  int64           id    = 1;
  string          hello = 2;
  repeated string word  = 3;
}
```

> 使用gpt解释吧。

### 编译命令

**1、编译指令**

```shell
protoc -I=$SRC_DIR --xxx_out=$DST_DIR $SRC_DIR/xxx.proto 
```

> 通过 --proto_path 或 -I 命令行标记来指定源 proto 文件和依赖的 proto 文件的目录路径
>
> $SRC_DIR：存放协议源文件的目录地址；
> $DST_DIR：输出代码文件的目录地址；
> xxx.proto：协议源文件名称；
> –xxx_out：根据自己的需要，选择对应的语言，例如（Java：–java_out，C++：–cpp_out 等）；
> 可通过在命令提示符中输入 protoc --help 查看更多帮助。

如果我们在 user.proto 文件所在路径下，则可以使用下面的指令直接生成

```shell
 protoc --go_out=./ *user.proto
```

```shell
protoc --go-grpc_out=. user.proto
```



> 这两个都需要运行，这是新版



```shell
 protoc user.proto --go_out=. --go-grpc_out=.
```

> 整合命令



# grpc使用

[4.proto文件 (fengfengzhidao.com)](https://docs.fengfengzhidao.com/#/docs/grpc文档/4.proto文件?id=多服务)



## Protobuf

Grpc结合Protobuf

```protobuf
syntax="proto3";

package user;

option  go_package = "./user;user";

service UserService {
  rpc GetUser(User) returns (Result){};
}


message User {
  string name = 1;
  int32 age = 2;
  string address = 3;
  string email = 4;
}

message Result {
  string code = 1;
  string message = 2;
}
```

## 服务端

```go
package main

import (
	"context"
	"fmt"
	"google.golang.org/grpc"
	"net"
	"protoDemo/user"
)

// 这里需要继承服务端UserServiceServer
type userService struct {
	user.UserServiceServer
}

// 这里需要实现getUser方法
func (userService) GetUser(ctx context.Context, use *user.User) (result *user.Result, err error) {
	fmt.Println("入参： ", use)

	return &user.Result{
		Code:    "200",
		Message: "结果是: " + use.Name,
	}, nil
}

func main() {
	listen, listenErr := net.Listen("tcp", ":8080")
	if listenErr != nil {
		fmt.Println(listenErr)
	}

	s := grpc.NewServer()
	server := userService{}
	user.RegisterUserServiceServer(s, &server)
	fmt.Println("grpc server running :8080")

	err := s.Serve(listen)
	if err != nil {
		return
	}

}

```

## 客户端

```go
package main

import (
    "context"
    "fmt"
    "google.golang.org/grpc"
    "google.golang.org/grpc/credentials/insecure"
    "log"
    "protoDemo/user"
)

// 这个是固定的
func getClient() user.UserServiceClient {
    addr := ":8080"
    conn, dialErr := grpc.Dial(addr, grpc.WithTransportCredentials(insecure.NewCredentials()))

    if dialErr != nil {
       log.Fatalf(fmt.Sprint("grpc connect addr [%s] 连接失败 %s", addr, dialErr))
    }

    defer conn.Close()

    client := user.NewUserServiceClient(conn)
    return client
}

func main() {
    client := getClient()

    result, err := client.GetUser(context.Background(), &user.User{
       Name:    "张三",
       Age:     18,
       Address: "北京",
       Email:   "123@qq.com",
    })

    result2, err2 := client.GetUser(context.Background(), &user.User{
       Name:    "李四",
       Age:     18,
       Address: "北京",
       Email:   "123@qq.com",
    })

    fmt.Println(result, err)

    fmt.Println(result2, err2)
}
```





## 总结

 其实protobuf主要作用是是写一个公共的函数，在服务端，你可以写方法进行继承如getUser，然后进行重写。



用户端到不用这么麻烦，就发送，这个生成的代码可以自动生成，因为就一个发送的功能，如果需要重写的话可以重写。客户端需要重写是因为需要写逻辑，并返回结果，这个必须需要重写。