[TOC]

## 多表查询



- @Results的方法

  >  @Select("select * from t_user")
  >     @Results({
  >                     @Result(column = "id", property = "id"),
  >                     @Result(column = "id", property = "orders", javaType = List.class,
  >                             many = @Many(select = "org.example.mapper.OrderMapper.selectByUid")
  >                     )
  >     })
  >
  > 
  >
  > 首先，@Result中column = "id"，property = "id"这句话是将数据库中一列的id赋值给User类的id，即column的id代表数据库表里面的字段，后面的代表类字段。
  >
  > 其次，@Result(column = "id", property = "orders", javaType = List.class,
  >                             many = @Many(select = "org.example.mapper.OrderMapper.selectByUid")
  >                     )
  >
  > 前面的一样，但是是将数据库中的id提取出来，然后映射到类里面的orders，javaTupe是让编译器知道property的orders的类型是一个列表，即List.class，many是一个一对多的关系，@Many中的select是一个选择，selectByUid这个是一个对Order类中的外键，即uid进行查询，即
  >
  > > `@Select("select * from t_order where uid = #{uid}")
  > > Lst<Order> selectByUid(int uid);`
  >
  > 这个由uid查询整个Orde中,uid == (User)id 数据库的值.
  >
  > 这个可以理解为外键, 可以一查多



- UserMapper中的

  ```  
    // 查询id为#{id}的用户
      @Select("select * from t_user where id = #{id}")
      User selectById(int id);
  
      // 查询用户及其所有订单
      @Select("select * from t_user")
      @Results({
                      @Result(column = "id", property = "id"),
                      @Result(column = "username", property = "username"),
                      @Result(column = "password", property = "password"),
                      @Result(column = "birthday", property = "birthday"),
                      @Result(column = "id", property = "orders", javaType = List.class,
                              many = @Many(select = "org.example.mapper.OrderMapper.selectByUid")
                      )
      })
      List<User> selectAllUserAndOrders();
  ```

  

- OrderMapper中

  ```
  @Mapper
  public interface OrderMapper {
      @Select("select * from t_order where uid = #{uid}")
      List<Order> selectByUid(int uid);
  
      //查询所有订单，并且查询订单的用户
      @Results({
              @Result(column = "id", property = "id"),
              @Result(column = "num", property = "num"),
              @Result(column = "con", property = "con"),
              @Result(column = "uid", property = "uid", javaType = User.class,
                      one = @One(select = "org.example.mapper.UserMapper.selectById")
              )
      })
      List<Order> selectAllOrdersAndUser();
  
  }
  ```




## 条件查询

```
   /*
    * 条件查询
    * 需要在接口中将orders这个告诉编译器这个不存在，@TableFileld(exist = false),
    * 因为这个数据库表中和类中的并不一样， 必须要进行隐藏
    */

    @GetMapping("/user/find")
    public List<User> findByCond() {
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("username", "佐助");  // 相等
        return userMapper.selectList(queryWrapper);
    }

```



## 分页查询

- 配置config---MyBatisPlusConfig

  ```
  import com.baomidou.mybatisplus.annotation.DbType;
  import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
  import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  
  // 分页查询配置
  @Configuration
  public class MyBatisPlusConfig {
      @Bean
      public MybatisPlusInterceptor paginationInTerceptor() {
          MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
          PaginationInnerInterceptor paginationInnerInterceptor = new PaginationInnerInterceptor(DbType.MYSQL);// 类型
          interceptor.addInnerInterceptor(paginationInnerInterceptor);
          return interceptor;
      }
  }
  
  ```

- UserController

  ```
  /*
      *  分页查询  select * from xx limit 0，10
      *
      **/
  
      @GetMapping("/user/findByPage")
      public IPage findByPage() {
          // 设置起始值及每页条数
          Page<User> page = new Page<>(0, 2);
          IPage iPage = userMapper.selectPage(page, null);
          return iPage;
      }
  ```

  

