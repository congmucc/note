# Redis基础

## 3 Redis p50

### 3.1 启动Redis

```
redis-server.exe redis.windows.conf
```

### 3.2 Redis常用命令 p52

#### 3.2.1 字符串

> 字符串(string): 普通字符串，Redis中最简单的数据类型

Redis 中字符串类型常用命令：

```
SET key value 			        设置指定key的值
GET key                         获取指定key的值
SETEX key seconds value         设置指定key的值，并将 key 的过期时间设为 seconds 秒
SETNX key value                 只有在 key不存在时设置 key 的值
```



#### 3.2.2 哈希

> 哈希(hash):也叫散列，类似于Java中的HashMap结构

Redis hash 是一个string类型的 field 和 value 的映射表，hash特别适合用于存储对象，常用命令：

```
HSET key field value             将哈希表 key 中的字段 field 的值设为 value
HGET key field                   获取存储在哈希表中指定字段的值
HDEL key field                   删除存储在哈希表中的指定字段
HKEYS key                        获取哈希表中所有字段
HVALS key                        获取哈希表中所有值
```





#### 3.2.3 列表

> 列表(list): 按照插入顺序排序，可以有重复元素，类似于Java中的LinkedList

Redis 列表是简单的字符串列表，按照插入顺序排序，常用命令：

```
LPUSH key value1 [value2]        将一个或多个值插入到列表头部
LRANGE key start stop            获取列表指定范围内的元素
RPOP key                         移除并获取列表最后一个元素
LLEN key                         获取列表长度
BRPOP key1 [key2 ] timeout       移出并获取列表的最后一个元素,如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止
```





#### 3.2.4 集合

> 集合(set):无序集合，没有重复元素，类似于Java中的HashSet

Redis set 是string类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据，常用命令：

```
SADD key member1 [member2]            向集合添加一个或多个成员
SMEMBERS key                          返回集合中的所有成员
SCARD key                             获取集合的成员数
SINTER key1 [key2]                    返回给定所有集合的交集
SUNION key1 [key2]                    返回所有给定集合的并集
SREM key member1 [member2]            移除集合中一个或多个成员
```





#### 3.2.5 有序集合

> 有序集合(sorted set /zset): 集合中每个元素关联一个分数(score)，根据分数升序排序，没有重复元素

Redis有序集合是string类型元素的集合，且不允许有重复成员。每个元素都会关联一个double类型的分数。常用命令：

```
ZADD key score1 member1 [score2 member2]     向有序集合添加一个或多个成员
ZRANGE key start stop [WITHSCORES]           通过索引区间返回有序集合中指定区间内的成员
ZINCRBY key increment member                 有序集合中对指定成员的分数加上增量 increment
ZREM key member [member ...]                 移除有序集合中的一个或多个成员
```




#### 3.2.6 通用命令

> Redis的通用命令是不分数据类型的，都可以使用的命令：

```
KEYS pattern 	查找所有符合给定模式( pattern)的 key 
EXISTS key 		检查给定 key 是否存在
TYPE key 		返回 key 所储存的值的类型
DEL key 		该命令用于在 key 存在是删除 key
```





# SpringBoot中使用Redis



### 1.10 Spring Cache p91

#### 1.10.1 介绍

Spring Cache 是一个框架，实现了基于注解的缓存功能，只需要简单地加一个注解，就能实现缓存功能。

Spring Cache 提供了一层抽象，底层可以切换不同的缓存实现，例如：

- EHCache
- Caffeine
- Redis(常用)

