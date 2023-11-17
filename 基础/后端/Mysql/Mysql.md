# MySQL 学习记录

## 1 MySQL读写分离

> 步骤： 
>
> 1. MySQL主从复制，

### 1.1 MySQL主从复制

> 背景： 
>
> MySQL主从复制是一个异步的复制过程，底层是基于Mysgl数据库自带的二进制日志功能。就是一台或多台MySQL数据
> 库(slave，即从库)从另一台MySQL数据库 (master，即主库)进行日志的复制然后再解析日志并应用到自身，最
> 终实现从库的数据和主库的数据保持一致。MySQL主从复制是MySQL数据库自带功能，无需借助第三方工具。
>
> 步骤：
>
> MySQL复制过程分成三步:
>
> - master将改变记录到二进制日志 (binary log)
> - slave将master的binary log拷贝到它的中继日志(relay og)
> - slave重做中继日志中的事件，将改变应用到自己的数据库中