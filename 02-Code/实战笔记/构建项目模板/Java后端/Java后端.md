# 1 初始构建

## 1.1 初始选择

- Catalog: **Internal**
- Archetype: .maven.archetypes:maven-archetype-**quickstart**

## 1.2 创建包与文件



## 1.3 pom导入

```java
    <!-- 父工程 -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.13</version>
    </parent>
```

```
<!-- 依赖 -->
<dependencies>
    <!-- 添加web类路径依赖 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

</dependencies>

<!-- 创建可执行的jar程序 -->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

## 1.4 主函数进行管理

- 在主函数中添加 `@SpringBootApplication` 注释，以及run方法

```java
@SpringBootApplication
public class Main {
    public static void main(String[] args) {
        // Main是函数名，其他不变
        SpringApplication.run(Main.class, args);
        System.out.println("Hello world!");
    }
}
```

# 2 添加常用pom依赖

## 2.1 lombok-Slf4j

```
<!-- lombok -->
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <version>1.18.28</version>
</dependency>
```

## 2.2 json工具

```xml
        <!-- 阿里JSON解析器 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.78</version>
        </dependency>
```

## 2.3 yml提示工具

```
        <!-- yml提示工具 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
```

## 2.4 pageHelper

```
        <!-- pagehelper 分页插件 -->
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper-spring-boot-starter</artifactId>
            <version>1.4.1</version>
        </dependency>

```

## 2.5 junit单元测试

```
    <!-- junit单元测试 -->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
```

## 2.6 commons工具包

```xml
<!-- commons工具包 -->
<dependency>
  <groupId>commons-lang</groupId>
  <artifactId>commons-lang</artifactId>
  <version>2.6</version>
</dependency>
```

> [Apache Commons – Apache Commons](https://commons.apache.org/)

## 2.7 Hutool工具包

```
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>4.5.15</version>
</dependency>
```

> [简介 | Hutool](https://doc.hutool.cn/pages/index/)



# 3 全局配置

##  3.1 跨域问题

- ../config/CorsConfig

```
@Configuration
public class CorsConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                //是否发送Cookie
                .allowCredentials(true)
                //放行哪些原始域
                .allowedOrigins("*")
                .allowedMethods(new String[]{"GET", "POST", "PUT", "DELETE"})
                .allowedHeaders("*")
                .exposedHeaders("*");
    }
}

```



## 3.2 异常处理

> 1. 全局异常处理
> 2. 局部异常处理

### 全局异常处理

- ../exception/GlobalExceptionHandler.java

```
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



### 局部异常处理

../exception/ServiceException.java

```
/**
 * RuntimeException是系统运行中的异常
 * 自定义异常
 */
@Data
public class ServiceException extends RuntimeException{
    // 错误码
    private String code;

    public ServiceException(String code, String msg) {
        super(msg);
        this.code = code;
    }
}
```



使用： 

```
        if (!user.getPassword().equals(password)) {
            throw new ServiceException(Result.CODE_AUTH_ERROR, "用户密码错误");
        }
```

> 这个是抛出，一定要抛出

##  3.3 JWT认证

> 步骤：
>
> [JWT详细讲解(保姆级教程)-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/995894)
>
> 1. 导入pom依赖（这里使用的是jjwt）
> 2. 创建properties属性类
> 3. yml配置
> 4. 封装jwt工具类
> 5. 创建拦截器，验证token
> 6. 创建注册器注册到springboot

1. **导入pom依赖（这里使用的是jjwt）**

       <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
            <version>0.9.1</version>
        </dependency>

2. **创建properties属性类**

   - ../properties/JwtProperties

   ```
   
   import lombok.Data;
   import org.springframework.boot.context.properties.ConfigurationProperties;
   import org.springframework.stereotype.Component;
   @Component
   @ConfigurationProperties(prefix = "jwt")
   @Data
   public class JwtProperties {
   
       /**
        * 生成jwt令牌相关配置
        */
       private String tokenHeader;
       private String secret;
       private long expiration;
   }
   ```

   

3. **yml配置**

   ```
   jwt:
     tokenHeader: token #JWT存储的请求头
     secret: mall-admin-secret #JWT加解密使用的密钥【私钥】
     expiration: 604800 #JWT的过期限时间(60*60*24*7)
   ```

   

