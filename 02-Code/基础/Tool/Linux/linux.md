# Centos7 学习笔记

## 1 前置条件

> 背景：
>
>  安装 Vmware 下载 Centos7
>
> 视频： [3 天搞定 Linux，1 天搞定 Shell，清华学神带你通关](https://www.bilibili.com/video/BV1WY4y1H7d3/?p=11&share_source=copy_web&vd_source=a9e0245042931de24eb0a8f018fa0eae)
>
> 文章：[超详细的 CentOS7 的下载安装配置教程*centos7 下载*秃头披风侠.的博客-CSDN 博客](https://blog.csdn.net/m0_51545690/article/details/123238360)
>
> 这里分配的处理器数量：2 个
>
> 每个处理器的内核数量为：4 个
>
> 这里面的**配置分区**选择自动分配
>
> 步骤：
>
> 1. 安装 Linux
> 2. 网卡设置
> 3. 安装 SSH 连接工具

1.  **安装 Linux**

2.  **网卡设置**

    > 修改网络初始化配置，设定网卡在系统启动时 ip 初始化
    >
    > ```
    > cd /              进入根目录
    > cd etc            进入etc目录
    > cd sysconfig      进入sysconfig目录
    > cd network-scipts 进入network-scripts目录
    > vi ifcfg-ens33    编辑ifcfg-ens33文件
    >
    > vi /etc/sysconfig/network-scripts/ifcfg-ens33
    >
    > i进入编辑区
    > 将ONBOOT=no改为yes
    > [esc] :wq保存退出
    > ```
    >
    > 然后进行重启，使用`ip addr`查看 ip 地址

3.  **安装 SSH 连接工具**

    > 背景：
    >
    >  SSH，建立在应用层基础上的安全协议
    >
    > 常用的 SSH 连接工具
    >
    > - putty
    > - secureCRT
    > - xshell
    > - finalshell
    >
    >  通过 SSH 连接工具就可以实现从本地连接到远程的 linux 服务器
    >
    > 步骤：
    >
    >     1. 下载
    >     1. 进行ip地址连接

    1. **下载**

       > [FinalShell 官网 (hostbuf.com)](https://www.hostbuf.com/)

    2. **进行 ip 地址连接**

       > 1. 选择 SSH 连接
       > 2. 主机是 linux 的 ip 地址，端口默认 22
       > 3. 用户名和密码是自己设置的
       > 4. 接收并保存

## 2 linux 常用命令：

![image-20231116204344565](assets/image-20231116204344565-1700138635093-1.png)

### 2.1 Linux 命令初体验

| 序号 | 命令           | 对应英文             |           作用           |
| ---- | -------------- | -------------------- | :----------------------: |
| 1    | ls             | list                 |   查看当前目录下的内容   |
| 2    | pwd            | print work directory |     查看当前所在目录     |
| 3    | cd [目录名]    | change directory     |         切换目录         |
| 4    | touch [文件名] | touch                | 如果文件不存在，新建文件 |
| 5    | mkdir [目录名] | make directory       |         创建目录         |
| 6    | rm [文件名]    | remove               |       删除指定文件       |

![image-20231116205238944](assets/image-20231116205238944.png)

- Linux 命令使用技巧：

  > - Tab 键自动补全
  > - 连续两次 Tab 键，给出操作提示
  > - 使用上下箭头快速调出曾经使用过的命令
  > - 使用 clear 命令或者 Ctrl+[快捷键实现清屏

- **文件目录操作命令**

  ![image-20231116220331581](assets/image-20231116220331581.png)

  ![image-20231116220659431](assets/image-20231116220659431.png)

  ![cat](assets/image-20231116220819679.png)

  ![more](assets/image-20231116220905695.png)

  ![tail](assets/image-20231116220940891.png)

  ![mkdir](assets/image-20231116221029727.png)

  ![rmdir](assets/image-20231116221145181.png)

  ![rm](assets/image-20231116221214570.png)

### 2.2 拷贝移动命令

![cp](assets/image-20231116221335374.png)

![mv](assets/image-20231116221501296.png)

### 2.3 打包压缩命令

> 这里常用命令为：zxvf 解压缩 zcvf 压缩

![tar](assets/image-20231116221546514.png)

### 2.4 文本编辑命令

> 前置：
>
>  Contos 中，安装 vim，yum install vim

![vim](assets/image-20231116221949241.png)

![image-20231116222415943](assets/image-20231116222415943.png)

![image-20231116222447244](assets/image-20231116222447244.png)

![image-20231116222504295](assets/image-20231116222504295.png)

![image-20231116222523455](assets/image-20231116222523455.png)

### 2.5 查找命令

![find](assets/image-20231116223005238.png)

![grep](assets/image-20231116223033784.png)
### 2.6 日志

#### 2.6.1 查看日志

>   `tail -f log | grep "内容"`      查看日志
 >  `ps -ef | grep "进程名字"`          查看正在运行的进程：
   >`rpm -qa|grep mysql`              查询当前系统中安装的名称带mysql的软件

#### 2.6.2 生成日志

- `> | >>`生成日志
```bash
docker logs -f id > test.log
```
>  注意`>`和`>>`的区别
`>`写入，覆盖掉原有的
`>>`继续添加，原来的还有


## 2.7 查看网卡速率

> 首先使用命令`ip addr`获取所有网卡，`eth0`是本机网卡
> 其次使用命令`ethtool  eth0`获取本机网卡情况即可
> 使用命令`cat /etc/sysconfig/network-scripts/ifcfg-eth0`

## 2.8 其他命令

### 2.8.1 查看目录的真实大小

- `du -sh 路径`: 查看目录的真实大小

### 2.8.2 查看磁盘占用率

- `df -h 路径`：查看磁盘占用率，这里是不写路径默认是`/`

- `df -i 路径`: 查看iNode磁盘占用率



## 3 软件安装

### 3.1 软件安装方式

- 二进制发布包安装

  > 软件已经针对具体平台编译打包发布，只要解压，修改配置即可

- rpm 安装

  > 软件已经按照 redhat 的包管理规范进行打包，使用 rpm 命令进行安装，不能自行解决库依赖问题

- yum 安装

  > 一种在线软件安装方式，本 质上还是 rpm 安装，自动下载安装包并安装，安装过程中自动解决库依赖问题
  >
  > ```text
  > yum -y list java*
  > ```
  >
  > > 这个是列出 java 的版本

- 源码编译安装

  > 软件以源码工程的形式发布，需要自己编译打包

### 3.2 安装 jdk

> 操作步骤:
>
> 1. 使用 FinalShell 自带的上传工具将 jdk 的二进制发布包上传到 Linux **jdk-8u171-inux-x64.tar.gz**
>
> 2. 解压安装包，命令为**tar -zxvf jdk-8u171-linux-x64.tar.gz -C /usr/local**
>
> 3. 配置环境变量，使用 vim 命令修改/etc/profile 文件，在文件末尾加入如下配置
>
>    ```
>            JAVA_HOME=/usr/local/jdk1.8.0 171
>            PATH=$JAVA_HOME/bin:$PATH
>    ```
>
> 4. 重新加载 profile 文件，使更改的配置立即生效，命令为**source /etc/profile**
>
> 5. 检查安装是否成功，命令为**java -version**

### 3.3 安装 Tomcat && 防火墙

> 操作步骤:
>
> 1. 安装 Tomcat
> 2. 验证 Tomcat
> 3. 关闭防火墙
> 4. 停止 Tomcat

1. **安装 Tomcat**

   > 1. 使用 FinalShell 自带的上传工具将 Tomcat 的二进制发布包上传到 Linux `apache-tomcat-7.0.57.tar.gz`
   > 2. 解压安装包，命令为`tar -zxvf apache-tomcat-7.0.57.tar.gz -C /usr/local`
   > 3. 进入 Tomcat 的 bin 目录启动服务，命令为`sh startup.sh`或者`./startup.sh`

2. **验证 Tomcat**

   > 验证 Tomcat 启动是否成功，有多种方式:
   >
   > - 查看启动日志
   >   `more /usr/local/apache-tomcat-7.0.57/logs/catalina.out` > `tail -50 /usr/local/apache-tomcat-7.0.57/logs/catalina.out`
   > - 查看进程 `ps -ef | grep tomcat`
   >   注意:
   >   - ps 命令是 linux 下非常强大的进程查看命令，通过 ps-ef 可以查看当前运行的所有进程的详细信息
   >   - “|”在 Linux 中称为管道符，可以将前一个命令的结果输出给后一个命令作为输入
   >   - 使用 ps 命令查看进程时，经常配合管道符和查找命令 grep 一起使用，来查看特定进程

   ![验证tomcat](assets/image-20231116232004755.png)

3. **关闭防火墙**

   > 防火墙操作:
   >
   > **开启/关闭防火墙之后要进行立即生效命令**
   >
   > - 查看防火墙状态(`systemctl status firewalld、firewall-cmd --state`)
   > - 暂时关闭防火墙(`systemctl stop firewalld`)
   > - 永久关闭防火墙(`systemctl disable firewalld`)
   > - 开启防火墙(`systemctl start firewalld`)
   > - 开放指定端口(`firewall-cmd --zone=public --add-port=8080/tcp --permanent`)
   > - 关闭指定端口(`firewall-cmd --zone=public --remove-port=8080/tcp --permanent`)
   > - **立即生效**(`firewall-cmd --reload`)
   > - 查看开放的端口(`firewall-cmd --zone=public --list-ports`)
   > - 查看防火墙状态(`firewall-cmd --list-all`)
   

   > 注意:
   > 1、`systemctl`是管理 Linux 中服务的命令，可以对服务进行启动、停止、重启、查看状态等操作
   > 2、`firewall-cmd`是 Linux 中专门用于控制防火墙的命令
   > 3、为了保证系统安全，服务器的防火墙不建议关闭

   ![防火墙](assets/image-20231116233121923.png)

4. **停止 Tomcat**

   > 停止 Tomcat 服务的方式:
   >
   > - 运行 Tomcat 的 bin 目录中提供的停止服务的脚本文件`shutdown.sh` > `sh shutdown.sh` > `./shutdown.sh`
   > - 结束 Tomcat 进程
   >   查看 Tomcat 进程，获得进程 id `ps -ef | grep tomcat`
   >   执行命令结束进程 `kill -9 进程号`
   >   注意:
   >   kill 命令是 Linux 提供的用于结束进程的命令，-9 表示强制结束

   ![停止Tomcat](assets/image-20231116234236909.png)

### 3.4 安装 MySQL

> 背景：
>
>  把步骤的前两步做了，之后跟着知乎来就行了，其实前两步都不用，但是这个流程是对的
>
>  [CentOS / Linux 安装 MySQL（超简单详细） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/623778183)
>
> 步骤：
>
> 1. 检测当前系统中是否安装 MySQL 数据库
> 1. 卸载已经安装的冲突软件
> 1. 安装 MySQL
> 1. 启动 mysql
> 1. 登录 MySQL 数据库，查阅临时密码
> 1. 登录 MySQL，修改密码，开放访问权限（mysql8.0 和低版本不同，还有 3306 端口开放）

1. **检测当前系统中是否安装 MySQL 数据库**

   > 检测当前系统中是否安装 MySQL 数据库
   >
   > ```
   > rpm -qa                         查询当前系统中安装的所有软件
   > rpm -qa|grep mysql              查询当前系统中安装的名称带mysql的软件
   > rpm -qa|grep mariadb            查询当前系统中安装的名称带mariadb的软件
   > ```
   >
   > RPM (Red-Hat Package Manager) RPM 软件包管理器，是红帽 Linux 用于管理和安装软件的工具
   >
   > 注意事项:
   > 如果当前系统中已经安装有 MySOL 数据库，安装将失败。CentOs7 自带 mariadb，与 MySOL 数据库冲突

2. **卸载已经安装的冲突软件**

   ```
   rpm -e --nodeps 软件名称         卸载软件
   rpm -e --nodeps mariadb-libs-5.5.60-1.l7 5x86 64
   ```

3. 将 MySQL 安装包上传到 Linux 并解压

   ```
   mkdir /usr/local/mysql
   tar -zxvf mysql-5.7.25-1.el7.x86_64.rpm-bundle.tar.gz -C /usr/local/mysql
   ```

   > 如果是 tar 结尾的用命令：
   >
   > ` tar xvf mysql-8.0.35-1.el7.x86_64.rpm-bundle.tar  -C /usr/local/mysql`
   >
   > 解压之后得到 6 个 rpm 的安装包文件

   > 使用这个来安装，记得选路径：
   >
   > [CentOS / Linux 安装 MySQL（超简单详细） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/623778183)
   >
   > [【MySQL 8 入门之安装篇】CentOS 7 系统离线下安装 MySQL 8 - 掘金 (juejin.cn)](https://juejin.cn/post/7110209311824936991)

4. 启动 mysql

   > ```
   > systemctl status mysqld        查看mysql服务状态
   > systemctl start mysqld         启动mysql服务
   > ```
   >
   > 说明:可以设置开机时启动 mysql 服务，避免每次开机启动 mysql
   >
   > ```
   > systemctl enable mysqld        开机启动mysql服务
   > ```
   >
   > ```
   > netstat -tunlp                 查看已经启动的服务
   > netstat -tunlp|grep mysql
   >
   > ps -ef|grep mysql              查看mysql进程
   > ```

5. 登录 MySQL 数据库，查阅临时密码

   > ```
   > cat /var/log/mysqld.log                 查看文件内容
   > cat /var/log/mysqld.log|grep password 查看文件内容中包含password的行信息
   > ```
   >
   > `[Note] A temporary password is generated for root@localhost: F51Iay,-1aG6`
   > 注意事项
   > 冒号后面的是密码，注意空格

6. 登录 MySQL，修改密码，开放访问权限

   > ```
   > mysgl -uroot -p                           登录mysql(使用临时密码登录)
   > #修改密码
   > set global validate_password_length=4;    设置密码长度最低位数
   > set global validate_password_policy=LOW;  设置密码安全等级低，便于密码可以修改成123456
   > set password = password('123456');          设置密码为123456
   >
   > #开启访问权限
   > grant all on *.* to 'root'@'%' identified by 'root';
   > flush privileges;
   > ```
   >
   > 如果是 Mysql8.0 需要先改密码然后才能设置长度和等级,且语句不同
   >
   > ```
   > 设置密码校验策略为：0（只验证密码长度）
   > set global validate.password_policy=0;
   >
   > 设置密码最低长度=N，例如设置密码最低长度=6，也就是密码最少要设置6个字符及以上
   > set global validate.password_length=6;
   >
   > ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';
   >
   >
   >
   > # 登录
   > mysql -u root -p'密码'
   >
   > # 如果你的数据库是 mysql 8 及以上
   > # 1、进入数据库
   > use mysql
   > # 2、修改user表
   > update user set host='%' where user='root';
   >
   >
   >
   > # 重载授权表
   > FLUSH PRIVILEGES;
   >
   > # 再执行授权语句
   > GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'WITH GRANT OPTION;
   >
   > # 退出
   > exit
   >
   > # 重启
   > systemctl restart mysqld
   > ```
   >
   > 这里需要开放 3306 端口

### 3.5 安装 lrzsz

> 背景：
>
>  这个 lrzsz 是将文件上传到 linux 的一个包 上传命令为`rz`
>
> 操作步骤:
>
> 1. 搜索 lrzsz 安装包，命令为`yum list lrzsz`
>
> 2. 使用 yum 命令在线安装，命令为`yum install lrzsz.x86 64`
>    注意事项
>    Yum(全称为 Yellow dog Updater, Modified)是一个在 Fedora 和 RedHat 以及 Cent0s 中的 Shell 前端软件包管理器。基于
>    RPM 包管理，能够从指定的服务器自动下载 RPM 包并目安装，可以自动处理依赖关系，并且一次安装所有依赖的软件包，无须
>    繁琐地一次次下载、安装

### 3.6 安装 Nginx

[Nginx](../../前端/Nginx/Nginx.md)

## 4 项目部署

### 4.1 手动部署项目

> 步骤:
>
> 1. 在 IDEA 中开发 SpringBoot 项目并打成 jar 包
> 2. 将 jar 包上传到 Linux 服务器
> 3. 启动 SpringBoot 程序
> 4. 检查防火墙，确保 8080 端口对外开放，访问 SpringBoot 项目
> 5. 改为后台运行 SpringBoot 程序，并将日志输出到日志文件
> 6. 停止 SpringBoot 程序

1. **在 IDEA 中开发 SpringBoot 项目并打成 jar 包**

2. **将 jar 包上传到 Linux 服务器**

3. **启动 SpringBoot 程序**

   > 使用命令： `java -jar 文件名` 来启动项目

4. **检查防火墙，确保 8080 端口对外开放，访问 SpringBoot 项目**

   > 这里 jar 包自带 tomcat， 需要将 linux 中的 tomcat 进程关闭
   >
   > 访问的时候使用 linux 的 ip

5. **改为后台运行 SpringBoot 程序，并将日志输出到日志文件**

   > 目前程序运行的问题:
   >
   > - 线上程序不会采用控制台霸屏的形式运行程序，而是将程序在后台运行
   > - 线上程序不会将日志输出到控制台，而是输出到日志文件，方便运维查阅信息
   >
   > nohup 命令: 英文全称 no hang up(不挂起)，用于不挂断地运行指定命令，出终端不会影响程序的运行
   > 语法格式: nohup Command [Arg ...] [&]
   > 参数说明:
   > Command: 要执行的命令
   > Arg : 一些参数，可以指定输出文件
   > & : 让命令在后台运行
   > 举例:
   > `nohup java -jar boot工程.jar  &> hello.log &` 后台运行 java -jar 命令，并将日志输出到 hello.log 文件

6. **停止 SpringBoot 程序**

   > `ps -ef | grep 'java -jar'`

#### 部署的一些话：

1. **安装 redis**

   > 安装 redis 可以看这个[【centos 安装 redis】（超详细）\_centos 安装 redis_AlanDreamer 的博客-CSDN 博客](https://blog.csdn.net/qq_39187538/article/details/126485922)
   >
   > **一定要注意，这里面 2 说的默认安装到了/usr/local/bin/目录**
   >
   > 这个很麻烦，所以说可以自动安装

2. **安装 nginx**

   > ```text
   > worker_processes  1;
   >
   > events {
   >     worker_connections  1024;
   > }
   >
   > http {
   >     include       mime.types;
   >     default_type  application/octet-stream;
   >     sendfile        on;
   >     keepalive_timeout  65;
   >
   >     server {
   >         listen       80;
   >         server_name  localhost;
   > 		charset utf-8;
   >
   > 		location / {
   >             root   /home/ruoyi/projects/dist;
   > 			try_files $uri $uri/ /index.html;
   >             index  index.html index.htm;
   >         }
   >
   > 		location /prod-api/ {
   > 			proxy_set_header Host $http_host;
   > 			proxy_set_header X-Real-IP $remote_addr;
   > 			proxy_set_header REMOTE-HOST $remote_addr;
   > 			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
   > 			proxy_pass http://localhost:8080/;
   > 		}
   >
   >         error_page   500 502 503 504  /50x.html;
   >         location = /50x.html {
   >             root   html;
   >         }
   >     }
   > }
   > ```
   >
   > 1. 修改：
   >    - 15 行中的`server_name  localhost;` localhost 改为自己的公网 ip 即可
   >    - 19 行中的`root   /home/ruoyi/projects/dist;` root 后面的改为自己存放 dist 的 linux 目录
   >
   > 不用直接改 nginx.conf,路径 `/etc/nginx/conf.d` 是 Nginx 的配置文件夹，它通常用于存放额外的配置文件。在默认的 Nginx 主配置文件中，会有如下的语句：
   >
   > ```
   > include /etc/nginx/conf.d/*.conf;
   > ```
   >
   > 这个好像用链接下载才可以，使用 tar 解压好像就不行

3. **总结**

   > 对于这个， 总体是前端使用 nginx 占用 80 端口显示页面，后端使用 tomcat 占用 8080 端口，还有 redis 和 mysql
   >
   > 最主要的还是是否能看懂 nginx 的配置

### 4.2 通过 Shell 脚本自动部署项目

> 步骤:
>
> 1. 在 Linux 中安装 Git
> 2. 在 Linux 中安装 maven
> 3. 编写 Shell 脚本(拉取代码、编译、打包、启动)
> 4. 为用户授予执行 Shell 脚本的权限
> 5. 执行 shell 脚本
