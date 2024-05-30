                                                      Java入职必知必会之Redis的基本使用



1、redis的使用

   * 新公司新项目，从零到一，没有人用redis，项目中第一次使用redis

       配置redis信息（主要就是redis的地址、端口、密码）

       引入redisTemplate依赖

     ```
     
             <dependency>
                 <groupId>org.springframework.boot</groupId>
                 <artifactId>spring-boot-starter-data-redis</artifactId>
             </dependency>
     ```

       写redis的工具类

     

       使用redis的工具类使用redis

     

   * 直接可以ctrl shift r 全局搜索redis,参考别人的代码，是如何使用的，照葫芦画瓢（这个技能很重要）

2、项目中使用redis的地方：

​         1、有些图表数据来源的表数据较多，处理过程复杂，导致接口响应慢，不合客户需求，redis

​        2、 自定义注解，需要把校验信息存储到redis中

​        3、一些经常使用的数据 在项目启动的时候就从数据库里加载到redis中

​        4、验证码、token信息加载到redis中

至于缓存一致性，直接先更新，再删除就能满足大部分的场景