4. **封装jwt工具类**

   - ../utils/JwtUtil

   ```
   
   
   import io.jsonwebtoken.Claims;
   import io.jsonwebtoken.Jwts;
   import io.jsonwebtoken.SignatureAlgorithm;
   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.beans.factory.config.YamlPropertiesFactoryBean;
   import org.springframework.core.io.ClassPathResource;
   
   import java.nio.charset.StandardCharsets;
   import java.util.Date;
   import java.util.Map;
   import java.util.Properties;
   
   
   public class JwtUtil {
       @Value("${jwt.secret}")
       private static String secret;
       @Value("${jwt.expiration}")
       private static Long expiration;
   
       static {
           // 从YAML文件中读取配置
           YamlPropertiesFactoryBean yaml = new YamlPropertiesFactoryBean();
           yaml.setResources(new ClassPathResource("application.yml"));
           Properties properties = yaml.getObject();
   
           // 设置静态属性的值
           secret = properties.getProperty("jwt.secret");
           expiration = Long.parseLong(properties.getProperty("jwt.expiration"));
       }
   
       /**
        * 根据负责生成JWT的token
        */
       public static String generateToken(Map<String, Object> claims) {
           return Jwts.builder()
                   .setClaims(claims)
                   .setExpiration(new Date(System.currentTimeMillis()+expiration))
                   .signWith(SignatureAlgorithm.HS512, secret)
                   .compact();
       }
   
   
       /**
        * Token解密
        *
        * @param secret    jwt秘钥 此秘钥一定要保留好在服务端, 不能暴露出去, 否则sign就可以被伪造, 如果对接多个客户端建议改造成多个
        * @param token     加密后的token
        * @return
        */
       public static Claims parseJWT(String secret, String token) {
           // 得到DefaultJwtParser
           Claims claims = Jwts.parser()
                   // 设置签名的秘钥
                   .setSigningKey(secret.getBytes(StandardCharsets.UTF_8))
                   // 设置需要解析的jwt
                   .parseClaimsJws(token).getBody();
           return claims;
       }
   }
   ```

   

5. **创建拦截器，验证token**

   - ../interceptor/JwtInterceptor.java

   ```
   
   /**
    * jwt令牌校验的拦截器
    */
   @Component
   @Slf4j
   public class JwtTokenAdminInterceptor implements HandlerInterceptor {
   
       @Autowired
       private JwtProperties jwtProperties;
   
       /**
        * 校验jwt
        *
        * @param request
        * @param response
        * @param handler
        * @return
        * @throws Exception
        */
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           //判断当前拦截到的是Controller的方法还是其他资源
           if (!(handler instanceof HandlerMethod)) {
               //当前拦截到的不是动态方法，直接放行
               return true;
           }
   
           //1、从请求头中获取令牌
           String token = request.getHeader(jwtProperties.getAdminTokenName());
   
           //2、校验令牌
           try {
               log.info("jwt校验:{}", token);
               Claims claims = JwtUtil.parseJWT(jwtProperties.getAdminSecretKey(), token);
               Long empId = Long.valueOf(claims.get(JwtClaimsConstant.EMP_ID).toString());
               //3、通过，放行
               return true;
           } catch (Exception ex) {
               //4、不通过，响应401状态码
               response.setStatus(401);
               return false;
           }
       }
   }
   ```
   
   

6. **创建注册器注册到springboot**

   - ../config/IntercptorConfig

   ````
   
   
   import com.mun.webspringboot.interceptor.JwtInterceptor;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
   import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
   
   
   /**
    * 注册
    */
   @Configuration
   public class IntercptorConfig implements WebMvcConfigurer {
   
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           registry.addInterceptor(jwtInterceptors())
                   .addPathPatterns("/**")    // 拦截所有请求，通过判断是否有 @LoginRequired 注解 决定是否需要登录
                   .excludePathPatterns("/user/login");
       }
   
       @Bean
       public JwtInterceptor jwtInterceptors() {
           return new JwtInterceptor();
       }
   
   }
   
   ````





## 3.4 统一结果类

- ../common/Result.java