**起步依赖：**

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-cache</artifactId>  		            		       	 <version>2.7.3</version> 
</dependency>
```



**常用注解：**

在SpringCache中提供了很多缓存操作的注解，常见的是以下的几个：

| **注解**       | **说明**                                                     |
| -------------- | ------------------------------------------------------------ |
| @EnableCaching | 开启缓存注解功能，通常加在启动类上                           |
| @Cacheable     | 在方法执行前先查询缓存中是否有数据，如果有数据，则直接返回缓存数据；如果没有缓存数据，调用方法并将方法返回值放到缓存中 |
| @CachePut      | 将方法的返回值放到缓存中                                     |
| @CacheEvict    | 将一条或多条数据从缓存中删除                                 |

在spring boot项目中，使用缓存技术只需在项目中导入相关缓存技术的依赖包，并在启动类上使用@EnableCaching开启缓存支持即可。

例如，使用Redis作为缓存技术，只需要导入Spring data Redis的maven坐标即可。

#### 1.10.2 使用

> 这个跟redis联合使用， 只不过将redis改为注解的方式来写了

[3.4 通过redis来将数据存到内存中 p87](#3.4 通过redis来将数据存到内存中 p87)

- @Cacheable例子（第九行）
```java
 /**

   * 条件查询
     *

   * @param categoryId

   * @return
     */
     @GetMapping("/list")
     @ApiOperation("根据分类id查询套餐")
     @Cacheable(cacheNames = "setmealCache",key = "#categoryId") //key: setmealCache::100
     public Result<List<Setmeal>> list(Long categoryId) {
     Setmeal setmeal = new Setmeal();
     setmeal.setCategoryId(categoryId);
     setmeal.setStatus(StatusConstant.ENABLE);

     List<Setmeal> list = setmealService.list(setmeal);
     return Result.success(list);
     }

```
 
- @CacheEvict

```
    @CacheEvict(cacheNames = "setmealCache",key = "#setmealDTO.categoryId")//key: setmealCache::100
    public Result save(@RequestBody SetmealDTO setmealDTO) {
        setmealService.saveWithDish(setmealDTO);
        return Result.success();
    }
    @CacheEvict(cacheNames = "setmealCache",allEntries = true) // allEntries = true这个是删除该setmealCache下的全部
```





### 3.3 SpringBoot Data Redis使用 p58

#### 3.3.1 导入Spring Data Redis 的maven坐标

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```



#### 3.3.2 配置Redis数据源

在application-dev.yml中添加

```yaml
sky:
  redis:
    host: localhost
    port: 6379
    password: 123456
    database: 10
```

**解释说明：**

database:指定使用Redis的哪个数据库，Redis服务启动后默认有16个数据库，编号分别是从0到15。

可以通过修改Redis配置文件来指定数据库的数量。

在application.yml中添加读取application-dev.yml中的相关Redis配置

```yaml
spring:
  profiles:
    active: dev
  redis:
    host: ${sky.redis.host}
    port: ${sky.redis.port}
    password: ${sky.redis.password}
    database: ${sky.redis.database}
```



#### 3.3.3 编写配置类，创建RedisTemplate对象

```
package com.sky.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
@Slf4j
public class RedisConfiguration {

    @Bean
    public RedisTemplate redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate redisTemplate = new RedisTemplate();
        // 设置redis的连接工厂对象
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        // 设置redis key的序列化
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        return redisTemplate;
    }
}
 
```

> 当前配置类不是必须的，因为 Spring Boot 框架会自动装配 RedisTemplate 对象，但是默认的key序列化器为
>
> JdkSerializationRedisSerializer，导致我们存到Redis中后的数据和原始数据有差别，故设置为StringRedisSerializer序列化器。



#### 3.3.4 java通过RedisTemplate对象操作Redis

- 创建测试类

