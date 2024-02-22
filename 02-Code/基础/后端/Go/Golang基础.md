# 1介绍

## 1.1 Go的特点

Go语言保证了既能到达静态编译语言的安全和性能，又达到了动态语言开发维护的高效率使用一个表达式来形容Go语言：**Go=C+Python**

1. **从c语言中继承**了很多理念，包括表达式语法，控制结构，基础数据类型，调用参数传值，指针等等，也保留了和c语言一样的编译执行方式及弱化的指针。
2. 引入包的概念，用于组织程序结构，Go语言的**一个文件都要归属于一个包**，而不能单独存在。
3. **垃圾回收机制**，内存自动回收，不需开发人员管理
4. **天然并发**
   - 语言层面支持并发，实现简单
   - goroutine，轻量级线程，可实现大并发处理，高效利用多核。
   - 基于CPS并发模型(Communicating Sequential Processes )实现

5. 吸收了管道通信机制，形成Go语言特有的管道channel，通过管道channel，可以实现不同的goroute之间的相互通信。
6. 函数返回多个值
7. 新的创新：比如切片slice、延时执行defer等

## 1.2 Go的强项

1、云计算基础设施领域代表项目：

>  docker、kubernetes、etcd、consul、cloudflare CDN、七牛云存储等。

2、基础后端软件代表项目：

>  tidb、influxdb、cockroachdb等。

3、微服务代表项目：

> go-kit、micro、monzo bank的typhon、bilibili等。

4、互联网基础设施代表项目：

> 以太坊、hyperledger等。

## 1.3 项目管理

**GOPATH**

- 这是Go最初的项目管理，环境配置好GOPATH目录即可，路径推荐$HOME/go(Go默认的)

- GOPATH目录是作为项目的编译产出目录和依赖目录

  - `$GOPATH/pkg` - 用来存放依赖的(比如gin)
  - `$GOPATH/bin` - 用来存放自己项目编译后的二进制文件的
  - `$GOPATH/src` - 用来放自己项目代码的，使用模块区分

- GOPATH项目代码放在$GOPATH/src目录下，没有依赖管理！容易出版本冲突的问题

**Go Vendor**

- 为了解决GOPATH没有依赖版本管理的问题，Go推出了Go Vendor
- 在项目根目录下放一个vendor文件夹，存放依赖的副本信息，解决依赖版本冲突的问题
- 项目无法在vendor找到对应的依赖就会去GOPATH中寻找
- 带来的新问题：项目A依赖 项目B和项目C，而后项目B和项目C依赖不同版本的依赖D，这时D的不同版本都会引进来，造成依赖冲突

**Go Module**

- 经过一系列的迭代，Go带来了Go Module用作项目管理，在Go1.11后被作为默认项目管理
- Go Module通过项目根路径中的`go.mod`文件管理依赖名与版本范围，通过`go.sum`文件记录项目实际使用的依赖和版本
- 注意：Go Module项目目录可以存在任意位置，不强制在`$GOPATH/src`下
- 使用：
  - `mkdir myproject` - 创建项目目录
  - `go mod init` 项目名 - 进入目录执行
  - `go get github.com/gin-gonic/gin@v1.6` - 下载依赖，自动下载到$GOPATH/pkg/mod，有依赖管理
- 注意：
      ○建议将环境变量GO111MODULE设置为auto，这样就会根据当前工作目录下是否有go.mod决定使用什么进行项目管理
      ○不同版本管理下，go的一系列执行命令都会产生变化

## 1.4 常用命令

> - Go有一系列的命令帮助我们去编译、运行、测试等
> - `go mod init {projectName}` - 空文件夹下执行，创建一个Go Module项目，生成go.mod文件
> - `go build -o {binaryName}` -  编译生成(所有go文件以及静态文件)可执行文件，需要在main函数的目录下，建议将main函数的go文件放在项目根目录
> - `go run main.go` - 直接运行Go文件，不会生成可执行文件，这个文件里必须有main函数
> - `go install` - 将编译后的可执行文件存放到$GOPATH/bin下
> - `go get` - 从远程下载第三方依赖到$GOPATH目录下(不同项目管理位置不一样嗷)
> - `go bug` - 提交Bug给Go官方
> - `go test` - 单元测试，Go内置有单元测试
> - `gofmt xxx.go` - 格式化go代码(可配合管道符进行全部格式化)

## 1.5 注意事项

- main函数所在go文件的特点
  - 文件名必须为 `main.go`
  - 必须属于main包，即`package main`
  - 一个项目只允许有一个main文件，推荐将其放在**项目根目录**
