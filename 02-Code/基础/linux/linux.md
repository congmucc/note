[TOC]

# Centos7学习笔记

## 1 前置条件

> 背景： 
>
> ​	安装Vmware下载Centos7
>
> 视频： [3天搞定Linux，1天搞定Shell，清华学神带你通关](https://www.bilibili.com/video/BV1WY4y1H7d3/?p=11&share_source=copy_web&vd_source=a9e0245042931de24eb0a8f018fa0eae)
>
> 文章：[超详细的CentOS7的下载安装配置教程_centos7下载_秃头披风侠.的博客-CSDN博客](https://blog.csdn.net/m0_51545690/article/details/123238360)
>
> 这里分配的处理器数量：2个
>
> 每个处理器的内核数量为：4个
>
> 这里面的**配置分区**选择自动分配
>
> 步骤： 
>
> 1. 安装Linux
> 2. 网卡设置
> 3. 安装SSH连接工具

1. **安装Linux**

2. **网卡设置**

   > 修改网络初始化配置，设定网卡在系统启动时ip初始化
   >
   > ```
   > cd /              进入根目录
   > cd etc            进入etc目录
   > cd sysconfig      进入sysconfig目录
   > cd network-scipts 进入network-scripts目录
   > vi ifcfg-ens33    编辑ifcfg-ens33文件
   > 
   > i进入编辑区
   > 将ONBOOT=no改为yes
   > [esc] :wq保存退出
   > ```
   >
   > 然后进行重启，使用`ip addr`查看ip地址

3. **安装SSH连接工具**

   > 背景： 
   >
   > ​	SSH，建立在应用层基础上的安全协议
   >
   > 常用的SSH连接工具
   >
   > - putty
   > - secureCRT
   > - xshell
   > - finalshell
   >
   > ​    通过SSH连接工具就可以实现从本地连接到远程的linux服务器
   >
   > 步骤： 
   >
   > 	1. 下载
   > 	1. 进行ip地址连接

    1. **下载**

       > [FinalShell官网 (hostbuf.com)](https://www.hostbuf.com/)

    2. **进行ip地址连接**

       > 1. 选择SSH连接
       > 2. 主机是linux的ip地址，端口默认22
       > 3. 用户名和密码是自己设置的
       > 4. 接收并保存



## 2 linux常用命令： 

![image-20231116204344565](image-20231116204344565-1700138635093-1.png)

### 2.1 Linux命令初体验

| 序号 | 命令           | 对应英文             |           作用           |
| ---- | -------------- | -------------------- | :----------------------: |
| 1    | ls             | list                 |   查看当前目录下的内容   |
| 2    | pwd            | print work directory |     查看当前所在目录     |
| 3    | cd [目录名]    | change directory     |         切换目录         |
| 4    | touch [文件名] | touch                | 如果文件不存在，新建文件 |
| 5    | mkdir [目录名] | make directory       |         创建目录         |
| 6    | rm [文件名]    | remove               |       删除指定文件       |

![image-20231116205238944](image-20231116205238944.png)



- Linux命令使用技巧：

  > - Tab键自动补全
  > - 连续两次Tab键，给出操作提示
  > - 使用上下箭头快速调出曾经使用过的命令
  > - 使用clear命令或者Ctrl+[快捷键实现清屏

- **文件目录操作命令**

  ![image-20231116220331581](image-20231116220331581.png)

  ![image-20231116220659431](image-20231116220659431.png)

  ![cat](image-20231116220819679.png)

  ![more](image-20231116220905695.png)

  ![tail](image-20231116220940891.png)

  ![mkdir](image-20231116221029727.png)

  ![rmdir](image-20231116221145181.png)

  ![rm](image-20231116221214570.png)

### 2.2 拷贝移动命令

![cp](image-20231116221335374.png)

![mv](image-20231116221501296.png)

### 2.3 打包压缩命令

>  这里常用命令为：zxvf解压缩    zcvf压缩

![tar](image-20231116221546514.png)

### 2.4 文本编辑命令

> 前置： 
>
> ​	Contos中，安装vim，yum install vim

![vim](image-20231116221949241.png)

![image-20231116222415943](image-20231116222415943.png)

![image-20231116222447244](image-20231116222447244.png)

![image-20231116222504295](image-20231116222504295.png)

![image-20231116222523455](image-20231116222523455.png)

### 2.5 查找命令

![find](image-20231116223005238.png)

![grep](image-20231116223033784.png)

## 3 软件安装

### 3.1 软件安装方式

- 二进制发布包安装

  > 软件已经针对具体平台编译打包发布，只要解压，修改配置即可

- rpm安装

  > 软件已经按照redhat的包管理规范进行打包，使用rpm命令进行安装，不能自行解决库依赖问题

- yum安装

  > 一种在线软件安装方式，本 质上还是rpm安装，自动下载安装包并安装，安装过程中自动解决库依赖问题

- 源码编译安装

  > 软件以源码工程的形式发布，需要自己编译打包

### 3.2 安装jdk

> 操作步骤:
>
> 1. 使用FinalShell自带的上传工具将jdk的二进制发布包上传到Linux  **jdk-8u171-inux-x64.tar.gz**
>
> 2. 解压安装包，命令为**tar -zxvf jdk-8u171-linux-x64.tar.gz -C /usr/local**
>
> 3. 配置环境变量，使用vim命令修改/etc/profile文件，在文件末尾加入如下配置
>
>    ```
>            JAVA_HOME=/usr/local/jdk1.8.0 171
>            PATH=$JAVA_HOME/bin:$PATH
>    ```
>
> 4. 重新加载profile文件，使更改的配置立即生效，命令为**source /etc/profile**
>
> 5. 检查安装是否成功，命令为**java -version**

### 3.3 安装Tomcat

> 操作步骤:
>
> 1. 安装Tomcat
> 2. 验证Tomcat
> 3. 关闭防火墙
> 4. 停止Tomcat

1. **安装Tomcat**

   > 1. 使用FinalShell自带的上传工具将Tomcat的二进制发布包上传到Linux `apache-tomcat-7.0.57.tar.gz`
   > 2. 解压安装包，命令为`tar -zxvf apache-tomcat-7.0.57.tar.gz -C /usr/local`
   > 3. 进入Tomcat的bin目录启动服务，命令为`sh startup.sh`或者`./startup.sh`

2. **验证Tomcat**

   > 验证Tomcat启动是否成功，有多种方式:
   >
   > - 查看启动日志
   >   `more /usr/local/apache-tomcat-7.0.57/logs/catalina.out`
   >   `tail -50 /usr/local/apache-tomcat-7.0.57/logs/catalina.out`
   > - 查看进程 `ps -ef | grep tomcat`
   >   注意:
   >   - ps命令是linux下非常强大的进程查看命令，通过ps-ef可以查看当前运行的所有进程的详细信息
   >   - “|”在Linux中称为管道符，可以将前一个命令的结果输出给后一个命令作为输入
   >   - 使用ps命令查看进程时，经常配合管道符和查找命令 grep 一起使用，来查看特定进程

   ![验证tomcat](image-20231116232004755.png)

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
   >
   > 注意:
   > 1、`systemctl`是管理Linux中服务的命令，可以对服务进行启动、停止、重启、查看状态等操作
   > 2、`firewall-cmd`是Linux中专门用于控制防火墙的命令
   > 3、为了保证系统安全，服务器的防火墙不建议关闭

   ![防火墙](image-20231116233121923.png)

4. **停止Tomcat**

   > 停止Tomcat服务的方式:
   >
   > - 运行Tomcat的bin目录中提供的停止服务的脚本文件`shutdown.sh`
   >   `sh shutdown.sh`
   >   `./shutdown.sh`
   > - 结束Tomcat进程
   >   查看Tomcat进程，获得进程id       `ps -ef | grep tomcat`
   >   执行命令结束进程 `kill -9 进程号`
   >   注意:
   >   kill命令是Linux提供的用于结束进程的命令，-9表示强制结束

   ![停止Tomcat](./assets/image-20231116234236909.png)

### 3.4 安装MySQL

> 背景：
>
> ​	把步骤的前两步做了，之后跟着知乎来就行了，其实前两步都不用，但是这个流程是对的
>
> ​        [CentOS / Linux 安装MySQL（超简单详细） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/623778183)
>
> 步骤： 
>
> 1. 检测当前系统中是否安装MySQL数据库
> 1. 卸载已经安装的冲突软件
> 1. 安装MySQL
> 1. 启动mysql
> 1. 登录MySQL数据库，查阅临时密码
> 1. 登录MySQL，修改密码，开放访问权限（mysql8.0和低版本不同，还有3306端口开放）

1. **检测当前系统中是否安装MySQL数据库**

   > 检测当前系统中是否安装MySQL数据库
   >
   > ```
   > rpm -qa                         查询当前系统中安装的所有软件
   > rpm -qa|grep mysql              查询当前系统中安装的名称带mysql的软件
   > rpm -qa|grep mariadb            查询当前系统中安装的名称带mariadb的软件
   > ```
   >
   >    RPM (Red-Hat Package Manager) RPM软件包管理器，是红帽Linux用于管理和安装软件的工具
   >    
   > 
   >
   > 注意事项:
   >    	如果当前系统中已经安装有MySOL数据库，安装将失败。CentOs7自带mariadb，与MySOL数据库冲突
   
2. **卸载已经安装的冲突软件**

   ```
   rpm -e --nodeps 软件名称         卸载软件
   rpm -e --nodeps mariadb-libs-5.5.60-1.l7 5x86 64
   ```

3. 将MySQL安装包上传到Linux并解压

   ```
   mkdir /usr/local/mysql
   tar -zxvf mysql-5.7.25-1.el7.x86_64.rpm-bundle.tar.gz -C /usr/local/mysql
   ```

   > 如果是tar结尾的用命令：
   >
   > ` tar xvf mysql-8.0.35-1.el7.x86_64.rpm-bundle.tar  -C /usr/local/mysql`
   >
   > 解压之后得到6个rpm的安装包文件

   > 使用这个来安装，记得选路径：
   >
   > [CentOS / Linux 安装MySQL（超简单详细） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/623778183)
   >
   > [【MySQL 8入门之安装篇】CentOS 7系统离线下安装MySQL 8 - 掘金 (juejin.cn)](https://juejin.cn/post/7110209311824936991)

4. 启动mysql

   > ```
   > systemctl status mysqld        查看mysql服务状态
   > systemctl start mysqld         启动mysql服务
   > ```
   >
   >
   > 说明:可以设置开机时启动mysql服务，避免每次开机启动mysql
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

5. 登录MySQL数据库，查阅临时密码

   > ```
   > cat /var/log/mysqld.log                 查看文件内容
   > cat /var/log/mysqld.log|grep password 查看文件内容中包含password的行信息
   > ```
   >
   > `[Note] A temporary password is generated for root@localhost: F51Iay,-1aG6`
   > 注意事项
   > 冒号后面的是密码，注意空格

6. 登录MySQL，修改密码，开放访问权限

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
   > 如果是Mysql8.0 需要先改密码然后才能设置长度和等级,且语句不同
   >
   > 
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
   >  
   >
   > 这里需要开放3306端口

### 3.5 安装lrzsz

> 背景： 
>
> ​	这个lrzsz是将文件上传到linux的一个包   上传命令为`rz`
>
> 操作步骤:
>
> 1. 搜索lrzsz安装包，命令为`yum list lrzsz`
>
> 2. 使用yum命令在线安装，命令为`yum install lrzsz.x86 64`
>    注意事项
>    Yum(全称为 Yellow dog Updater, Modified)是一个在Fedora和RedHat以及Cent0s中的Shell前端软件包管理器。基于
>    RPM包管理，能够从指定的服务器自动下载RPM包并目安装，可以自动处理依赖关系，并且一次安装所有依赖的软件包，无须
>    繁琐地一次次下载、安装

### 3.6 安装Nginx

[Nginx](Nginx.md)

## 4 项目部署 

### 4.1 手动部署项目

> 步骤:
>
> 1. 在IDEA中开发SpringBoot项目并打成jar包
> 2. 将jar包上传到Linux服务器
> 3. 启动SpringBoot程序
> 4. 检查防火墙，确保8080端口对外开放，访问SpringBoot项目
> 5. 改为后台运行SpringBoot程序，并将日志输出到日志文件
> 6. 停止SpringBoot程序

1. **在IDEA中开发SpringBoot项目并打成jar包**

2. **将jar包上传到Linux服务器**

3. **启动SpringBoot程序**

   > 使用命令： `java -jar 文件名`  来启动项目

4. **检查防火墙，确保8080端口对外开放，访问SpringBoot项目**

   > 这里jar包自带tomcat， 需要将linux中的tomcat进程关闭
   >
   > 访问的时候使用linux的ip

5. **改为后台运行SpringBoot程序，并将日志输出到日志文件**

   > 目前程序运行的问题:
   >
   > - 线上程序不会采用控制台霸屏的形式运行程序，而是将程序在后台运行
   > - 线上程序不会将日志输出到控制台，而是输出到日志文件，方便运维查阅信息
   >
   > 
   >
   > nohup 命令: 英文全称 no hang up(不挂起)，用于不挂断地运行指定命令，出终端不会影响程序的运行
   > 语法格式: nohup Command [Arg ...] [&]
   > 参数说明:
   > Command: 要执行的命令
   > Arg : 一些参数，可以指定输出文件
   > & : 让命令在后台运行
   > 举例:
   > `nohup java -jar boot工程.jar  &> hello.log &`          后台运行java -jar命令，并将日志输出到hello.log文件

6. **停止SpringBoot程序**

   > `ps -ef | grep 'java -jar'`



#### 部署的一些话： 

1. **安装redis**

   > 安装redis可以看这个[【centos安装redis】（超详细）_centos 安装redis_AlanDreamer的博客-CSDN博客](https://blog.csdn.net/qq_39187538/article/details/126485922)
   >
   > **一定要注意，这里面2 说的默认安装到了/usr/local/bin/目录**
   >
   > 这个很麻烦，所以说可以自动安装

2. **安装nginx**

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
   >    - 15行中的`server_name  localhost;`     localhost改为自己的公网ip即可
   >    - 19行中的`root   /home/ruoyi/projects/dist;`          root后面的改为自己存放dist的linux目录
   >
   > 不用直接改nginx.conf,路径 `/etc/nginx/conf.d` 是 Nginx 的配置文件夹，它通常用于存放额外的配置文件。在默认的 Nginx 主配置文件中，会有如下的语句：
   >
   > ```
   > include /etc/nginx/conf.d/*.conf;
   > ```
   >
   > 这个好像用链接下载才可以，使用tar解压好像就不行

3. **总结**

   > 对于这个， 总体是前端使用nginx占用80端口显示页面，后端使用tomcat占用8080端口，还有redis和mysql
   >
   > 最主要的还是是否能看懂nginx的配置







### 4.2 通过Shell脚本自动部署项目

> 步骤:
>
> 1. 在Linux中安装Git
> 2. 在Linux中安装maven
> 3. 编写Shell脚本(拉取代码、编译、打包、启动)
> 4. 为用户授予执行Shell脚本的权限
> 5. 执行shell脚本