```
package com.sky.test;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.connection.DataType;
import org.springframework.data.redis.core.*;

import java.util.List;
import java.util.Set;
import java.util.concurrent.TimeUnit;

@SpringBootTest
public class SpringDataRedisTest {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    public void testRedisTemplate() {
        System.out.println(redisTemplate);
        //string数据操作
        ValueOperations valueOperations = redisTemplate.opsForValue();
        //hash类型的数据操作
        HashOperations hashOperations = redisTemplate.opsForHash();
        //list类型的数据操作
        ListOperations listOperations = redisTemplate.opsForList();
        //set类型数据操作
        SetOperations setOperations = redisTemplate.opsForSet();
        //zset类型数据操作
        ZSetOperations zSetOperations = redisTemplate.opsForZSet();
    }

    /**
     * 操作字符串
     */
    @Test
    public void testString() {
        // set get setex setnx
        redisTemplate.opsForValue().set("city", "北京");
        String city = (String) redisTemplate.opsForValue().get("city");
        System.out.println("String中的city：" + city);

        redisTemplate.opsForValue().set("code", "1234", 3, TimeUnit.MINUTES);
        redisTemplate.opsForValue().setIfAbsent("lock", "1");
        redisTemplate.opsForValue().setIfAbsent("lock", "2");
    }

    /**
     * 操作哈希类型的数据
     */
    @Test
    public void testHash() {
        //hset hget hdel hkeys hvals
        HashOperations hashOperations = redisTemplate.opsForHash();

        hashOperations.put("100", "name", "tom");
        hashOperations.put("100", "age", "20");

        String name = (String)hashOperations.get("100", "name");
        System.out.println("hash中的name：" + name);

        Set keys = hashOperations.keys("100");
        System.out.println("hash中获取所有的keys： " + keys);

        List values = hashOperations.values("100");
        System.out.println("hash中获取所有的value：" + values);

    }

    /**
     * 操作列表类型的数据
     */
    @Test
    public void testList(){
        //lpush lrange rpop llen
        ListOperations listOperations = redisTemplate.opsForList();

        listOperations.leftPushAll("mylist","a","b","c");
        listOperations.leftPush("mylist","d");

        List mylist = listOperations.range("mylist", 0, -1);
        System.out.println(mylist);

        listOperations.rightPop("mylist");

        Long size = listOperations.size("mylist");
        System.out.println(size);
    }

    /**
     * 操作集合类型的数据
     */
    @Test
    public void testSet(){
        //sadd smembers scard sinter sunion srem
        SetOperations setOperations = redisTemplate.opsForSet();

        setOperations.add("set1","a","b","c","d");
        setOperations.add("set2","a","b","x","y");

        Set members = setOperations.members("set1");
        System.out.println(members);

        Long size = setOperations.size("set1");
        System.out.println(size);

        Set intersect = setOperations.intersect("set1", "set2");
        System.out.println(intersect);

        Set union = setOperations.union("set1", "set2");
        System.out.println(union);

        setOperations.remove("set1","a","b");
    }

    /**
     * 操作有序集合类型的数据
     */
    @Test
    public void testZset(){
        //zadd zrange zincrby zrem
        ZSetOperations zSetOperations = redisTemplate.opsForZSet();

        zSetOperations.add("zset1","a",10);
        zSetOperations.add("zset1","b",12);
        zSetOperations.add("zset1","c",9);

        Set zset1 = zSetOperations.range("zset1", 0, -1);
        System.out.println(zset1);

        zSetOperations.incrementScore("zset1","c",10);

        zSetOperations.remove("zset1","a","b");
    }

    /**
     * 通用命令操作
     */
    @Test
    public void testCommon(){
        //keys exists type del
        Set keys = redisTemplate.keys("*");
        System.out.println(keys);

        Boolean name = redisTemplate.hasKey("name");
        Boolean set1 = redisTemplate.hasKey("set1");

        for (Object key : keys) {
            DataType type = redisTemplate.type(key);
            System.out.println(type.name());
        }

        redisTemplate.delete("mylist");
    }

}

```



# 项目中使用Redis



### 3.4 通过redis来将数据存到内存中 p87

> 如果使用注解转到

