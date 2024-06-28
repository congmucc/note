[TOC]

## java

> 2024：
> https://www.yuque.com/xiaqing-en2ii/re3a5a/wy1susg62s7cois1

### 1 新的插件：lombok

````
import lombok.Data;
// 可以直接生成get，set方法，不用再写get和set了，还有toString()
@Data

// 自动生成无参构造
@NoArgsConstructor

// 自动生成有参构造
@AllArgsConstructor
````



### 2 新的注解：@JsonIgnore

@JsonIgnore  后端向前端发送数据的时候可以忽略属性，例如password

- User

```
    private Integer id;
    private String username;
    @JsonIgnore
    private String password;
    private String nickname;
    private String email;
    private String phone;
    private String address;
```



### 3 private重复的代码写一个私有的方法，然后导入即可



## SpringBoot

### 1. serve（动态修改）

> 可以将post即作为新增也可以作为更新



### 2. 分页查询

- UserController

```
    // 分页查询
    // 接口路径： /user/page?pageNum=1&pageSize=10
    // pageNum 是第几页   pageSize是一页显示几个
    // limit 第一个参数 ： (pageNum - 1) * pageSize   公式推倒看视频
    // 第二个参数：pageSize
    @GetMapping("/page")
    public Map<String, Object> get(@RequestParam Integer pageNum, @RequestParam Integer pageSize) {
        pageNum = (pageNum - 1) * pageSize;
        List<User> data = userMapper.selectPage(pageNum, pageSize);
        Integer total = userMapper.selectTotal();
        Map<String, Object> res = new HashMap<>();
        res.put("data", data);
        res.put("total", total);
        return res;
    }
```

- UserMapper

```
    // 分页查询
    @Select("select * from user limit #{pageNum}, #{pageSize}")
    List<User> selectPage(Integer pageNum, Integer pageSize);

    // 查询总条数
    @Select("select count(*) from user")
    Integer selectTotal();

```





### 3. post更新--可以同时添加更新

> 这个可以同时进行添加和更新

首先再controller中写post就行

- UserController

```

    @Autowired
    private UserService userService;

    // 从数据库中查询全部
    @GetMapping("/get")
    public List<User> get() {
        return userMapper.findAll();
    }

    // 增加
    @PostMapping("/post")
    public String save(@RequestBody User user) {
        // 新增或者更新   这里用userService
        int i = userService.save(user);
        if (i > 0) {
            System.out.println("插入成功");
            return "插入成功";
        } else {
            System.out.println("插入失败");
            return "插入失败";
        }
    }
```



其次在UserServe写入

- UserServe

```
package com.org.service;

import com.org.entity.User;
import com.org.mapper.UserMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    @Autowired
    private UserMapper userMapper;

    public int save(User user) {
        if (user.getId() == null) {   // user 没有id， 则表示新增
            return userMapper.insert(user);
        } else {   // 否则则为更新
            return userMapper.update(user);
        }
    }
}

```





之后在mapper写入连接方式

- Usermapper

```
    // 增加
    @Insert("insert into user(username, password, nickname, email, phone, address) values(#{username}, #{password}, #{nickname}, #{email}, #{phone}, #{address})")
    int insert(User user);

    // 更新
    int update(User user);

```





最后在\src\main\resources\路径下新建一个mapper\User.xml

- User.xml

> 这个可以直接在Mybatis官网上找

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.org.mapper.UserMapper">
    <update id="update">
        update user
<!--        set 里面只要有一项-->
        <set>
            <if test="username != null">
                username = #{username},
            </if>
<!--            <if test="password != null">-->
<!--                password = #{password},-->
<!--            </if>-->
            <if test="nickname != null">
                nickname = #{nickname},
            </if>
            <if test="email != null">
                email = #{email},
            </if>
            <if test="phone != null">
                phone = #{phone},
            </if>
            <if test="address != null">
                address = #{address}
            </if>
        </set>
        where id = #{id}
    </update>