- **package**必须出现在有效代码的第一行
- **import**必须写在**package**下面，依赖导入后必须使用
- `main() {` - {必须在同一行，不能够换行

```go
// main包表明其为可执行文件
package main

import "fmt"

// 默认入口函数-和C语言一致
func main() {
	fmt.Print("Hello World!")
}
```

# 2 语言基础

## 2.1 输入输出

- Go有多种输出方式，如下：
  - `fmt.Fprintln()` - 使用OS标准输出流，自带换行
    - 可以将内容直接写进文件 - `fmt.Fprintln({文件流}, "HelloWorld")`
  - `fmt.Fprintf()` - 使用转译输出，换行需自己处理
  - `fmt.Println()` - 简化`Fprint`
  - `fmt.Sprint()` - 类似字符串拼接函数
  - `log` - Go自带日志处理

- Go有多种输入方式，如下：
  - 使用`fmt`包下的`Scan()`、`Scanln()`、`Scanf()`等（区别是分隔符不同）

```go
func main() {
	file, err := os.Create("output.txt")
	if err != nil {
		log.Fatal(err)  // 日志处理
	}
	defer file.Close()

	// 将字符串 Hello, World!写入文件
	fmt.Fprintln(file, "Hello, World!")

	// 转译输出
	fmt.Printf("Hello \t World \n Hello")

	// 拼接返回一个字符串
	sentence := fmt.Sprint("你好,我今年", 20, "岁")
	fmt.Print(sentence)
}
```

> 输出demo

```go
func main() {
	var name string
	var age int

	// 输入 20,Aomsir
	fmt.Scanf("%d,%s", &age, &name)
	fmt.Printf("%s\t%d\n", name, age)
}
```

> 输入demo



## 2.2 定义变量

- Go命名规则：以字母或下划线开头，后面由数字，字母，下划线组成。

声明变量：

1. `var name string` - 可全局使用。
2. `name := "Aomsir"` - 只能在函数内使用，但用得最多。
3. `var name = "Aomsir"` - 与2等价，但是全局可用。

注意：

- Go语言中，`=`和`:=`都是用来赋值的，但使用上有差别。
- `=`用于给已有变量进行复制。
- `:=`是短变量声明，用于声明短变量并进行赋值。

```go
// 批量声明定义变量
var (
	age     int    = 12
	content string = "I'm content\n"
)

func main() {
	// 结构体类型
	student := [...]struct {
		name string
		age  int
	}{
		{"Aomsir", 20},
		{"Jewix", 24},
	}
	for index := 0; index < len(list); index++ {
		fmt.Println(list[index])
	}
	fmt.Println(student)
}
```





## 2.3 定义常量

- Go语言中，常量使用`const`关键字进行声明定义，不强制要求使用，值不能够被改变。

- Go中常量可以参与运算。

- 常量生成器(`iota`)如下所示：
  - 默认第一个为0，后面依次递增。

```go
package main

import "fmt"

func main() {
	// 常量生成器
	const (
		a = iota
		b
		c
	)

	// 0 1 2
	fmt.Println(a, b, c)
}
```



## 2.4 数据类型

- Golang提供有多种数据类型，如下：

- `bool`
  - 布尔类型，值为 `true/false`，默认为 `false`，无法参与运算。

- `int8/uint8`、`int16/uint16`、`int32/uint32`、`int64/uint64`、`int/uint`（默认，根据CPU决定位数）
  - 一般使用整型默认为 `int`。
  - `int8`代表8位有符号整数，以此类推，`uint`同理。
  - `uint`代表无符号整数。

- `float32`、`float64`
  - `float32`为单精度浮点型。
  - `float64`为双精度浮点型，用的更多。

- `string`
  - 字符串类型，较为灵活。

- `complex64`、`complex128`
  - 复数类型带有实部和虚部，通常用来计算。

- `array`
  - 同一类型固定长度数组，元素类型与元素长度在声明时已确定。

- `slice`
  - 切片类型，是一种动态数组类型，支持动态扩容，提供灵活的访问和修改元素的方式。
  - 切片类型（以 `int` 为例）定义的时候不需要指定元素个数，比如 `[]int`。
  - 定义可以使用 `make` 函数，如下，当前存储5个元素，初始值都为0，最多可扩容10个（如满系统会自动扩容）。

- `map`
  - 键值对类型，与切片一致，有两种方式进行定义。
  - 定义：
    - `var mapName map[keyType]valueType`
    - `make(map[keyType]valueType)`

- `chan`
  - 在Go中，管道类型是一种内存的通信方式，用于在不同的Goroutine之间传递数据。
  - 管道可以看作一个队列，设置size即为缓冲管道，否则为不缓冲管道。
  - 定义： `var channelName chan elementType`
  - 初始化： `make(chan elementType, size)`
  - 还有单向管道，双向管道，用到再查。

- `struct`
  - 结构体类型，定义与使用如下。
  - Go语言没有明确的面向对象特性，结构体可看为类。
  - 属性名首字母大写代表为public，小写为private，公开的话可以被其他包直接访问（例如：`person1.Name`），否则只能通过暴露的函数。

- `interface`
  - 接口类型，与Java类似，是一种抽象类型，定义一组方法的集合（没有实现）。
  - 这部分需要结合结构体与函数一起看。
  - `interface{}`，可以代表任何事务，类似于泛型，可以代表变量或函数。

- 注意：

  - Go当中变量的数据类型转换只有显示转换。
  - 数据类型转换：`类型(变量名或值)`。
  - Go语言没有字符类型，可以使用字符编码进行转换，如 `fmt.Printf("%c",i)`。
  - 空指针为 `nil`。
  - 查看占用字节数：`unsafe.Sizeof(var)`。
  - 不同数据类型的变量进行运算必须将类型转换成一样。