```
package com.example.springboot.common;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;


@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class Result {

    public static final String CODE_SUCCESS = "200";
    public static final String CODE_AUTH_ERROR = "401";
    public static final String CODE_SYS_ERROR = "500";

    private String code;
    private String msg;
    private Object data;


    public static Result success() {
        return new Result(CODE_SUCCESS, "请求成功", null);
    }

    public static Result success(Object data) {
        return new Result(CODE_SUCCESS, "请求成功", data);
    }

    public static Result error(String msg) {
        return new Result(CODE_SYS_ERROR, msg, null);
    }

    public static Result error(String code, String msg) {
        return new Result(code, msg, null);
    }

    public static Result error() {
        return new Result(CODE_SYS_ERROR, "系统错误", null);
    }

}
```



## 3.5 Knife4j

> (1)简介
>
> knife4j是为Java MVC框架集成Swagger生成Api文档的增强解决方案,前身是swagger-bootstrap-ui,取名kni4j是希望它能像一把匕首一样小巧,轻量,并且功能强悍!
>
> gitee地址：https://gitee.com/xiaoym/knife4j
>
> 官方文档：https://doc.xiaominfo.com/
>
> 效果演示：http://knife4j.xiaominfo.com/doc.html

> 1. 导入 knife4j 的maven坐标
> 2. 在配置类中加入 knife4j 相关配置
> 3. 设置静态资源映射，否则接口文档页面无法访问
> 4. 访问测试



1. **导入 knife4j 的maven坐标**

   在pom.xml中添加依赖(**有这个就不要添加swagger依赖了**)

   ```xml
           <!-- swagger3 -->
           <dependency>
               <groupId>com.github.xiaoymin</groupId>
               <artifactId>knife4j-spring-boot-starter</artifactId>
               <version>3.0.2</version>
           </dependency>
   ```

2. **在配置类中加入 knife4j 相关配置**

   ../config/IntercptorConfig.java

   ```java
   /**
        * 通过knife4j生成接口文档
        * @return
   */
       @Bean
       public Docket docket() {
           ApiInfo apiInfo = new ApiInfoBuilder()
                   .title("苍穹外卖项目接口文档")
                   .version("2.0")
                   .description("苍穹外卖项目接口文档")
                   .build();
           Docket docket = new Docket(DocumentationType.SWAGGER_2)
                   .apiInfo(apiInfo)
                   .select()
                   .apis(RequestHandlerSelectors.basePackage("com.sky.controller"))
                   .paths(PathSelectors.any())
                   .build();
           return docket;
       }
   ```

   ```
   package com.heima.common.knife4j;
   
   import com.github.xiaoymin.knife4j.spring.annotations.EnableKnife4j;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.context.annotation.Import;
   import springfox.bean.validators.configuration.BeanValidatorPluginsConfiguration;
   import springfox.documentation.builders.ApiInfoBuilder;
   import springfox.documentation.builders.PathSelectors;
   import springfox.documentation.builders.RequestHandlerSelectors;
   import springfox.documentation.service.ApiInfo;
   import springfox.documentation.spi.DocumentationType;
   import springfox.documentation.spring.web.plugins.Docket;
   import springfox.documentation.swagger2.annotations.EnableSwagger2;
   
   @Configuration
   @EnableSwagger2
   @EnableKnife4j
   @Import(BeanValidatorPluginsConfiguration.class)
   public class SwaggerConfiguration {
   
       @Bean(value = "defaultApi2")
       public Docket defaultApi2() {
           Docket docket=new Docket(DocumentationType.SWAGGER_2)
                   .apiInfo(apiInfo())
                   //分组名称
                   .groupName("1.0")
                   .select()
                   //这里指定Controller扫描包路径
                   .apis(RequestHandlerSelectors.basePackage("com.heima"))
                   .paths(PathSelectors.any())
                   .build();
           return docket;
       }
       private ApiInfo apiInfo() {
           return new ApiInfoBuilder()
                   .title("黑马头条API文档")
                   .description("黑马头条API文档")
                   .version("1.0")
                   .build();
       }
   }
   ```

   > 以上有两个注解需要特别说明，如下表：
   >
   > | 注解              | 说明                                                         |
   > | ----------------- | ------------------------------------------------------------ |
   > | `@EnableSwagger2` | 该注解是Springfox-swagger框架提供的使用Swagger注解，该注解必须加 |
   > | `@EnableKnife4j`  | 该注解是`knife4j`提供的增强注解,Ui提供了例如动态参数、参数过滤、接口排序等增强功能,如果你想使用这些增强功能就必须加该注解，否则可以不用加 |
   >
   > - 添加配置
   >
   > 在Spring.factories中新增配置
   >
   > ```java
   > org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
   >   com.heima.common.swagger.Swagger2Configuration, \
   >   com.heima.common.swagger.SwaggerConfiguration
   > ```
   >
   > - 访问
   >
   > 在浏览器输入地址：`http://host:port/doc.html`

