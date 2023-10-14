[TOC]

## 第一节 ORM介绍

- ORM（Object Relational Mapping，对象关系映射）是为了解决面向对象与 关系数据库存在的互不匹配现象的一种技术。
- ORM通过使用描述对象和数据库之间映射的元数据将程序中的对象自动持久化 到关系数据库中。 
- ORM框架的本质是简化编程中操作数据库的编码。

> 算是连接数据库和程序对象的中间组件

## 第二节MyBatis-plus介绍

- MyBatis是一款优秀的数据持久层ORM框架，被广泛地应用于应用系统。
- MyBatis能够非常灵活地实现动态SQL，可以使用XML或注解来配置和映射原 生信息，能够轻松地将Java的POJO（Plain Ordinary Java Object，普通的 Java对象）与数据库中的表和字段进行映射关联。 
- MyBatis-Plus是一个 MyBatis 的增强工具，在 MyBatis 的基础上做了增强， 简化了开发。

> 实质就是一个ORM的框架

#### - 添加依赖pom.xml

  ```
  <!--        myBatisPlus依赖-->
          <dependency>
              <groupId>com.baomidou</groupId>
              <artifactId>mybatis-plus-boot-starter</artifactId>
              <version>3.4.2</version>
          </dependency>
  
  <!--        mysql驱动依赖-->
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
              <version>5.1.47</version>
          </dependency>
  
  <!--        数据库连接池druid-->
          <dependency>
              <groupId>com.alibaba</groupId>
              <artifactId>druid-spring-boot-starter</artifactId>
              <version>1.1.20</version>
          </dependency>
  ```

  

#### - 全局配置（application.properties)

  ```
  # 配置数据库相关信息
  # 使用我们添加的依赖中的连接池
  spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
  # 指定我们用什么驱动连接数据库
  spring.datasource.driver-class-name=com.mysql.jdbc.Driver
  # 数据库在哪里  3306是本地的数据库地址，miniProject是数据库名字(可修改)
  spring.datasource.url=jdbc:mysql://localhost:3306/miniProject?useSSL=false
  # 数据库的账号
  spring.datasource.username=root
  #数据库的密码
  spring.datasource.password=123456
  # 指定了日志输出的格式
  mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
  ```

  > 记得把# 这种注释全删了，不然报错找不到配置

#### - 添加@MapperScan注解

  ```
  这个是plus的
  ```


#### - 基于MyBatis

  - userController

  ```
  @RestController
  public class UserController {
      @Autowired  // 自动注入UserMapper 实例化的对象
      private UserMapper userMapper;
      @GetMapping("/user")  // RESTful格式
      // 可以直接返回json格式，返回List格式的话，这个过程是自动完成的
      public List query() {
          //这是基于MyBatis的
          //List<User> list = userMapper.find();
          //这是基于MyBatisPlus的
          List<User> list = userMapper.selectList(null);
          System.out.println(list);
          return list;
      }
  
      @PostMapping("/user")
      public String  save(User user) {
          int i = userMapper.insert(user);
          if (i > 0) {
              return "插入成功";
          } else {
              return "插入失败";
          }
      }
  
      @DeleteMapping("/user")
      //localhost:8080/del  也可以看ppt上的，那个也可以(感觉更好)
      public String deleteClass(User user) {
          int i = userMapper.delete(user.getId());
          if (i > 0) {
              return "删除成功";
          } else {
              return "删除失败";
          }
      }
  
      @PutMapping("/user")
      public String update(User user) {
          int i = userMapper.update(user);
          if (i > 0) {
              return "更新成功";
          } else {
              return "更新失败";
          }
      }
  
  }
  ```

  - userMapper

  ```
    @Mapper
    public interface UserMapper extends BaseMapper<User> {
        //// 这是老版myBatis
        //// 查询所有用户
        //@Select("select * from t_user")
        //public List<User> find();
        //
        //增加一个用户
        // 这个因为形参里面是一个User的实例化对象， 所以要用固定语法传入数据， #{data}  data是User的属性如下
        @Insert("insert into t_user values(#{id}, #{username}, #{password}, #{birthday})")
        public int insert(User user);
    
        // 删除单个用户
        @Delete("delete from t_user where id=#{id}")
        int delete(int id);
    
        // 更新单个用户
        @Update("update t_user set username=#{username}, password=#{password}, birthday=#{birthday} where id=#{id}")
        int update(User user);
    }
    
  ```

​    

#### - 基于MyBatisPlus的

  > 可以看视频，就是在单表查询上进行引入注解进行更快的查询，基于MyBatis的UserController也有例子(查询)。