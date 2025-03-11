[TOC]

# Vue

[Vue3 + Vite + Ts 开发必备的 VSCode 插件 - 掘金 (juejin.cn)](https://juejin.cn/post/7207304028253683769)



# 一、关于命名

### 1、项目命名

项目名使用小写英文单词，多个之间使用连字符 "-" ，例如 `my-test`

### 2、包命名

##### 2.1、项目基本包

com.company.{项目英文名（较长时适当简化）}.{模块名（可选）}
 例如：test 包命名为 com.dhsg.test

**注意：***包命名全为小写英文字母，不可以使用大写英文字母，不可以使用 "_"、"-" 等符号*

##### 2.2、模块

- config：配置类
- filter：过滤器
- common：公共类，定义常量类，组件
- enums：枚举类（由于 enum 是 java 的关键字，所以使用 enums）
- entity：数据库相关的实体类
- vo：数据模型类(参数模型，数据传输模型等）
- controller：控制层接口
- service：服务层接口
- service.impl：服务层接口实现
- mapper：数据库访问层接口
- exception：异常
- util：工具类

### 3、类命名

类名首字母大写，如果由多个单词组成，每个单词的首字母都要大写，也即***大驼峰式命名法\***
 例如：MyFirstClass.java



```cpp
public class MyFirstClass{

}
```

**注意：**

- *如果类名由多个单词组成，每个单词的首字母都要大写，包括第一个单词*
- *接口类名前面要加前缀 "I" ，例如服务层的一个接口类，IHelloService.java*

### 4、变量命名

变量名首字母小写，如果由多个单词组成，则除了第一个单词其他每个单词的首字母都要大写，也即***小驼峰式命名法\***
 例如：`String myName = "小驼峰式命名法";`

### 5、常量命名

常量名全部大写，如果由多个单词组成，则每个单词直接用下划线 "_" 分隔
 例如：`public static final String SYSTEM_COLOR= "RED";`

### 6、方法命名

同变量命名一样，使用***小驼峰式命名法\***
 例如：



```cpp
public void myFunction(){

}
```

### 7、补充

- 名称只能由字母、数字、下划线、$符号组成
- 不能以数字开头
- 名称不能使用 JAVA 中的关键字
- 坚决不允许出现中文及拼音命名

# 二、关于日志

### 1、统一使用 slf4j 和 logback 完成日志功能



```xml
        <!-- slf4j -->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.25</version>
        </dependency>

        <!-- logback -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-core</artifactId>
            <version>1.2.3</version>
        </dependency>
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.3</version>
        </dependency>
```

### 2、输出日志格式



```ruby
%boldGreen(%date{yyyy-MM-dd HH:mm:ss}) | %highlight(%-5level) | %cyan(%thread) | %magenta(%logger) | %n %msg %n%n
```

### 3、输出位置

统一将日志文件输出到项目根目录下的 log 文件夹

### 4、使用示例



```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class Example {

    private static final Logger logger = LoggerFactory.getLogger(Example.class);

    public void function(){
        
        logger.error("error");
        logger.warn("warn");
        logger.info("info");
        logger.debug("debug");
        logger.trace("trace");

    }

}
```

# 三、关于异常

# 四、关于接口

### 1、URL 命名

##### 模板：`URL = scheme://{domain}/{version}/{endpoint}/[?query][#fragment]`

**scheme：***指底层用的协议，如 http、https、ftp*
 **domain：***服务器的 IP 地址或者域名，例如 [https://api.example.com](https://links.jianshu.com/go?to=https%3A%2F%2Fapi.example.com)*
 **version：***版本，例如 [https://api.example.com/v1/](https://links.jianshu.com/go?to=https%3A%2F%2Fapi.example.com%2Fv1%2F)，另一种做法是，将版本号放在 HTTP 头信息中，但不如放入 URL 方便和直观。Github 采用这种做法。*
 **endpoint：***资源路径，表示 API 的具体网址，要为复数。举例来说，有一个 API 提供产品的信息，还包括各种订单和用户的信息，则它的路径应该设计成下面这样。*

- [https://api.example.com/v1/products](https://links.jianshu.com/go?to=https%3A%2F%2Fapi.example.com%2Fv1%2Fproducts)
- [https://api.example.com/v1/orders](https://links.jianshu.com/go?to=https%3A%2F%2Fapi.example.com%2Fv1%2Forders)
- [https://api.example.com/v1/customers](https://links.jianshu.com/go?to=https%3A%2F%2Fapi.example.com%2Fv1%2Fcustomers)

**query：***为发送给服务器的参数*

- ?limit=10：指定返回记录的数量。
- ?offset=10：指定返回记录的开始位置。
- ?page=2&per_page=100：指定第几页，以及每页的记录数。
- ?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。

**fragment：***锚点，定位到页面的资源，锚点为资源 id*

**补充：**

1. 正斜杠分隔符 "/" 必须用来指示层级关系。
2. 应该使用连字符 "-" 来提高 URI 的可读性。
3. URI 路径中首选小写字母。
4. URI 路径名词均为复数，使用数据库表名加后缀 "s" 。
5. POST 方式避免使用 URI 编码的参数，尽量保持 URI 的干净。

### 2、接口方法命名

根据该接口的方法类型来命名方法名，例如
 当该接口的 URI 为 "/orders"，则

- GET：getOrders
- POST：postOrders
- PUT：putOrders
- DELETE：deleteOrders

### 3、接口传参

根据该接口的方法类型来定义，例如

- **GET：**

当参数含有层级关系的个时候，优先使用 "/" 分割传值方式，例如：
 [https://www.example.com/v1.1/products/](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.example.com%2Fv1.1%2Fproducts%2F){id值}

当有可选参数时候，使用 `?param1="xxx"&m2="xxx"` 方式，例如：
 [https://www.example.com/v1.1/products?type=](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.example.com%2Fv1.1%2Fproducts%3Ftype%3D)"xxx"&name="xxx"

**补充：**
 URI 保持简约层级风格，可以混合使用，比如查找某商户下的产品：
 [https://www.example.com/v1.1/products?type=](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.example.com%2Fv1.1%2Fproducts%3Ftype%3D)"xxx"&name="xxx"

- **POST：**

Json 格式包装参数提交，body 携带参数数据，例如：



```dart
POST  https://www.example.com/v1.1/products

Content-Type: application/json;charset=utf-8
{
"type":"类型1",
"name":"产品名称1"，
"price": 1000.00
}
```

- **PUT：**

Json 格式包装参数提交，body 携带参数数据，例如。



```dart
PUT https://www.example.com/v1.1/products

Content-Type: application/json;charset=utf-8
{
"type":"类型1",
"name":"产品名称1"，
"price": 1000.00
}
```

- **DELETE：**

使用 "/" 分割传值方式，例如删除产品信息：
 DELETE  [https://www.example.com/v1.1/products/](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.example.com%2Fv1.1%2Fproducts%2F){id} #

### 4、响应体格式

在 common 包下新建两个类

CommonResult.java



```dart
import lombok.Data;

import java.io.Serializable;

/**
 * 统一返回格式
 *
 * @author dhsg
 * @date 2019-10-14
 **/
@Data
public class CommonResult implements Serializable {

    /**
     * 返回状态，1表示成功,0表示失败
     */
    private int status;

    /**
     * 返回信息
     */
    private String message;

    /**
     * 返回实体数据json格式
     */
    private Object data;

    /**
     * 有参构造函数
     *
     * @param status  返回状态，1表示成功,0表示失败
     * @param message 返回信息
     * @param data    返回实体数据json格式
     */
    public CommonResult(int status, String message, Object data) {
        this.status = status;
        this.message = message;
        this.data = data;
    }

    /**
     * 无参构造函数
     */
    public CommonResult() {
    }

}
```

CommonResultUtil.java



```php
/**
 * 统一返回工具类
 *
 * @author dhsg
 * @date 2019-10-14
 **/
public class CommonResultUtil {

    /**
     * 成功标识码
     */
    private static Integer SUCCESS_CODE = 1;

    /**
     * 失败标识码
     */
    private static Integer FAIL_CODE = 0;

    /**
     * 返回成功
     */
    public static CommonResult success() {
        return new CommonResult(SUCCESS_CODE, "", null);
    }

    /**
     * 返回成功,并带上信息和数据
     * @param message 返回信息
     * @param data    返回实体数据json格式
     */
    public static CommonResult success(String message, Object data) {
        return new CommonResult(SUCCESS_CODE, message, data);
    }


    /**
     * 返回失败
     */
    public static CommonResult fail() {
        return new CommonResult(FAIL_CODE, "", null);
    }

    /**
     * 返回失败,并带上信息
     * @param message 返回信息
     */
    public static CommonResult fail(String message) {
        return new CommonResult(FAIL_CODE, message, null);
    }

}
```

使用示例



```kotlin
return CommonResultUtil.success("查询成功", null);
```

### 5、接口注释

controller 层统一使用 swagger2 组件管理 rest 接口文档。

# 五、关于注释

### 1、类注释

在每个类前面必须加上类注释，注释模板如下：



```dart
/**
* 类的详细说明
*
* @author  类创建者姓名
* @date    创建日期 YYYY-MM-DD
*/
```

### 2、属性注释

在每个属性前面必须加上属性注释，注释模板如下：



```cpp
/** 
* 提示信息 
*/
```

例如：



```tsx
/** 
* 系统名称 
*/
private String systemName = null;
```

### 3、方法注释

在每个方法前面必须加上方法注释，注释模板如下：



```dart
/**
* 方法的详细使用说明
*
* @param  参数名 参数1的使用说明
* @return 返回结果的说明
* @throws 异常类型 注明从此类方法中抛出异常的说明
*/
```

例如：



```dart
    /**
     * 一个用于说明方法上如何注释的例子
     * @param param1 参数1说明
     * @param param2 参数2说明
     * @return 返回说明
     * @throws Exception 异常说明
     */
    public String myFunction(String param1,int param2) throws Exception{
        return null;
    }
```

### 4、方法内注释

在方法内部的代码上方使用单行或者多行注释，该注释根据实际情况添加。
 例如：



```dart
    /**
     * 一个用于说明方法上如何注释的例子
     * @param param1 参数1说明
     * @param param2 参数2说明
     * @return 返回说明
     * @throws Exception 异常说明
     */
    public String myFunction(String param1,int param2) throws Exception{

        // 单行注释，控制台打印 param1
        System.out.println(param1);

        // 多行注释
        // 控制台打印 param2
        System.out.println(param2);
        return null;
    }
```

**注意：***一定要在代码的上方写注释，不可以追加在代码尾部，像下面的写法是不被允许的*



```csharp
System.out.println(param1); // 控制台打印 param1 
```

### 5、其他

- 注释中的说明信息使用中文
- 在开发过程中，常常有一些方法有了新的实现思路，但是还没完全做好，于是需要先把旧的方法注释一下。但是在开发好了之后，一定要记得还原或者删除这些不需要的代码，保持代码的整洁。

# 六、版本控制

### 1、拉取原则

- 每日开始工作拉取
- 提交之前拉取

### 2、提交原则

- 提交代码必须构建成功（编译，打包成功）
- 提交代码必须完整（不能漏掉一些文件）
- 提交代码必须忽略到本地临时文件（target, logs, .idea, *.iml,dist 等）

### 3、提交注释

- 使用中文填写注释
- 注释要反映本次提交变更的情况，注释描述添加前缀，前缀如下
   【创建】 通常在项目创建时使用
   【新增】
   【修改】
   【删除】
   【修复-number】 修复 Bug 使用，number 是 Bug 编号
   例如：`【新增】 新增一个控制器类 HelloController.java`

# 七、项目管理

### 1、使用 maven 做项目管理

### 2、pom.xml 中新增依赖的时候，必须添加注释

例如：



```xml
        <!-- web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
```

### 3、所有微服务工程必须依赖父 maven 工程，作为父 maven 工程的子工程

### 4、关于一些依赖的版本，需要在父 maven 的 pom 文件中指定作为常量引用

### 5、项目结构

|                 1                  |         2          |               3               |         4          |          5          |
| :--------------------------------: | :----------------: | :---------------------------: | :----------------: | :-----------------: |
|  src/main/java -- 存放 java 文件   |         -          |               -               |         -          |          -          |
|                 -                  |    com.chinadci    |               -               |         -          |          -          |
|                 -                  |         -          |            config             |         -          |          -          |
|                 -                  |         -          |               -               |   XxxConfig.java   |          -          |
|                 -                  |         -          |            common             |         -          |          -          |
|                 -                  |         -          |               -               |   CommonXxx.java   |          -          |
|                 -                  |         -          |             enums             |         -          |          -          |
|                 -                  |         -          |               -               |    XxxEnum.java    |          -          |
|                 -                  |         -          |            entity             |         -          |          -          |
|                 -                  |         -          |               -               |   XxxEntity.java   |          -          |
|                 -                  |         -          |              vo               |         -          |          -          |
|                 -                  |         -          |               -               |     XxxVo.java     |          -          |
|                 -                  |         -          |          controller           |         -          |          -          |
|                 -                  |         -          |               -               | XxxController.java |          -          |
|                 -                  |         -          |            service            |         -          |          -          |
|                 -                  |         -          |               -               |  IXxxService.java  |          -          |
|                 -                  |         -          |            service            |         -          |          -          |
|                 -                  |         -          |               -               |        impl        |          -          |
|                 -                  |         -          |               -               |         -          | XxxServiceImpl.java |
|                 -                  |         -          |            mapper             |         -          |          -          |
|                 -                  |         -          |               -               |  IXxxMapper.java   |          -          |
|                 -                  |         -          |           exception           |         -          |          -          |
|                 -                  |         -          |               -               | XxxException.java  |          -          |
|                 -                  |         -          |             util              |         -          |          -          |
|                 -                  |         -          |               -               |    XxxUtil.java    |          -          |
|                 -                  |         -          | XxxApplication.java -- 启动类 |         -          |          -          |
|  src/main/test -- 存放测试类文件   |         -          |               -               |         -          |          -          |
| src/main/resources -- 存放配置文件 |         -          |               -               |         -          |          -          |
|                 -                  |       mapper       |               -               |         -          |          -          |
|                 -                  |         -          |         XxxMapper.xml         |         -          |          -          |
|                 -                  | static -- 静态文件 |               -               |         -          |          -          |
|                 -                  |         -          |            xxx.js             |         -          |          -          |
|                 -                  |         -          |            xxx.css            |         -          |          -          |
|                 -                  |     tempaltes      |               -               |         -          |          -          |
|                 -                  |         -          |           xxx.html            |         -          |          -          |
|                 -                  |  application.yml   |               -               |         -          |          -          |
|                 -                  |   bootstrap.yml    |               -               |         -          |          -          |
|                 -                  |    logback.xml     |               -               |         -          |          -          |

# 八、开发环境

- 开发环境：JDK 1.8+
- 开发工具：IntelliJ IDEA 2018（安装 Lombok Plugin）
- 构建工具：Maven 3.x
- 代码管理工具：Git / TortoiseGit
- Spring Cloud 版本：Finchley.RELEASE
- Spring Boot 版本：2.0.3.RELEASE
- Tomcat 版本：8
- slf4j 版本：1.7.25
- logback 版本：1.2.3

作者：半碗鱼汤
链接：https://www.jianshu.com/p/c952e8a25958
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。