</mapper>
```
> 这里得test中得username是从前端传过来的，






#### 3.1 MybatisPlus方式分页查询，也是关于空字符串与null的问题的（p9）

```
    /**
     *分页查询--mybatis-plus方式
     *
     * @RequestParam(defaultValue = "") 这个是前端不传数据的话把值设为为空字符串
     * 例如localhost:9090/user/page?pageNum=1&pageSize=5&username=a&email=&address= 此时对应的@RequestParam(defaultValue = "") String username,
     * username 就是空字符串
     *
     * @param pageNum
     * @param pageSize
     * @param username
     * @param email
     * @param address
     * @return
     */
    @GetMapping("/page")
    public IPage<User> get(@RequestParam Integer pageNum,
                           @RequestParam Integer pageSize,
                           @RequestParam(defaultValue = "") String username,
                           @RequestParam(defaultValue = "") String email,
                           @RequestParam(defaultValue = "") String address) {
        IPage<User> page = new Page<>(pageNum, pageSize);
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        // 这句话的意思是，如果传过来的数据为空，因为数据库创建的时候是null默认值（可以改），所以说做一下判断如果前端传过来的数据是""的话（就是传过来一个字符串，就查。
        // 例如这个项目：localhost:9090/user/page?pageNum=1&pageSize=5&username=a&email=&address=  email和address都为空，所以说传过来的是一个空字符串
        // 则Strings.isNotEmpty(email)和Strings.isNotEmpty(address)为false，这两段代码都不会执行
        System.out.println(Strings.isNotEmpty(email));
        queryWrapper.like(Strings.isNotEmpty(username),"username", username);
        queryWrapper.like(Strings.isNotEmpty(email),"email", email);   // 上面的不会执行
        queryWrapper.like(Strings.isNotEmpty(address),"address", address);  // 上面的不会执行

        // 或者也可以将数据库创建数据的默认值设为‘’（空字符串），这样也不用Strings.isNotEmpty(address)了
        // 原理是模糊搜索的话，like是含有，都执行，因为默认值是空，所以说不用担心
        // queryWrapper.like("username", username);
        // queryWrapper.like("email" , email);
        // queryWrapper.like("address", address);

        IPage<User> userIPage = userService.page(page, queryWrapper);
        return userIPage;
    }
```



##### 3.1.1 @RequestParam(defaultValue = "") 

```
上面代码中
@RequestParam(defaultValue = "") 这个是前端不传数据的话把值设为为空字符串
例如localhost:9090/user/page?pageNum=1&pageSize=5&username=a&email=&address= 此时对应的@RequestParam(defaultValue = "") String username
中username 就是空字符串


```







### 4.代码生成器

#### 4.1依赖需要两个

```
<!--        代码生成器-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.5.1</version>
        </dependency>
<!--        velocity-->
        <dependency>
            <groupId>org.apache.velocity</groupId>
            <artifactId>velocity</artifactId>
            <version>1.7</version>
        </dependency>
```





#### 4.2 com.org新建一个utils中新建CodeGenerator.java

```
package com.org.utils;

import com.baomidou.mybatisplus.generator.FastAutoGenerator;
import com.baomidou.mybatisplus.generator.config.OutputFile;

import java.util.Collections;

/**
 * mp代码生成器
 * @since 2023-08-02
 */
public class CodeGenerator {
    public static void main(String[] args) {
        generate();
    }

    private static void generate() {
        FastAutoGenerator.create("jdbc:mysql://localhost:3306/Bms?useSSL=false", "root", "123456")
                .globalConfig(builder -> {
                    builder.author("congmu") // 设置作者
                            .enableSwagger() // 开启 swagger 模式
                            .fileOverride() // 覆盖已生成文件
                            .outputDir("D:\\Code\\Project\\SpringBoot+Vue\\BackEndManagementSystem\\src\\main\\java\\"); // 指定输出目录
                })
                .packageConfig(builder -> {
                    builder.parent("com.org") // 设置父包名
                            .moduleName(null) // 设置父包模块名
                            .pathInfo(Collections.singletonMap(OutputFile.mapperXml, "D:\\Code\\Project\\SpringBoot+Vue\\BackEndManagementSystem\\src\\main\\resources\\mapper\\")); // 设置mapperXml生成路径
                })
                .strategyConfig(builder -> {
                    builder.entityBuilder().enableLombok();
//                    builder.mapperBuilder().enableMapperAnnotation().build();
                    builder.controllerBuilder().enableHyphenStyle()  // 开启驼峰转连字符
                            .enableRestStyle();  // 开启生成@RestController 控制器
                    builder.addInclude("user") // 设置需要生成的表名
                            .addTablePrefix("t_", "c_"); // 设置过滤表前缀
                })
                // .templateEngine(new FreemarkerTemplateEngine()) // 使用Freemarker引擎模板，默认的是Velocity引擎模板
                .execute();;
    }
}

