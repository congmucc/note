1. [Redis基础](Redis.md)

   > 1. 常用命令
   > 2. string、hash、list、set、zset
   > 3. 整合SpringBoot

2. [Redis高级](Redis高级.md)

   > 1. 
   > 2. Redis的三大删除策略
   >    - 定时删除
   >    - 惰性删除
   >    - 内存淘汰
   > 3. 缓存问题
   >    - 缓存穿透：缓存数据库都没有
   >    - 缓存击透：缓存没有
   >    - 缓存雪崩：缓存中大量数据失效
   > 4. 事务和锁机制
   >    - 事务冲突
   >    - 悲观锁
   >    - 乐观锁
   >    - 分布式锁
   >    - 消息队列

3. [Redis高可用](../..//Java/微服务框架/分布式/分布式缓存/分布式缓存.md)

   > 1. Redis持久化
   >    - RDB：快照
   >    - AOF：命令日志
   > 2. Redis主从
   >    - 读写分离
   >    - 全量同步
   >    - 增量同步
   > 3. Redis哨兵
   >    - 集群监控
   >    - 故障恢复（断开时间长短（偏移量大的）、优先级高）
   > 4. 分片集群
   >    - 添加新节点（类似tcp三次握手）
   >    - 分配存储（类似哈希表，根据能力分配槽位）
   >    - 待机的话是让有自己数据的从节点补上去

### 2.2.1双写一致性问题
> 这个是redis和mysql同步的问题

原理：[后端面试反复问的缓存双写一致性问题_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Jz421e7an/)
解决方案：[【IT老齐062】缓存一致性如何保障？先写库还是先写缓存？聊聊Cache Aside Pattern与延迟双删_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1aF411e7ur/)
> 双写一致，需要  **先更新数据库再删缓存+延时双删**+**删除重试**

### Redis的大Key问题如何解决
> Redis的大Key问题是指单个Key所对应的数据量过大，一般单个key超过10kb就被认为是大Key
1. 分拆大Key  
big list： list1、list2、...listN  
big hash：可以将数据分段存储，比如一个大的key，假设存了1百万的用户数据，可以拆分成200个key，每个key下面存放5000个用户数据  
  
2. 压缩数据  
在存储之前对较大的数据进行压缩，从而减少存储占用空间。
3. 惰性删除  
当更新或删除大Key时使用惰性删除(lazyfree-lazy-expire yes)来避免阻塞整个Redis。  
4. 使用SCAN替代KEYS  
在处理集合时，使用SCAN命令遍历大Key而不是KEYS，避免一次性加载所有数据