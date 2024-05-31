# 1、安装

官方地址：[Gin Web Framework (gin-gonic.com)](https://gin-gonic.com/zh-cn/)

# 2、golang程序热加载

工具 1（推荐）：https://github.com/gravityblast/fresh

```shell
go get github.com/pilu/fresh
D:\gin_demo>fresh
```

工具 2：https://github.com/codegangsta/gin

```shell
go get -u github.com/codegangsta/gin
D:\gin_demo>gin run main
```



# 3、Gin框架中的路由

## 3.1、介绍

路由（Routing）是由一个 URI（或者叫路径）和一个特定的 HTTP 方法（GET、POST 等） 组成的，涉及到应用如何响应客户端对某个网站节点的访问。

## 3.2、简单的路由配置
### 3.2.1、GET

```go
r.GET("网址", func(c \*gin.Context) {
	c.String(200, "Get")
})
```

### 3.2.2、POST

```go
r.POST("网址", func(c \*gin.Context) {
	c.String(200, "POST")
})
```

### 3.2.3、PUT

```go
r.PUT("网址", func(c \*gin.Context) {
	c.String(200, "PUT")
})
```

### 3.2.4、DELETE 

```go
r.DELETE("网址", func(c \*gin.Context) {
	c.String(200, "DELETE")
})
```

### 3.2.5、路由里面获取 Get 传值

域名/news?aid=20

```go
r.GET("/news", func(c \*gin.Context) {
	aid := c.Query("aid")
	c.String(200, "aid=%s", aid)
})
```

### 3.2.6、动态路由

域名/user/20

```go
r.GET("/user/:uid", func(c \*gin.Context) {
	uid := c.Param("uid")
	c.String(200, "userID=%s", uid)
})
```



## 3.3、c.String() c.JSON() c.JSONP() c.XML() c.HTML()

**返回一个字符串**

```go
r.GET("/news", func(c \*gin.Context) {
	aid := c.Query("aid")
	c.String(200, "aid=%s", aid)
})
```

**返回一个JSON数据**

```go
func main() {
    r:= gin.Default()
    // gin.H 是 map[string]interface{}的缩写
    r.GET("/someJSON", func(c \* gin.Context) {
        // 方式一：自己拼接 JSON
        c.JSON(http.StatusOK, gin.H{ "message": "Hello world!" })
    })
    r.GET("/moreJSON", func(c \* gin.Context) {
        // 方法二：使用结构体
        var msg struct {
            Name string `json:"user"`
            Message string
            Age int
        }

        msg.Name = "IT 营学院"
        msg.Message = "Hello world!"
        msg.Age = 18
        c.JSON(http.StatusOK, msg)
    })
    r.Run(":8080")
}
```

**JsonPN**

```go
func main() {
	r := gin.Default()
	r.GET("/JSONP", func(c \*gin.Context) {
		data := map[string]interface{}{
		"foo": "bar",
	}
	// /JSONP?callback=x
	// 将输出：x({\"foo\":\"bar\"})
	c.JSONP(http.StatusOK, data)
	})
    
	// 监听并在 0.0.0.0:8080 上启动服务
	r.Run(":8080")
}
```

> 和json的区别是 如果在页面上输入`JSONP?callback=x`将在页面上输出：`x({\"foo\":\"bar\"})`

**返回 XML 数据**

```go
func main() {
	r := gin.Default()
	// gin.H 是 map[string]interface{}的缩写
	r.GET("/someXML", func(c \*gin.Context) {
		// 方式一：自己拼接 JSON
		c.XML(http.StatusOK, gin.H{"message": "Hello world!"})
	})

	r.GET("/moreXML", func(c \*gin.Context) {
		// 方法二：使用结构体
		type MessageRecord struct {
			Name string
			Message string
			Age int
		}

		var msg MessageRecord
		msg.Name = "IT 营学院"
		msg.Message = "Hello world!"
		msg.Age = 18
		c.XML(http.StatusOK, msg)
	})

	r.Run(":8080")
}
```

**渲染模板**

```go
router.GET("/", func(c \*gin.Context) {
	c.HTML(http.StatusOK, "default/index.html", map[string]interface{}{
		"title": "前台首页"
	})
})
```

# 4、Gin HTML 模板渲染

## 4.1、全部模板放在一个目录里面的配置方法

1、我们首先在项目根目录新建 templates 文件夹，然后在文件夹中新建 index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>这是一个 html 模板</h1>
    <h3>{{.title}}</h3>
</body>
</html
```

2、Gin 框架中使用 c.HTML 可以渲染模板，渲染模板前需要使用 LoadHTMLGlob()或者 LoadHTMLFiles()方法加载模板

```go
router.GET("/", func(c *gin.Context) {
    c.HTML(http.StatusOK, "default/index.html", map[string]interface{}{ 
        "title": "前台首页"
    })
})
router.GET("/", func(c *gin.Context) {
    c.HTML(http.StatusOK, "index.html", gin.H{ 
        "title": "Main website", 
    })
})
```

```go
package main
import ( 
    "net/http"
    "github.com/gin-gonic/gin"
)

func main() {
    router := gin.Default()
    router.LoadHTMLGlob("templates/*")
    //router.LoadHTMLFiles("templates/template1.html", "templates/template2.html")
    router.GET("/", func(c *gin.Context) {
        c.HTML(http.StatusOK, "index.html", gin.H{
            "title": "Main website", 
        })
    })
    router.Run(":8080")
}
```



## 4.2、模板放在不同目录里面的配置方法

Gin 框架中如果不同目录下面有同名模板的话我们需要使用下面方法加载模板

 注意：定义模板的时候需要通过 define 定义名称 templates/admin/index.html

相当于给模板定义一个名字 define end 成对出现

```go
{{ define "admin/index.html" }}
    <!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="UTF-8">
            <meta http-equiv="X-UA-Compatible" content="IE=edge">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>Document</title>
        </head>
        <body>
            <h1>后台模板</h1>
            <h3>{{.title}}</h3>
        </body>
    </html>
{{ end }}
```

业务逻辑

```go
package main
import ( 
    "net/http"
	"github.com/gin-gonic/gin"
)
func main() {
    router := gin.Default()
    router.LoadHTMLGlob("templates/**/*")
    router.GET("/", func(c *gin.Context) {
        c.HTML(http.StatusOK, "default/index.html", gin.H{ 
            "title": "前台首页", 
        })
    })
    router.GET("/admin", func(c *gin.Context) {
        c.HTML(http.StatusOK, "admin/index.html", gin.H{ 
            "title": "后台首页", 
        })
    })
    router.Run(":8080")
}
```

注意：如果模板在多级目录里面的话需要这样配置 r.LoadHTMLGlob("templates/\*\*/\*\*/\*") /** 表示目录

## 4.3、gin 模板基本语

### 4.3.1、{{.}} 输出数

模板语法都包含在{{和}}中间，其中{{.}}中的点表示当前对象。

当我们传入一个结构体对象时，我们可以根据.来访问结构体的对应字段。例如：

```go
package main
import ( 
    "net/http"
	"github.com/gin-gonic/gin"
)

type UserInfo struct {
    Name string
    Gender string
    Age int
}
func main() {
    router := gin.Default()
    router.LoadHTMLGlob("templates/**/*")
    user := UserInfo{
    Name: "张三", Gender: "男", Age: 18, }
    router.GET("/", func(c *gin.Context) {
        c.HTML(http.StatusOK, "default/index.html", map[string]interface{}{ 
            "title": "前台首页", 
            "user": user,
        })
	})
	router.Run(":8080")
}
```

 相当于给模板定义一个名字 define end 成对出现

```html
{{ define "default/index.html" }}
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    <body>
        <h1>前台模板</h1>
        <h3>{{.title}}</h3>
        <h4>{{.user.Name}}</h4>
        <h4>{{.user.Age}}</h4>
    </body>
</html>
{{end}}
```

### 4.3.2、注释

```go
{{/* a comment */}}
```

注释，执行时会忽略。可以多行。注释不能嵌套，并且必须紧贴分界符始止

### 4.3.3、变量

我们还可以在模板中声明变量，用来保存传入模板的数据或其他语句生成的结果。具体语法 如下

```html
<h4>{{$obj := .title}}</h4>
<h4>{{$obj}}</h4>
```

### 4.2.4、移除空格

有时候我们在使用模板语法的时候会不可避免的引入一下空格或者换行符，这样模板最终渲 染出来的内容可能就和我们想的不一样，这个时候可以使用{{-语法去除模板内容左侧的所有 空白符号， 使用-}}去除模板内容右侧的所有空白符号。

```html
{{- .Name -}}
```

**注意**：-要紧挨{{和}}，同时与模板值之间需要使用空格分隔。

### 4.3.5、比较函数

布尔函数会将任何类型的零值视为假，其余视为真。 

下面是定义为函数的二元比较运算的集合： 

```go
eq 	如果 arg1 == arg2 则返回真

ne 	如果 arg1 != arg2 则返回真

lt 	如果 arg1 < arg2 则返回真 

le 	如果 arg1 <= arg2 则返回真

gt 	如果 arg1 > arg2 则返回真 

ge 	如果 arg1 >= arg2 则返回真
```

### 4.3.6、条件判断

```html
{{if pipeline}} T1 {{end}}
{{if pipeline}} T1 {{else}} T0 {{end}}
{{if pipeline}} T1 {{else if pipeline}} T0 {{end}}
{{if gt .score 60}}
及格
{{else}}
不及格
{{end}}

{{if gt .score 90}}
优秀
{{else if gt .score 60}}
及格
{{else}}
不及格
{{end}}
```



### 4.3.7、range

Go 的模板语法中使用 range 关键字进行遍历，有以下两种写法，其中 pipeline 的值必须是数 组、切片、字典或者通道。

```html
{{range $key,$value := .obj}}
	{{$value}}
{{end}
```

如果 pipeline 的值其长度为 0，不会有任何输出

```html
{{$key,$value := .obj}}
	{{$value}}
{{else}}
	pipeline 的值其长度为 0
{{end}
```

如果 pipeline 的值其长度为 0，则会执行T0

```go
router.GET("/", func(c *gin.Context) {
    c.HTML(http.StatusOK, "default/index.html", map[string]interface{}{ 
        "hobby": []string{"吃饭", "睡觉", "写代码"}, 
    })
})

{{range $key,$value := .hobby}}
	<p>{{$value}}</p>
{{end}}

user := UserInfo{
    Name: "张三", 
    Gender: "男", 
    Age: 18, 
}

router.GET("/", func(c *gin.Context) {
	c.HTML(http.StatusOK, "default/index.html", map[string]interface{}{ 
        "user": user, 
    })
})
```

以前要输出数据：

```go
<h4>{{.user.Name}}</h4>
<h4>{{.user.Gender}}</h4>
<h4>{{.user.Age}}</h4>
```

现在要输出数据：

```go
{{with .user}}
    <h4>姓名：{{.Name}}</h4>
    <h4>性别：{{.user.Gender}}</h4>
    <h4>年龄：{{.Age}}</h4>
{{end}}
```

简单理解：相当于 var .=.use

### 4.3.8、预定义函数 （了解）

执行模板时，函数从两个函数字典中查找：首先是模板函数字典，然后是全局函数字典。一 般不在模板内定义函数，而是使用 Funcs 方法添加函数到模板里。

预定义的全局函数如下：

- and 
  函数返回它的第一个 empty 参数或者最后一个参数；
  就是说"and x y"等价于"if x then y else x"；所有参数都会执行； 

- or 
  返回第一个非 empty 参数或者最后一个参数； 
  亦即"or x y"等价于"if x then x else y"；所有参数都会执行； 

- not 
  返回它的单个参数的布尔值的否定 

- len 
  返回它的参数的整数类型长度 

- index 
  执行结果为第一个参数以剩下的参数为索引/键指向的值； 
  如"index x 1 2 3"返回 x[1][2][3]的值；每个被索引的主体必须是数组、切片或者字典。 

- print 
  即 fmt.Sprint 

- printf 
  即 fmt.Sprintf 

- println 
  即 fmt.Sprintln 

- html 
  返回与其参数的文本表示形式等效的转义 HTML。 
  这个函数在 html/template 中不可用。 

- urlquery 
  以适合嵌入到网址查询中的形式返回其参数的文本表示的转义值。 
  这个函数在 html/template 中不可用。 

- js 
  返回与其参数的文本表示形式等效的转义 JavaScript。 

- call 
  执行结果是调用第一个参数的返回值，该参数必须是函数类型，其余参数作为调用该函 数的参数； 如"call .X.Y 1 2"等价于 go 语言里的 dot.X.Y(1, 2)； 其中 Y 是函数类型的字段或者字典的值，或者其他类似情况； call 的第一个参数的执行结果必须是函数类型的值（和预定义函数如 print 明显不同）； 该函数类型值必须有 1 到 2 个返回值，如果有 2 个则后一个必须是 error 接口类型； 如果有 2 个返回值的方法返回的 error 非 nil，模板执行会中断并返回给调用模板执行者 该错误:

  ```go
  {{len .title}}
  {{index .hobby 2}}
  ```

### 4.3.9、自定义模板函数

```go
router.SetFuncMap(template.FuncMap{ 
    "formatDate": formatAsDate, 
})
```

```go
package main
import ( 
    "fmt"
    "html/template"
    "net/http"
    "time"
    "github.com/gin-gonic/gin"
)

func formatAsDate(t time.Time) string {
	year, month, day := t.Date()
	return fmt.Sprintf("%d/%02d/%02d", year, month, day)
}

func main() {
	router := gin.Default()
    //注册全局模板函数 注意顺序，注册模板函数需要在加载模板上面
    router.SetFuncMap(template.FuncMap{ "formatDate": formatAsDate, })
    //加载模板
    router.LoadHTMLGlob("templates/**/*")
    router.GET("/", func(c *gin.Context) {
        c.HTML(http.StatusOK, "default/index.html", map[string]interface{}{ 
            "title": "前台首页", 
            "now": time.Now(), 
        })
    })
    router.Run(":8080")
}
```

模板里面的用法

```go
{{.now | formatDate}}
或者
{{formatDate .now }}
```

## 4.4、嵌套 template

1、新建 templates/deafult/page_header.html

```go
{{ define "default/page_header.html" }}
	<h1>这是一个头部</h1>
{{end}}
```

2、外部引入

注意： 

1、引入的名字为 page_header.html 中定义的名字 

2、引入的时候注意最后的点（.）

```go
{{template "default/page_header.html" .}
```

```go
<!-- 相当于给模板定义一个名字 define end 成对出现-->
{{ define "default/index.html" }}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    {{template "default/page_header.html" .}}
</body>
</html>
{{end}}
```

# 5、静态文件服务

当我们渲染的 HTML 文件中引用了静态文件时,我们需要配置静态 web 服务 r.Static("/static", "./static")   前面的/static 表示路由   后面的./static 表示路径

```go
func main() {
    r := gin.Default()
    r.Static("/static", "./static")
    r.LoadHTMLGlob("templates/**/*")
    // ... r.Run(":8080")
}
<link rel="stylesheet" href="/static/css/base.css" />
```

# 6、路由详解

路由（Routing）是由一个 URI（或者叫路径）和一个特定的 HTTP 方法（GET、POST 等） 组成的，涉及到应用如何响应客户端对某个网站节点的访问。

路由传值、路由返回值