```

> 需要注意的坑，这个生成器版本要3.5.1，其他的一些改成自己的就行，按照视频上的和官网上的，注意

```
                    builder.entityBuilder().enableLombok();
//                    builder.mapperBuilder().enableMapperAnnotation().build();
                    builder.controllerBuilder().enableHyphenStyle()  // 开启驼峰转连字符
                            .enableRestStyle();  // 开启生成@RestController 控制器
```

> 这些新增的一定要有，尤其是开启生成@RestController 控制器，最重要



#### 4.3 生成代码逻辑编写，controller.java.vm

之后在idea中的maven仓库里面的mybatis-plus-generator\3.5.1\mybatis-plus-generator-3.5.1.jar!\templates\下找到

controller.java.vm这个文件复制一下，在resources下创建文件夹templates将复制的controller.java.vm拷贝到里面，修改

```
package ${package.Controller};


import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import org.apache.logging.log4j.util.Strings;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import java.util.List;

import ${package.Service}.${table.serviceName};
import ${package.Entity}.${entity};

#if(${restControllerStyle})
import org.springframework.web.bind.annotation.RestController;
#else
import org.springframework.stereotype.Controller;
#end
#if(${superControllerClassPackage})
import ${superControllerClassPackage};
#end

/**
 * <p>
 * $!{table.comment} 前端控制器
 * </p>
 *
 * @author ${author}
 * @since ${date}
 */
#if(${restControllerStyle})
@RestController
#else
@Controller
#end
@RequestMapping("#if(${package.ModuleName})/${package.ModuleName}#end/#if(${controllerMappingHyphenStyle})${controllerMappingHyphen}#else${table.entityPath}#end")
#if(${kotlin})
class ${table.controllerName}#if(${superControllerClass}) : ${superControllerClass}()#end

#else
#if(${superControllerClass})
public class ${table.controllerName} extends ${superControllerClass} {
#else
public class ${table.controllerName} {
#end

    @Autowired
    private ${table.serviceName} ${table.entityPath}Service;

    // 查询所有
    @GetMapping("/get")
    public List<${entity}> get() {
        return ${table.entityPath}Service.list();
    }

    // 新增或者更新
    @PostMapping("/post")
    public Boolean save(@RequestBody ${entity} ${table.entityPath}) {
        return ${table.entityPath}Service.saveOrUpdate(${table.entityPath});
    }

    // 根据id删除
    @DeleteMapping("/del/{id}")
    public Boolean delete(@PathVariable Integer id) {
        return ${table.entityPath}Service.removeById(id);
    }
     // 批量删除
     @DeleteMapping("/batch/{ids}")
     public boolean deleteBatch(@PathVariable List<Integer> ids) {
        return ${table.entityPath}Service.removeByIds(ids);
     }

    @GetMapping("/page")
    public Page<${entity}> findPage(@RequestParam Integer pageNum,
                                @RequestParam Integer pageSize,
                                @RequestParam(defaultValue = "") String username,
                                @RequestParam(defaultValue = "") String email,
                                @RequestParam(defaultValue = "") String address) {
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        queryWrapper.like(Strings.isNotEmpty(username),"username", username);
        queryWrapper.like(Strings.isNotEmpty(email),"email", email);
        queryWrapper.like(Strings.isNotEmpty(address),"address", address);
        // 倒序
        queryWrapper.orderByDesc("id");
        return ${table.entityPath}Service.page(new Page<>(pageNum, pageSize), queryWrapper);
    }


}

#end
```



> import ${package.Service}.${table.serviceName};
> import ${package.Entity}.${entity};
>
> 这两段代码的意思是导入service模板
>
> ${table.serviceName}  是Service
>
> ${table.entityPath}这个是user实体
>
> ${entity}这个是类User     List<${entity}>



> 总结，以vm结尾的都可以修改 上面的就userController



### 5 封装一个错误码

#### 5.1 类

- 错误码类Result.java

```
package com.org.common;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;


/**
 * 结构统一包装类状态码，用于给前端发送数据
 *
 * code 状态码  200成功， 500出错
 * msg 返回的信息
 * data 后台携带的数据
 *
 */

@Data
// 无参构造
@NoArgsConstructor
// 有参构造
@AllArgsConstructor

public class Result {