3. **设置静态资源映射，否则接口文档页面无法访问**

   WebMvcConfiguration.java

   ```java
   /**
        * 设置静态资源映射
        * @param registry
   */
   protected void addResourceHandlers(ResourceHandlerRegistry registry) {
           registry.addResourceHandler("/doc.html").addResourceLocations("classpath:/META-INF/resources/");
           registry.addResourceHandler("/webjars/**").addResourceLocations("classpath:/META-INF/resources/webjars/");
   }
   ```

4. **访问测试**

   接口文档访问路径为 http://ip:port/doc.html ---> http://localhost:8080/doc.html

5. **常用注解**

   | **注解**          | **说明**                                               |
   | ----------------- | ------------------------------------------------------ |
   | @Api              | 用在类上，例如Controller，表示对类的说明               |
   | @ApiModel         | 用在类上，例如entity、DTO、VO                          |
   | @ApiModelProperty | 用在属性上，描述属性信息                               |
   | @ApiOperation     | 用在方法上，例如Controller的方法，说明方法的用途、作用 |



## 3.6 Aop实现全局接口请求日志

1. 连接点（JointPoint） 连接点是程序中可能被拦截的方法。在 AOP 中，连接点是指所有被拦截到的方法。连接点包含两个信息：一个是方法的位置信息，另一个是方法的名称。
2. 切点（Pointcut） 切点是一组连接点的集合，是要被拦截的连接点。在 Spring AOP 中，切点采用 AspectJ 的切点表达式进行描述，格式如 @Pointcut("execution(public * com.example.demo.controller.*.*(..))")。
3. 通知（Advice） 通知是指拦截到连接点后要执行的代码，包括 @Before、@AfterReturning、@AfterThrowing、@After 和 @Around 五种类型。
4. 切面（Aspect） 切面是一个包含通知和切点的对象，主要用来维护切点和通知之间的关系。
5. 织入（Weaving） 织入是将切面应用到目标对象来创建新的代理对象的过程。在 Spring AOP 中，织入可以在编译时、类加载时和运行时进行。



1. pom.xml文件添加以下依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
```

2. 新增*WebLog*实体类

```java
@Data
public class WebLog implements Serializable {

    private static final long serialVersionUID = 1L;


    /**
     * 操作用户
     */
    private String username;

    /**
     * 操作用户角色
     */
    private String userRole;

    /**
     * 操作时间
     */
    private Long startTime;

    /**
     * 消耗时间
     */
    private Integer spendTime;

    /**
     * 根路径
     */
    private String basePath;

    /**
     * URI
     */
    private String uri;

    /**
     * URL
     */
    private String url;

    /**
     * 请求类型
     */
    private String method;

    /**
     * IP地址
     */
    private String ip;

    /**
     * 请求参数
     */
    private Object parameter;

    /**
     * 返回数据
     */
    private Object result;

}
```

3. 编写统一日志记录切面

```java
/**
 * 统一日志处理切面
 * @author 刘博
 */
@Aspect
@Component
@Order(1)
@Slf4j
public class WebLogAspect {

    @Pointcut("@annotation(org.springframework.web.bind.annotation.RequestMapping)")
    public void requestLog() {
    }
    @Pointcut("@annotation(org.springframework.web.bind.annotation.GetMapping)")
    public void getLog() {
    }
    @Pointcut("@annotation(org.springframework.web.bind.annotation.PostMapping)")
    public void postLog() {
    }


    // 在方法执行之前进行通知
    @Before("requestLog() || getLog() || postLog()")
    public void before(JoinPoint joinPoint) {
        System.out.println("执行方法 " + joinPoint.getSignature().getName() + " 前置通知");
    }

    // 在方法执行之后进行通知
    @After("requestLog() || getLog() || postLog()")
    public void after(JoinPoint joinPoint) {
        System.out.println("执行方法 " + joinPoint.getSignature().getName() + " 后置通知");
    }

    // 在方法执行之后返回结果时进行通知
    @AfterReturning(returning = "result", pointcut = "requestLog() || getLog() || postLog()")
    public void afterReturning(JoinPoint joinPoint, Object result) {
        System.out.println("执行方法 " + joinPoint.getSignature().getName() + " 返回通知，返回值：" + result);
    }

