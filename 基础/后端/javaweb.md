## 1 Maven高级  

### 1.1 分模块设计与开发

通过new Module创建Maven项目





### 1.2 继承关系

> 文件格式为BackEndManagementSystem为父工程



#### 1.2.1 创建maven模块 parent，该工程为父工程，设置打包方式pom(默认jar)

```

    <groupId>org.example</groupId>
    <artifactId>BackEndManagementSystem</artifactId>
    <version>1.0-SNAPSHOT</version>
    
     
    <packaging>pom</packaging>


    <!--    父工程-->
    <parent>
        <groupId>o rg.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.13</version>
        <!-- 父工程的相对路径 -->
        <relativePath/>
    </parent>
```

> 对于相对路径是，对于父工程，直接这样写就行
>
> 子工程需要写父工程的pom.xml路径



#### 1.2.2 在子工程的pom.xml文件中，配置继承关系。

```
<parent>
    <groupId>org.example</groupId>
    <artifactId>BackEndManagementSystem</artifactId>
    <version>1.0-SNAPSHOT</version>
    <relativePath>../pom.xml</relativePath>
</parent>
```

> 这是子工程导入pom文件
>
> 注意： 
>
> 子工程不需要配置id了，如下不需配置：
>
> ```
> <groupId>org.example</groupId>
> ```





#### 1.2.3 在父工程中配置各个工程共有的依赖(子工程会自动继承父工程的依赖)



父工程配置：

```
<modules>
    <module>bems-server</module>
    <module>bems-pojos</module>
    <module>bems-utils</module>
</modules>
```





### 1.3 版本锁定

> 在父工程中可以加入dependencyManagement标签用作版本管理，其中仅仅对子工程版本号进行控制，如果子工程想要导入，需要自己导入相应依赖



- <dependencyManagement>和<dependencies>的区别

> <dependencies>是直接依赖,在父工程配置了依赖,子工程会直接继承下来
> <dependencyManagement> 是统一管理依赖版本,不会直接依赖，还需要在子工程中引入所需依赖(无需指定版本)



- 父工程中

```
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.5.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.velocity</groupId>
            <artifactId>velocity</artifactId>
            <version>1.7</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

> 这仅仅是个例子， 子工程如果导入这两个依赖，版本会切换这两个依赖，除此之外不导入的话**不会**被子工程继承，要跟dependencies这个标签分开。





- 子工程中



```
<properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

    <mybatis-plus-generator.version>3.5.2</mybatis-plus-generator.version>
    <velocity.version>1.7</velocity.version>
</properties>


<dependencies>
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-generator</artifactId>
        <version>${mybatis-plus-generator.version}</version>
    </dependency>
    <dependency>
        <groupId>org.apache.velocity</groupId>
        <artifactId>velocity</artifactId>
        <version>${velocity.version}</version>
    </dependency>
</dependencies>
```

> 导入两个依赖， 同时将version交给properties进行集中处理



### 1.3 聚合



- 聚合：将多个模块组织成一个整体，同时进行项目的构建。 
- 聚合工程：一个不具有业务功能的“空”工程（有且仅有一个pom文件） 【PS：一般来说，继承关 系中的父工程与聚合关系中的聚合工程是同一个】 

- 作用：快速构建项目（无需根据依赖关系手动构建，直接在聚合工程上构建即可）

在maven中，我们可以在聚合工程中通过  设置当前聚合工程所包含的子模块的名称。我 们可以在 tlias-parent中，添加如下配置，来指定当前聚合工程，需要聚合的模块：

  ```
  <!--聚合其他模块-->
  <modules>
      <module>../tlias-pojo</module>
      <module>../tlias-utils</module>
      <module>../tlias-web-management</module>
  </modules>
  ```

那此时，我们要进行编译、打包、安装操作，就无需在每一个模块上操作了。只需要在聚合工程上，统 一进行操作就可以了。



### 1.4 继承与聚合对比 

- 作用

  - 聚合用于快速构建项目 继承用于简化依赖配置、

  - 统一管理依赖 

- 相同点： 

  - 聚合与继承的pom.xml文件打包方式均为pom，通常将两种关系制作到同一个pom文件中 
  - 聚合与继承均属于设计型模块，并无实际的模块内容 

  

- 不同点： 

  - 聚合是在聚合工程中配置关系，聚合可以感知到参与聚合的模块有哪些
  - 继承是在子模块中配置关系，父模块无法感知哪些子模块继承了自己