    private String code;
    private String msg;
    private Object data;

    // 成功的无消息返回
    public static Result success() {
        return new Result(Constants.CODE_200, "", null);
    }
    // 成功的有消息返回
    public static Result success(Object data) {
        return new Result(Constants.CODE_200, "", data);
    }

    // 错误返回，返回一个错误信息，和错误码
    public static Result error(String code, String msg) {
        return new Result(code, msg, null);
    }
    public static Result error() {
        return new Result(Constants.CODE_500, "系统错误", null);
    }
}

```



#### 5.2 码接口

```
package com.org.common;

public interface Constants {

    String CODE_200 = "200";  // 成功
    String CODE_401 = "401";  // 权限不足
    String CODE_400 = "400";  // 参数错误
    String CODE_500 = "500";  // 系统错误
    String CODE_600 = "600";  // 其他业务异常

}

```

> 这个用于实现



### 6 关于自定义异常(封装异常)

> 这个比较复杂, 异常这点的javase中其实要再看看



#### 6.1 异常捕获

- GlobalExceptionHandler.java

```
package com.org.exception;

import com.org.common.Result;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;

@ControllerAdvice
public class GlobalExceptionHandler {
    /**
     * @ExceptionHandler相当于controller的@RequestMapping
     * 如果抛出的的是ServiceException，则调用该方法
     * @param se 业务异常
     * @return
     */
    @ExceptionHandler(ServiceException.class)
    @ResponseBody
    public Result handle(ServiceException se){
        return Result.error(se.getCode(), se.getMessage());
    }
}

```

> 这个是一个总的异常类, 用于捕获异常使用@ExceptionHandler(ServiceException.class)
> @ResponseBody



#### 6.2 详细的一异常

- ServiceException.java

```
package com.org.exception;

import lombok.Data;

/**
 * RuntimeException是系统运行中的异常
 * 自定义异常
 */
@Data
public class ServiceException extends RuntimeException{
    private String code;

    public ServiceException(String code, String msg) {
        super(msg);
        this.code = code;
    }
}

```

> 这是异常的真正实现





#### 6.3 使用方法

```
        if (one != null) {
            // 从 one的数据copy到userDTO   此时one里面是获取的后端所有数据， 一对一复制，将one（User属性）的数据username，password，nickname，avatar赋值到userDTO
            BeanUtil.copyProperties(one, userDTO, true);
            return userDTO;
        } else {
            throw new ServiceException(Constants.CODE_600, "用户名或密码错误");
        }
    }
```

> one是一个注册的表单传过来的数据在数据库中是否有,等于null说明有, 然后throw new ServiceException(Constants.CODE_600, "用户名或密码错误");爆出异常,这里需要打印一下才可以显示, 这个进入到ServiceException的实现类中了, 然后一个有参构造返回数据,让GlobalExceptionHandler类捕获了,可以看出,此时函数中是直接返回一个Result的错误码,错误信息就是ServiceException的有参构造然后fan



### 7 JWT

#### 7.1 后端：

##### 7.1.1 依赖

```
        <!-- java-jwt -->
        <dependency>
            <groupId>com.auth0</groupId>
            <artifactId>java-jwt</artifactId>
            <version>4.4.0</version>
        </dependency>