    // 在方法抛出异常时进行通知
    @AfterThrowing(throwing = "ex", pointcut = "requestLog() || getLog() || postLog()")
    public void afterThrowing(JoinPoint joinPoint, Exception ex) {
        System.err.pzrintln("执行方法 " + joinPoint.getSignature().getName() + " 异常通知，异常：" + ex.getMessage());
    }

    @Around("requestLog() || getLog() || postLog()")
    public Object doAround(ProceedingJoinPoint joinPoint) throws Throwable {
        long startTime = System.currentTimeMillis();
        //获取当前请求对象
        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = attributes.getRequest();
        Object result = joinPoint.proceed();
        try {
            //生成web日志，不影响请求结果
            generatorWebLog(joinPoint, startTime, request, result);
        }catch (Exception e){
            log.error("web请求日志异常",e);
        }
        return result;
    }

    private void generatorWebLog(ProceedingJoinPoint joinPoint, long startTime, HttpServletRequest request, Object result) {
        WebLog webLog = new WebLog();
        long endTime = System.currentTimeMillis();
        String urlStr = request.getRequestURL().toString();
        webLog.setBasePath(StrUtil.removeSuffix(urlStr, URLUtil.url(urlStr).getPath()));
        webLog.setIp(WebUtils.getClientRealIp(request));
        webLog.setMethod(request.getMethod());
        webLog.setParameter(getParameter(joinPoint));
        webLog.setResult(result);
        webLog.setSpendTime((int) (endTime - startTime));
        webLog.setStartTime(startTime);
        webLog.setUri(request.getRequestURI());
        webLog.setUrl(request.getRequestURL().toString());
        webLog.setDescription(getDescription(joinPoint));
        log.info("web请求日志:{}", JSONUtil.parse(webLog));
    }

    /**
     * 根据方法和传入的参数获取请求参数
     */
    private Object getParameter(ProceedingJoinPoint joinPoint) {
        //获取方法签名
        MethodSignature signature =(MethodSignature) joinPoint.getSignature();
        //获取参数名称
        String[] parameterNames = signature.getParameterNames();
        //获取所有参数
        Object[] args = joinPoint.getArgs();
        //请求参数封装
        JSONObject jsonObject = new JSONObject();
        if(parameterNames !=null && parameterNames.length > 0){
            for(int i=0; i<parameterNames.length;i++){
                jsonObject.put(parameterNames[i],args[i]);
            }
        }
        return jsonObject;
    }

    /**
     * 获取方法描述
     */
    private String getDescription(ProceedingJoinPoint joinPoint) {
        //获取方法签名
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        //获取方法
        Method method = signature.getMethod();
        //获取注解对象
        //ApiOperation annotation = method.getAnnotation(ApiOperation.class);
//        if (Objects.isNull(annotation)) {
//            return "";
//        }
//        return annotation.value();
        return "";
    }
}
```





# 4 基本整合

##  4.1 mysql

- pom

  ```java
          <!-- mysql驱动依赖 -->
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
              <version>8.0.14</version>
          </dependency>
  ```

  

- yml

  ```java
  server:
    port: 8080
  spring:
    datasource:
      username: root
      password: 123456
      url: jdbc:mysql://localhost:3306/数据库名?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull&serverTimezone=GMT%2B8
      driver-class-name: com.mysql.cj.jdbc.Driver
  ```

## 4.2 mybatis / plus

- pom

  ```java
      <!-- mybatis -->
      <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>3.0.2</version>
      </dependency>
  ```

- yml

  ```java
  # mybatis配置
  mybatis:
    mapper-locations: classpath:mapper/*.xml    # mapper映射文件位置
    type-aliases-package: com.gouggou.shardingtable.entity    # 实体类所在的位置
    configuration:
      log-impl: org.apache.ibatis.logging.stdout.StdOutImpl   #用于控制台打印sql语句
  ```

  > 日常使用第一个即可

## 4.3 Redis

### 4.3.1 maven坐标

```xml
    <!-- 集成redis依赖  -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-redis</artifactId>
    </dependency>
```



### 4.3.2 配置Redis数据源

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
    host:
    port:
    password:
    database:
```



### 4.3.3 编写配置类，创建RedisTemplate对象

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

