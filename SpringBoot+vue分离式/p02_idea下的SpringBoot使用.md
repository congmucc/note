[TOC]

## IDEA版本



### 用maven文件创建并导入依赖：



会有一个pom.xml文件，里面需要导入一些**依赖**

#### 例如Spring Boot父工程（版本自己修改）

```
    <!--    父工程-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.13</version>
    </parent>
```

#### 其次添加依赖

```
    <!-- 依赖  -->
    <dependencies>
        <!--添加web类路径依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

    </dependencies>
```



#### 创建可执行的jar程序

```
    <!--创建可执行的jar程序-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>


```





#### 然后进行热部署   注意要放在  `<dependencies></dependencies>`里面

```
        <!--devtools热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional>
            <scope>true</scope>
        </dependency>
```

注意要在resources里面创建一个application.properties文件，并把启动部署的代码放在里面

```
spring:
devtools:
restart:
#设置开启热部署
enabled: true
#重启目录
additional-paths: src/main/java
exclude: WEB-INF/**
freemarker:
#页面不加载缓存，修改即时生效
cache: false
```

以及修改idea

- File-Settings-Build,Execution,Deployment-Compiler下的    `Build Project automatically`选项打对勾

- 2021之后，compiler.automake.allow.when.app.running这个并不是ctrl + shift + alt +/了，而是在Settings->Advanced Settings里面的Allow auto-make to start even if developed application is currently running，打上对勾就行。





这些做完之后需要下载依赖   快捷键 `ctrl + shift + O`  下载依赖





#### 在主函数中添加 `@SpringBootApplication` 注释，以及run方法

大致如下：

```
@SpringBootApplication
public class Main {
    public static void main(String[] args) {
        // Main是函数名，其他不变
        SpringApplication.run(Main.class, args);
        System.out.println("Hello world!");
    }
}
```









这才可以开始，如果之后有其他依赖也可以进行下载