```

##### 7.1.2 生成token

> 在包utils中设置一个TokenUtils.java类这是通过id和密码设定的

````
package com.org.utils;


import com.auth0.jwt.JWT;
import com.auth0.jwt.algorithms.Algorithm;
import com.sky.springbootdemo.jwt.entity.User;
import org.springframework.stereotype.Service;
 

import java.util.Date;
@Component
public class TokenUtils {

    private static IUserService staticUserService;
    @Resource
    private IUserService userService;

    @PostConstruct
    public void setUserService() {
        staticUserService = userService;
    }

    /**
     * 生成token
     * @return
     */
    public static String  genToken(String userId, String sign) {
        return JWT.create().withAudience(userId) // 将 user id 保存到 token 里面,作为载荷
                .withExpiresAt(DateUtil.offsetHour(new Date(), 2)) //2小时后token过期
                .sign(Algorithm.HMAC256(sign)); // 以 password 作为 token 的密钥
    }
}

````



使用方法，需要在service中的实现类中的login方法进行获取id和密码

**package** com.org.service.impl.UserServiceImpl.java

```
    @Override
    public UserDTO login(UserDTO userDTO) {
        User one = getUserInfo(userDTO);
        if (one != null) {
            // 从 one的数据copy到userDTO   此时one里面是获取的后端所有数据， 一对一复制，将one（User属性）的数据username，password，nickname，avatar赋值到userDTO
            BeanUtil.copyProperties(one, userDTO, true);
            // 设置token
            String token = TokenUtils.genToken(one.getId().toString(), one.getPassword());
            userDTO.setToken(token);
            return userDTO;
        } else {
            throw new ServiceException(Constants.CODE_600, "用户名或密码错误");
        }
    }
```





##### 7.1.3 拦截器拦截token（定义+注册）

###### 7.1.3.1 定义

> 在包interceptor中创建一个JWtInterceptor.java类             注册会使用定义

```
package com.org.config.interceptor;

import cn.hutool.core.util.StrUtil;
import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTVerifier;
import com.auth0.jwt.algorithms.Algorithm;
import com.auth0.jwt.exceptions.JWTDecodeException;
import com.auth0.jwt.exceptions.JWTVerificationException;
import com.org.common.Constants;
import com.org.entity.User;
import com.org.exception.ServiceException;
import com.org.service.IUserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 拦截器，验证token信息
 */

public class JwtInterceptor implements HandlerInterceptor {
    @Autowired
    private IUserService userService;
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        String token = request.getHeader("token");
        // 如果不是映射到方法直接通过
        if(!(handler instanceof HandlerMethod)){
            return true;
        }
        // 执行认证
        if (StrUtil.isBlank(token)) {
            throw new ServiceException(Constants.CODE_401,"无token，请从新登录");
        }
        // 获取 token 中的 user id
        String userId;
        try {
            userId = JWT.decode(token).getAudience().get(0);
        } catch (JWTDecodeException j) {
            throw new ServiceException(Constants.CODE_401, "token验证失败");
        }
        // 根据token中的userid查询数据库，
        User user = userService.getById(userId);
        if (user == null) {
            throw new ServiceException(Constants.CODE_401, "用户不存在请重新登录");
        }
        // 验证 token
        JWTVerifier jwtVerifier = JWT.require(Algorithm.HMAC256(user.getPassword())).build();
        try {
            jwtVerifier.verify(token);
        } catch (JWTVerificationException e) {
            throw new ServiceException(Constants.CODE_401, "token验证失败，请重新登陆");
        }


        return true;
    }
}

```



###### 7.1.3.2 注册

使用方法，需要在config中设置一个IntercptorConfig.java继承WebMvcConfigurer接口，并且实现addInterceptors方法。

```
package com.org.config;

import com.org.config.interceptor.JwtInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class IntercptorConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(jwtInterceptor())
                .addPathPatterns("/**")    // 拦截所有请求，通过判断是否有 @LoginRequired 注解 决定是否需要登录
                // 这是不需要拦截的，也就是白名单
                .excludePathPatterns("/user/login", "/user/register", "/**/export", "/**/import");
    }
    @Bean
    public JwtInterceptor jwtInterceptor() {
        return new JwtInterceptor();
    }
}

```



#### 7.2 前端

> 前端jwt这个是在axios封装request.js中设置的



##### 7.2.1 请求token

```
// request 拦截器
// 可以自请求发送前对请求做一些处理
// 比如统一加token，对请求参数统一加密
request.interceptors.request.use(config => {
    config.headers['Content-Type'] = 'application/json;charset=utf-8';
    let user = localStorage.getItem('user') ? JSON.parse(localStorage.getItem('user')) : {}
    if (user) {
        config.headers['token'] = user.token;  // 设置请求头
    }
    return config
}, error => {
    return Promise.reject(error)
});