[1.10.2 使用](#1.10.2 使用)

#### 3.4.1设置redis

> 实际就是查的时候进行判断
>
> 1. // 查询redis中是否存在菜品数据
> 2. // 如果存在， 直接返回， 无需查询数据库
> 3. // 如果不存在， 查询数据库，并将此查询放在redis中
>
> 就这个三步，如果是普通的话只有查询数据库

```
    /**
     * 根据分类id查询菜品
     *
     * @param categoryId
     * @return
     */
    @GetMapping("/list")
    @ApiOperation("根据分类id查询菜品")
    public Result<List<DishVO>> list(Long categoryId) {

        // 查询redis中是否存在菜品数据
        String key = "dish_" + categoryId;
        List<DishVO> redisList = (List<DishVO>) redisTemplate.opsForValue().get(key);
        if (redisList != null && redisList.size() > 0) {
            // 如果存在， 直接返回， 无需查询数据库
            return Result.success(redisList);
        }

        Dish dish = new Dish();
        dish.setCategoryId(categoryId);
        dish.setStatus(StatusConstant.ENABLE);//查询起售中的菜品

        // 如果不存在， 查询数据库，并将此查询放在redis中
        List<DishVO> list = dishService.listWithFlavor(dish);
        redisTemplate.opsForValue().set(key, list);

        return Result.success(list);
    }

}
```



#### 3.4.2 清除redis缓存数据



> 如果是修改了数据库中的内容，例如菜品价格，这时候需要清除redis中的数据，防止与数据库中的不一致， 以下是例子

为了保证**数据库**和**Redis**中的数据保持一致，修改**管理端接口 DishController** 的相关方法，加入清理缓存逻辑。

需要改造的方法：

- 新增菜品
- 修改菜品
- 批量删除菜品
- 起售、停售菜品

**抽取清理缓存的方法：**

在管理端DishController中添加

```java
	@Autowired
    private RedisTemplate redisTemplate;
	/**
     * 清理缓存数据
     * @param pattern
     */
    private void cleanCache(String pattern){
        Set keys = redisTemplate.keys(pattern);
        redisTemplate.delete(keys);
    }
```

**调用清理缓存的方法，保证数据一致性：**

**1). 新增菜品优化**

```java
	/**
     * 新增菜品
     *
     * @param dishDTO
     * @return
     */
    @PostMapping
    @ApiOperation("新增菜品")
    public Result save(@RequestBody DishDTO dishDTO) {
        log.info("新增菜品：{}", dishDTO);
        dishService.saveWithFlavor(dishDTO);

        //清理缓存数据
        String key = "dish_" + dishDTO.getCategoryId();
        cleanCache(key);
        return Result.success();
    }
```

**2). 菜品批量删除优化**

```java
	/**
     * 菜品批量删除
     *
     * @param ids
     * @return
     */
    @DeleteMapping
    @ApiOperation("菜品批量删除")
    public Result delete(@RequestParam List<Long> ids) {
        log.info("菜品批量删除：{}", ids);
        dishService.deleteBatch(ids);

        //将所有的菜品缓存数据清理掉，所有以dish_开头的key
        cleanCache("dish_*");

        return Result.success();
    }
```

**3). 修改菜品优化**

```java
	/**
     * 修改菜品
     *
     * @param dishDTO
     * @return
     */
    @PutMapping
    @ApiOperation("修改菜品")
    public Result update(@RequestBody DishDTO dishDTO) {
        log.info("修改菜品：{}", dishDTO);
        dishService.updateWithFlavor(dishDTO);

        //将所有的菜品缓存数据清理掉，所有以dish_开头的key
        cleanCache("dish_*");

        return Result.success();
    }
```

**4). 菜品起售停售优化**

```java
	/**
     * 菜品起售停售
     *
     * @param status
     * @param id
     * @return
     */
    @PostMapping("/status/{status}")
    @ApiOperation("菜品起售停售")
    public Result<String> startOrStop(@PathVariable Integer status, Long id) {
        dishService.startOrStop(status, id);

        //将所有的菜品缓存数据清理掉，所有以dish_开头的key
        cleanCache("dish_*");

        return Result.success();
    }
```