```

> let user = localStorage.getItem('user') ? JSON.parse(localStorage.getItem('user')) : {}
>     if (user) {
>         config.headers['token'] = user.token;  // 设置请求头
>     }
>
> 这是代码，从后端返回token，放在localStorage中，然后用的时候可以进行查询



##### 7.2.2 拦截token

```
// response 拦截器
// 可以在接口响应后统一处理结果
request.interceptors.response.use(
    response => {
        let res = response.data;
        // 如果是返回的文件
        if (response.config.responseType === 'blob') {
            return res
        }
        // 兼容服务端返回的字符串数据
        if (typeof res === 'string') {
            res = res ? JSON.parse(res) : res
        }
        // 当权限验证不通过的时候给出提示
        if (res.code === '401') {
            ElementUI.Message({
                message: res.msg,
                type: 'error'
            })
        }
        return res;
    },
    error => {
        console.log('err' + error) // for debug
        return Promise.reject(error)
    }
)
```

> if (res.code === '401') {
>             ElementUI.Message({
>                 message: res.msg,
>                 type: 'error'
>             })
>         }
>
> 这个是代码
>
> 在失败的时候进行提示。



## vue

### 1 使用vue-router

#### 1.1 cli进行选择

#### 1.2 一些用法 

```
{

  path: '/login',

  name: 'Login',

  component: () => import('../views/Login.vue')

 },
```



##### 1.2.1 这串代码中component这个写法可以少了导入，方便

##### 1.2.2 声明式导航和编程时导航

| 声明式                   | 编程式             |
| :----------------------- | ------------------ |
| `<router-link:to="···">` | `router.push(···)` |

  声明式用在标签中作为html中,编程式用在js中和一些绑定的属性

例如:

声明式:

```
<router-link to="/login">个人信息</router-link>
```

> 这个只能在template中使用

编程式:

```
<span style="text-decoration:none" @click="logout">退出</span>

//logout中
this.$router.push("/login")

<--或者下面的-->
<span style="text-decoration:none" @click="$router.push('/login')">退出</span>
```

> 这两个都是跳转到login, 一样的写法    js中和html(也算)都可



### 2 封装axios

#### 2.1 main.js中全局

```
import request from '@/utils/request';



// 导入element-ui插件
Vue.use(ElementUI, {size: "small"});
Vue.prototype.request = request

```



#### 2.2 封装

在包utils中创建一个request.js, 这个就是axios封装了

```
import axios from 'axios'
import ElementUI from 'element-ui';

const request = axios.create({
    baseURL: 'http://localhost:9090',
    timeout: 5000
})

// request 拦截器
// 可以自请求发送前对请求做一些处理
// 比如统一加token，对请求参数统一加密
request.interceptors.request.use(config => {
    config.headers['Content-Type'] = 'application/json;charset=utf-8';
    let user = localStorage.getItem('user') ? JSON.parse(localStorage.getItem('user')) : {}
    if (user) {
        config.headers['token'] = user.token;  // 设置请求头
    }
    return config
}, error => {
    return Promise.reject(error)
});

// response 拦截器
// 可以在接口响应后统一处理结果
request.interceptors.response.use(
    response => {
        let res = response.data;
        // 如果是返回的文件
        if (response.config.responseType === 'blob') {
            return res
        }
        // 兼容服务端返回的字符串数据
        if (typeof res === 'string') {
            res = res ? JSON.parse(res) : res
        }
        // 当权限验证不通过的时候给出提示
        if (res.code === '401') {
            ElementUI.Message({
                message: res.msg,
                type: 'error'
            })
        }
        return res;
    },
    error => {
        console.log('err' + error) // for debug
        return Promise.reject(error)
    }
)


export default request


```

#### 2.3 使用

```
this.request.get(`/user/username/${this.user.username}`).then(res => {
            if (res.code === '200') {
                this.form = res.data
            }
            console.log(res.data)
            console.log("form: "+this.form)
        })
```

这里如果then多了可以使用promise, 防止回调地狱



## Mysql

### 建表

最好设置成空字符串 即EMPTY STRING，防止搜的时候搜不到