# SpringSecurity

# 1、功能

**身份认证**：·身份认证是验证谁正在访问系统资源，判断用户是否为合法用户。认证用户的常见方式是要求用户输入用户名和密码

**授权**：用户进行身份认证后，系统会控制谁能访问哪些资源，这个过程叫做授权。用户无法访问没有权限的资源。

**防御常见攻击**：

- CSRF

- HTTP Headers
- HTTP Requests



# 2、介绍

2.2.1 SpringSecurity完整流程
 SpringSecurity的原理其实就是一个过滤器链，内部包含了提供各种功能的过滤器。这里我们可以看看入门案例中的过滤器。

![image-20240605144043580](./assets/image-20240605144043580.png)

 图中只展示了核心过滤器，其它的非核心过滤器并没有在图中展示。

UsernamePasswordAuthenticationFilter:负责处理我们在登陆页面填写了用户名密码后的登陆请求。入门案例的认证工作主要有它负责。

ExceptionTranslationFilter： 处理过滤器链中抛出的任何AccessDeniedException和AuthenticationException 。

FilterSecurityInterceptor： 负责权限校验的过滤器。







# 3、认证

## 3.1、流程->初期实现数据库权限管理

1. 添加依赖

2. 需要添加一个配置类，具体去[spring官网](https://docs.spring.io/spring-security/reference/servlet/configuration/java.html)上进行配置类配置。

3. 基于数据库的用户认证流程实现。即实现UserDetailsService

4. 添加用户功能。



1. **添加依赖**

   ```xml
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-oauth2-client</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-security</artifactId>
           </dependency>
   ```

2. **需要添加一个配置类,具体去[spring官网](https://docs.spring.io/spring-security/reference/servlet/configuration/java.html)上进行配置类配置。**

   ```java
   @Configuration
   @EnableWebSecurity
   public class WebSecurityConfig {
   
   	@Bean
   	public UserDetailsService userDetailsService() {
   		InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager();
   		manager.createUser(User.withDefaultPasswordEncoder().username("user").password("password").roles("USER").build());
   		return manager;
   	}
   }
   ```

3. **基于数据库的用户认证流程实现，即实现UserDetailsService**

   [14-自定义配置-基于数据库的用户认证流程实现_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV14b4y1A7Wz?p=14)

   - 程序启动时：
     - 创建DBUserDetailsManager类(其实就是UserDetailsService)，实现接口UserDetailsManager,UserDetailsPasswordService。
     - 在应用程序中初始化这个类的对象

   > 第一步主要是将内存中的配置放入到表中。这里是需要修改一下具体使用的配置，具体可以参照`InMemoryUserDetailsManager`这个方法中返回值等东西
   >
   > 这里面之所以不同是因为一个是根据源码进行修改的，一个是直接给出最终的值。
   >
   > 其中主要是loadUserByUsername重写方式需要参考

   重写详情

   ```java
   package com.atguigu.securitydemo.config;
   
   public class DBUserDetailsManager implements UserDetailsManager, UserDetailsPasswordService {
       
       @Resource
       private UserMapper userMapper;
   
       @Override
       public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
   
           QueryWrapper<User> queryWrapper = new QueryWrapper<>();
           queryWrapper.eq("username", username);
           User user = userMapper.selectOne(queryWrapper);
           if (user == null) {
               throw new UsernameNotFoundException(username);
           } else {
               Collection<GrantedAuthority> authorities = new ArrayList<>();
               return new org.springframework.security.core.userdetails.User(
                       user.getUsername(),
                       user.getPassword(),
                       user.getEnabled(),
                       true, //用户账号是否过期
                       true, //用户凭证是否过期
                       true, //用户是否未被锁定
                       authorities); //权限列表
           }
       }
   
       @Override
       public UserDetails updatePassword(UserDetails user, String newPassword) {
           return null;
       }
   
       @Override
       public void createUser(UserDetails user) {
   
       }
   
       @Override
       public void updateUser(UserDetails user) {
   
       }
   
       @Override
       public void deleteUser(String username) {
   
       }
   
       @Override
       public void changePassword(String oldPassword, String newPassword) {
   
       }
   
       @Override
       public boolean userExists(String username) {
           return false;
       }
   }
   
   ```

   > 这点存入数据库注意：如果要测试，需要往用户表中写入用户数据，并且如果你想让用户的密码是明文存储，需要在密码前加{noop}。例如

   重写后的配置类

   ![image-20240604174153000](./assets/image-20240604174153000.png)

   ```java
   @Bean
   public UserDetailsService userDetailsService() {
       DBUserDetailsManager manager = new DBUserDetailsManager();
       return manager;
   }
   ```

   

   这个DBUserByUsername就是实现继承的方法

   **关于初始化类**：这里面可以不在配置类中添加，直接再DBUserByUsername类中添加Component注解然后删除这个配置类都行，因为security实行的是创建一个类。

4. **添加用户功能**

   添加功能其实没什么说的，增删改查和正常的一样，存入数据库，一般来说是将DBUserByUsername作为一个service，就实现一个UserDetailsService即可







## 其他一些需要明白的

### **关于实现数据库的数据用户认证**：

实现loadUserByUsername这个接口即可：

上面的重写详情，不过一般来说是重新重写一个类来进行实现，和上面的差不多都。

```java
package com.sangeng.domain;

import com.alibaba.fastjson.annotation.JSONField;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import java.util.Collection;
import java.util.List;
import java.util.stream.Collectors;

@Data
@NoArgsConstructor
public class LoginUser implements UserDetails {

    private User user;
        
    //存储权限信息
    private List<String> permissions;
    
    
    public LoginUser(User user,List<String> permissions) {
        this.user = user;
        this.permissions = permissions;
    }


    //存储SpringSecurity所需要的权限信息的集合
    @JSONField(serialize = false)
    private List<GrantedAuthority> authorities;

    @Override
    public  Collection<? extends GrantedAuthority> getAuthorities() {
        if(authorities!=null){
            return authorities;
        }
        //把permissions中字符串类型的权限信息转换成GrantedAuthority对象存入authorities中
        authorities = permissions.stream().
                map(SimpleGrantedAuthority::new)
                .collect(Collectors.toList());
        return authorities;
    }

    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getUserName();
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}
```

使用：

```java
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        LambdaQueryWrapper<User> wrapper = new LambdaQueryWrapper<>();
        wrapper.eq(User::getUserName,username);
        User user = userMapper.selectOne(wrapper);
        if(Objects.isNull(user)){
            throw new RuntimeException("用户名或密码错误");
        }
        List<String> permissionKeyList =  menuMapper.selectPermsByUserId(user.getId());
//        //测试写法
//        List<String> list = new ArrayList<>(Arrays.asList("test"));
        return new LoginUser(user,permissionKeyList);
    }
}
```

> 这个都是实现了一个新的，其实就是仿照着写







### **关于登录页面的修改：**

http添加即可

```java
    protected void configure(HttpSecurity http) throws Exception {
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                // 对于登录接口 允许匿名访问
                .antMatchers("/user/login").anonymous()
                // 除上面外的所有请求全部需要鉴权认证
                .anyRequest().authenticated();

        //把token校验过滤器添加到过滤器链中
        http.addFilterBefore(jwtAuthenticationTokenFilter, UsernamePasswordAuthenticationFilter.class);
    }
```

> 对于登录页面，允许匿名访问




### 认证处理器

- 登录成功后调用：AuthenticationSuccessHandler
- 登录失败后调用：AuthenticationFailureHandler
- 登出成功调用：LogoutSuccessHandler 

#### 成功结果处理

```java
package com.atguigu.securitydemo.config;

public class MyAuthenticationSuccessHandler implements AuthenticationSuccessHandler {
    @Override
    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {

        //获取用户身份信息
        Object principal = authentication.getPrincipal();

        //创建结果对象
        HashMap result = new HashMap();
        result.put("code", 0);
        result.put("message", "登录成功");
        result.put("data", principal);

        //转换成json字符串
        String json = JSON.toJSONString(result);

        //返回响应
        response.setContentType("application/json;charset=UTF-8");
        response.getWriter().println(json);
    }
}
```

WebSecurityConfigurerAdapter配置

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private AuthenticationSuccessHandler successHandler;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin().successHandler(successHandler);

        http.authorizeRequests().anyRequest().authenticated();
    }
}
```





实际上在UsernamePasswordAuthenticationFilter进行登录认证的时候，如果认证失败了是会调用AuthenticationFailureHandler的方法进行认证失败后的处理的。AuthenticationFailureHandler就是登录失败处理器。

#### 失败结果处理

```java
package com.atguigu.securitydemo.config;

public class MyAuthenticationFailureHandler implements AuthenticationFailureHandler {

    @Override
    public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response, AuthenticationException exception) throws IOException, ServletException {

        //获取错误信息
        String localizedMessage = exception.getLocalizedMessage();

        //创建结果对象
        HashMap result = new HashMap();
        result.put("code", -1);
        result.put("message", localizedMessage);

        //转换成json字符串
        String json = JSON.toJSONString(result);

        //返回响应
        response.setContentType("application/json;charset=UTF-8");
        response.getWriter().println(json);
    }
}
```

WebSecurityConfigurerAdapter配置

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private AuthenticationSuccessHandler successHandler;

    @Autowired
    private AuthenticationFailureHandler failureHandler;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()
//                配置认证成功处理器
                .successHandler(successHandler)
//                配置认证失败处理器
                .failureHandler(failureHandler);

        http.authorizeRequests().anyRequest().authenticated();
    }
}
```

#### 登出处理

登出，和前面一样



### 解释：WebSecurityConfigurerAdapter和 HttpSecurity

他其实就是一个让security知道我们重写了一些处理器，就是上面的

http.formLogin().failureHandler(failureHandler);

只要看到httpsecurity就是这个即可

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {


    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                // 对于登录接口 允许匿名访问
                .antMatchers("/user/login").anonymous()
                // 除上面外的所有请求全部需要鉴权认证
                .anyRequest().authenticated();
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }
}

```

> 这上面慢慢添加





# 4、自定义失败处理
我们还希望在认证失败或者是授权失败的情况下也能和我们的接口一样返回相同结构的json，这样可以让前端能对响应进行统一的处理。要实现这个功能我们需要知道SpringSecurity的异常处理机制。

 在SpringSecurity中，如果我们在认证或者授权的过程中出现了异常会被ExceptionTranslationFilter捕获到。在ExceptionTranslationFilter中会去判断是认证失败还是授权失败出现的异常。

 如果是认证过程中出现的异常会被封装成AuthenticationException然后调用AuthenticationEntryPoint对象的方法去进行异常处理。

 如果是授权过程中出现的异常会被封装成AccessDeniedException然后调用AccessDeniedHandler对象的方法去进行异常处理。

 所以如果我们需要自定义异常处理，我们只需要自定义AuthenticationEntryPoint和AccessDeniedHandler然后配置给SpringSecurity即可。



①自定义实现类

```java
@Component
public class AccessDeniedHandlerImpl implements AccessDeniedHandler {
    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException accessDeniedException) throws IOException, ServletException {
        ResponseResult result = new ResponseResult(HttpStatus.FORBIDDEN.value(), "权限不足");
        String json = JSON.toJSONString(result);
        WebUtils.renderString(response,json);

    }
}
```

```java
@Component
public class AuthenticationEntryPointImpl implements AuthenticationEntryPoint {
    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException, ServletException {
        ResponseResult result = new ResponseResult(HttpStatus.UNAUTHORIZED.value(), "认证失败请重新登录");
        String json = JSON.toJSONString(result);
        WebUtils.renderString(response,json);
    }
}
```




②配置给`SpringSecurity`即`WebSecurityConfigurerAdapter`



 先注入对应的处理器

```java
@Autowired
private AuthenticationEntryPoint authenticationEntryPoint;

@Autowired
private AccessDeniedHandler accessDeniedHandler;
```
 然后我们可以使用HttpSecurity对象的方法去配置。

```java
    http.exceptionHandling().authenticationEntryPoint(authenticationEntryPoint).
            accessDeniedHandler(accessDeniedHandler);
```






# 5、授权

## 5.1、授权基本流程和原理
 在SpringSecurity中，会使用默认的FilterSecurityInterceptor来进行权限校验。在FilterSecurityInterceptor中会从SecurityContextHolder获取其中的Authentication，然后获取其中的权限信息。当前用户是否拥有访问当前资源所需的权限。

 所以我们在项目中只需要把当前登录用户的权限信息也存入Authentication。

 然后设置我们的资源所需要的权限即可。

原理就是第一句在拦截器上将信息配置到SecurityContextHolder中，然后在FilterSecurityInterceptor获取



 **授权实现**
**限制访问资源所需权限**
 SpringSecurity为我们提供了基于注解的权限控制方案，这也是我们项目中主要采用的方式。我们可以使用注解去指定访问对应的资源所需的权限。

 但是要使用它我们需要先开启相关配置。这个的意思是开启基于方法授权

```java
@EnableGlobalMethodSecurity(prePostEnabled = true)
```

 然后就可以使用对应的注解。@PreAuthorize

```java
@RestController
public class HelloController {

    @RequestMapping("/hello")
    @PreAuthorize("hasAuthority('test')")
    public String hello(){
        return "hello";
    }
}

```

**封装权限信息**
 我们前面在写UserDetailsServiceImpl的时候说过，在查询出用户后还要获取对应的权限信息，封装到UserDetails中返回。

 我们先直接把权限信息写死封装到UserDetails中进行测试。

 我们之前定义了UserDetails的实现类LoginUser，想要让其能封装权限信息就要对其进行修改。

```java
package com.sangeng.domain;

import com.alibaba.fastjson.annotation.JSONField;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import java.util.Collection;
import java.util.List;
import java.util.stream.Collectors;

@Data
@NoArgsConstructor
public class LoginUser implements UserDetails {

    private User user;
        
    //存储权限信息
    private List<String> permissions;
    
    
    public LoginUser(User user,List<String> permissions) {
        this.user = user;
        this.permissions = permissions;
    }


    //存储SpringSecurity所需要的权限信息的集合
    @JSONField(serialize = false)
    private List<GrantedAuthority> authorities;

    @Override
    public  Collection<? extends GrantedAuthority> getAuthorities() {
        if(authorities!=null){
            return authorities;
        }
        //把permissions中字符串类型的权限信息转换成GrantedAuthority对象存入authorities中
        authorities = permissions.stream().
                map(SimpleGrantedAuthority::new)
                .collect(Collectors.toList());
        return authorities;
    }

    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getUserName();
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}


```



LoginUser修改完后我们就可以在UserDetailsServiceImpl中去把权限信息封装到LoginUser中了。我们写死权限进行测试，后面我们再从数据库中查询权限信息。

```java
package com.sangeng.service.impl;

import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.extension.conditions.query.LambdaQueryChainWrapper;
import com.sangeng.domain.LoginUser;
import com.sangeng.domain.User;
import com.sangeng.mapper.UserMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Objects;

@Service
public class UserDetailsServiceImpl implements UserDetailsService {

    @Autowired
    private UserMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        LambdaQueryWrapper<User> wrapper = new LambdaQueryWrapper<>();
        wrapper.eq(User::getUserName,username);
        User user = userMapper.selectOne(wrapper);
        if(Objects.isNull(user)){
            throw new RuntimeException("用户名或密码错误");
        }
        //TODO 根据用户查询权限信息 添加到LoginUser中
        List<String> list = new ArrayList<>(Arrays.asList("test"));
        return new LoginUser(user,list);
    }
}


```

校验的时候从redis中获取权限

```java
@Component
public class JwtAuthenticationTokenFilter extends OncePerRequestFilter {

    @Autowired
    private RedisCache redisCache;

    @Override
    protected void doFilterInternal(HttpServletRequest resuest, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        //获取token
        String token = resuest.getHeader("token");
        if (!StringUtils.hasLength(token)) {
            //放行
            filterChain.doFilter(resuest, response);
            return;
        }
        //解析token
        String userId;
        try {
            Claims claims = JwtUtil.parseJWT(token);
            userId = claims.getSubject();
        } catch (Exception e) {
            e.printStackTrace();
            throw new RuntimeException("token非法");
        }
        //从redis中获取用户信息
        String redisKey = SysConsts.LOGIN_KEY + ":" + userId;
        LoginUser loginUser = redisCache.getCacheObject(redisKey);
        if (Objects.isNull(loginUser)) {
            throw new RuntimeException("用户未登录");
        }
        //存入SecurityContextHolder
        //TODO 获取权限信息封装到Authentication中
        UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(loginUser, null,loginUser.getAuthorities());
        SecurityContextHolder.getContext().setAuthentication(authenticationToken);

        //放行
        filterChain.doFilter(resuest, response);
    }
}

```

 然后我们可以在UserDetailsServiceImpl中去调用该mapper的方法查询权限信息封装到LoginUser对象中即可。

```java
@Service
public class UserDetailsServiceImpl implements UserDetailsService {

    @Autowired
    private UserMapper userMapper;

    @Autowired
    private MenuMapper menuMapper;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        LambdaQueryWrapper<User> wrapper = new LambdaQueryWrapper<>();
        wrapper.eq(User::getUserName,username);
        User user = userMapper.selectOne(wrapper);
        if(Objects.isNull(user)){
            throw new RuntimeException("用户名或密码错误");
        }
        List<String> permissionKeyList =  menuMapper.selectPermsByUserId(user.getId());
//        //测试写法
//        List<String> list = new ArrayList<>(Arrays.asList("test"));
        return new LoginUser(user,permissionKeyList);
    }
}

```





### **登录失败、权限不足（返回json）：**

实现AccessDeniedHandler即可，这部分配置了当用户已经认证但没有足够的权限访问某个资源时

```java
@Component
public class AccessDeniedHandlerImpl implements AccessDeniedHandler {
    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException accessDeniedException) throws IOException, ServletException {
        ResponseResult result = new ResponseResult(HttpStatus.FORBIDDEN.value(), "权限不足");
        String json = JSON.toJSONString(result);
        WebUtils.renderString(response,json);

    }
}
```



实现AuthenticationEntryPoint即可（这是没有登录访问页面）

```java
@Component
public class AuthenticationEntryPointImpl implements AuthenticationEntryPoint {
    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException, ServletException {
        ResponseResult result = new ResponseResult(HttpStatus.UNAUTHORIZED.value(), "认证失败请重新登录");
        String json = JSON.toJSONString(result);
        WebUtils.renderString(response,json);
    }
}
```



然后我们可以使用HttpSecurity对象的方法去配置。

        http.exceptionHandling().authenticationEntryPoint(authenticationEntryPoint).
                accessDeniedHandler(accessDeniedHandler);

> 其实也是继承这个WebSecurityConfigurerAdapter



###  RBAC权限模型

 RBAC权限模型（Role-Based Access Control）即：基于角色的权限控制。这是目前最常被开发者使用也是相对易用、通用权限模型。

![image-20240605150332093](./assets/image-20240605150332093.png)

在这个设计方案中，用户可以被分配一个或多个角色，而每个角色又可以具有一个或多个权限。通过对用户角色关联和角色权限关联表进行操作，可以实现灵活的权限管理和访问控制。

当用户尝试访问系统资源时，系统可以根据用户的角色和权限决定是否允许访问。这样的设计方案使得权限管理更加简单和可维护，因为只需调整角色和权限的分配即可，而不需要针对每个用户进行单独的设置。









# 其他

## CSRF

 CSRF是指跨站请求伪造（Cross-site request forgery），是web常见的攻击之一。

 https://blog.csdn.net/freeking101/article/details/86537087

 SpringSecurity去防止CSRF攻击的方式就是通过csrf_token。后端会生成一个csrf_token，前端发起请求的时候需要携带这个csrf_token,后端会有过滤器进行校验，如果没有携带或者是伪造的就不允许访问。

 我们可以发现CSRF攻击依靠的是cookie中所携带的认证信息。但是在前后端分离的项目中我们的认证信息其实是token，而token并不是存储中cookie中，并且需要前端代码去把token设置到请求头中才可以，所以CSRF攻击也就不用担心了。





# OAuth 2.0

## 1、介绍

>  主要是为了解决授权和安全相关的问题的，如防止第三方破解密码
>
>  场景是如果用户需要进行pdf打印

![image-20240604104820562](./assets/image-20240604104820562.png)

> 一般来说就是这四种角色，**用户，客户应用，第三方授权服务器，资源服务器（后端）** 
>
> 这里面1是微信登陆，2是redirect。5之前应该有一个下发授权码，然后客户应用使用授权码进行申请token,看下图

![](./assets/OAuth.png)



## 2、四种授权码和选择

OAuth2提供**授权码模式**、**密码模式**、**简化模式**、**客户端模式**等四种授权模式。[41-OAuth2-四种授权模式-授权码模式_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV17M4m1R7WG?p=41)

- **第一种方式：授权码**

  **授权码（authorization code），指的是第三方应用先申请一个授权码，然后再用该码获取令牌。**

  这种方式是最常用，最复杂，也是最安全的，它适用于那些有后端的 Web 应用。授权码通过前端传送，令牌则是储存在后端，而且所有与资源服务器的通信都在后端完成。这样的前后端分离，可以避免令牌泄漏。

  

  ![image-20231220180422742](./assets/image-20231220180422742-1717572807604-1.png)

  - 注册客户应用：客户应用如果想要访问资源服务器需要有凭证，需要在授权服务器上注册客户应用。注册后会**获取到一个ClientID和ClientSecrets**

    这两个是应用访问id和应用访问密钥

  **第二种方式：隐藏式**

  **隐藏式（implicit），也叫简化模式，有些 Web 应用是纯前端应用，没有后端。这时就不能用上面的方式了，必须将令牌储存在前端。**

  RFC 6749 规定了这种方式，允许直接向前端颁发令牌。这种方式没有授权码这个中间步骤，所以称为隐藏式。这种方式把令牌直接传给前端，是很不安全的。因此，只能用于一些安全要求不高的场景，并且令牌的有效期必须非常短，通常就是会话期间（session）有效，浏览器关掉，令牌就失效了。

  ![image-20231220185958063](./assets/image-20231220185958063-1717572822222-3.png)

  ```
  https://a.com/callback#token=ACCESS_TOKEN
  将访问令牌包含在URL锚点中的好处：锚点在HTTP请求中不会发送到服务器，减少了泄漏令牌的风险。
  ```

  

  

  **第三种方式：密码式**

  **密码式（Resource Owner Password Credentials）：如果你高度信任某个应用，RFC 6749 也允许用户把用户名和密码，直接告诉该应用。该应用就使用你的密码，申请令牌。**

  这种方式需要用户给出自己的用户名/密码，显然风险很大，因此只适用于其他授权方式都无法采用的情况，而且必须是用户高度信任的应用。

  ![image-20231220190152888](./assets/image-20231220190152888-1717572832053-5.png)

  **第四种方式：凭证式**

  **凭证式（client credentials）：也叫客户端模式，适用于没有前端的命令行应用，即在命令行下请求令牌。**

  这种方式给出的令牌，是针对第三方应用的，而不是针对用户的，即有可能多个用户共享同一个令牌。

  ![image-20231220185958063](./assets/image-20231220185958063-1717572867573-7.png)

**授权类型选择**

![image-20231223020052999](./assets/image-20231223020052999.png)

> 第一方：企业内部的。
>
> 第二方：甲方或者第三方





## 3、Spring中的实现

### 3.3、配置OAuth客户端属性

application.yml：

```properties
spring:
  security:
    oauth2:
      client:
        registration:
          github:
            client-id: 7807cc3bb1534abce9f2
            client-secret: 008dc141879134433f4db7f62b693c4a5361771b
#            redirectUri: http://localhost:8200/login/oauth2/code/github
```



### 3.4、创建Controller

```java
package com.atguigu.oauthdemo.controller;

@Controller
public class IndexController {

    @GetMapping("/")
    public String index(
            Model model,
            @RegisteredOAuth2AuthorizedClient OAuth2AuthorizedClient authorizedClient,
            @AuthenticationPrincipal OAuth2User oauth2User) {
        model.addAttribute("userName", oauth2User.getName());
        model.addAttribute("clientName", authorizedClient.getClientRegistration().getClientName());
        model.addAttribute("userAttributes", oauth2User.getAttributes());
        return "index";
    }
}
```



### 3.5、创建html页面

resources/templates/index.html

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="https://www.thymeleaf.org" xmlns:sec="https://www.thymeleaf.org/thymeleaf-extras-springsecurity5">
<head>
    <title>Spring Security - OAuth 2.0 Login</title>
    <meta charset="utf-8" />
</head>
<body>
<div style="float: right" th:fragment="logout" sec:authorize="isAuthenticated()">
    <div style="float:left">
        <span style="font-weight:bold">User: </span><span sec:authentication="name"></span>
    </div>
    <div style="float:none">&nbsp;</div>
    <div style="float:right">
        <form action="#" th:action="@{/logout}" method="post">
            <input type="submit" value="Logout" />
        </form>
    </div>
</div>
<h1>OAuth 2.0 Login with Spring Security</h1>
<div>
    You are successfully logged in <span style="font-weight:bold" th:text="${userName}"></span>
    via the OAuth 2.0 Client <span style="font-weight:bold" th:text="${clientName}"></span>
</div>
<div>&nbsp;</div>
<div>
    <span style="font-weight:bold">User Attributes:</span>
    <ul>
        <li th:each="userAttribute : ${userAttributes}">
            <span style="font-weight:bold" th:text="${userAttribute.key}"></span>: <span th:text="${userAttribute.value}"></span>
        </li>
    </ul>
</div>
</body>
</html>
```



### 3.6、启动应用程序

- 启动程序并访问localhost:8080。浏览器将被重定向到默认的自动生成的登录页面，该页面显示了一个用于GitHub登录的链接。
- 点击GitHub链接，浏览器将被重定向到GitHub进行身份验证。
- 使用GitHub账户凭据进行身份验证后，用户会看到授权页面，询问用户是否允许或拒绝客户应用访问GitHub上的用户数据。点击允许以授权OAuth客户端访问用户的基本个人资料信息。
- 此时，OAuth客户端访问GitHub的获取用户信息的接口获取基本个人资料信息，并建立一个已认证的会话。



## 4、案例分析

### 4.1、登录流程

1. **A 网站让用户跳转到 GitHub，并携带参数ClientID 以及 Redirection URI。**
2. GitHub 要求用户登录，然后询问用户"A 网站要求获取用户信息的权限，你是否同意？"
3. 用户同意，GitHub 就会重定向回 A 网站，同时发回一个授权码。
4. **A 网站使用授权码，向 GitHub 请求令牌。**
5. GitHub 返回令牌.
6. **A 网站使用令牌，向 GitHub 请求用户数据。**
7. GitHub返回用户数据
8. **A 网站使用 GitHub用户数据登录**

![image-20231223203225688](./assets/image-20231223203225688-1717576216461-17.png)

### 4.2、CommonOAuth2Provider

CommonOAuth2Provider是一个预定义的通用OAuth2Provider，为一些知名资源服务API提供商（如Google、GitHub、Facebook）预定义了一组默认的属性。

例如，**授权URI、令牌URI和用户信息URI**通常不经常变化。因此，提供默认值以减少所需的配置。

因此，当我们配置GitHub客户端时，只需要提供client-id和client-secret属性。

```java
GITHUB {
    public ClientRegistration.Builder getBuilder(String registrationId) {
        ClientRegistration.Builder builder = this.getBuilder(
        registrationId, 
        ClientAuthenticationMethod.CLIENT_SECRET_BASIC, 
        
        //授权回调地址(GitHub向客户应用发送回调请求，并携带授权码)   
		"{baseUrl}/{action}/oauth2/code/{registrationId}");
        builder.scope(new String[]{"read:user"});
        //授权页面
        builder.authorizationUri("https://github.com/login/oauth/authorize");
        //客户应用使用授权码，向 GitHub 请求令牌
        builder.tokenUri("https://github.com/login/oauth/access_token");
        //客户应用使用令牌向GitHub请求用户数据
        builder.userInfoUri("https://api.github.com/user");
        //username属性显示GitHub中获取的哪个属性的信息
        builder.userNameAttributeName("id");
        //登录页面超链接的文本
        builder.clientName("GitHub");
        return builder;
    }
},
```





**Spring Security**

- 客户应用（OAuth2 Client）：OAuth2客户端功能中包含OAuth2 Login
- 资源服务器（OAuth2 Resource Server）

**Spring**

- 授权服务器（Spring Authorization Server）：它是在Spring Security之上的一个单独的项目。



## 相关依赖

```xml
<!-- 资源服务器 -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
</dependency>

<!-- 客户应用 -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-oauth2-client</artifactId>
</dependency>

<!-- 授权服务器 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-authorization-server</artifactId>
</dependency>
```









# SSO（Single Sign On）

> CAS是中央认证服务，Central Authentication Service的缩写，每个单点登录的架构都需要实现一个CAS服务
>
> 其实就是一个认证。



- **服务标识**：每个系统或应用都会从 CAS获得一个唯一标识
- **TGT Ticket**:是在成功通过 CAS 认证后通过 cookie 的形式发放给客户端的，可以把它比作游乐园的通票，有了它就可以畅玩游乐园中的所有项目。
- **Service Ticket (ST)** :  有的项目需要特殊的门票，这好比是某个具体游玩项目的门票。





## 理解认证与授权

什么是认证 (Authentication)：认证是关于验证你的凭据，如用户名/邮箱和密码，以验证访问者的身份。

什么是授权（Authorization)：授权发生在系统完成身份认证之后，最终会授予你访问资源（如信息，文件，数据库，资金，位置，几乎任何内容）的完全权限。



在典型的单点登录SSO体系结构中，身份提供者和服务提供者交换数字证书和元数据以建立信任。它们通过诸如安全断言标记语言（SAML）、OIDC（基于OAuth2）等开放协议进行交互



其中OIDC = OAuth2 + 认证

- OIDC 是在 OAuth2 基础上的扩展，主要加入了认证的内容
- OAuth2中有4个角色，分别是授权服务器，资源服务器，资源所有者，客户端
- OIDC聚焦在认证环节，所以没有资源服务器这个角色，其他三个名称也做了调整，授权服务器改成了 OpenlD provider，资源所有者称作终端用户(end user)，而客户端称作 Relying party



1、OAuth2 中定义了 4 种授权方式，最常见的是授权码方式（Authorization Code），也是我们上面例子中演示的（微信登陆腾讯会议），这种方式的优点之一是我们不需要将密码告知客户端，也能让客户端获得需要的资源

2、OAuth2 中规定了两个服务接口，分别是授权接口（Authorization Endpoint）和令牌接口（token endpoint），OIDC 中新增了一个 userinfo 接口，用于获取更信息的用户信息

3、scope 定义了具体需要访问资源的内容，这并非由规范定义，而是有服务实现方决定，OIDC 中增加了 openid 的 scope，这样调用令牌接口返回的响应中就会携带openid 属性，这个 ID 是个 JWT

4、OAuth2中定义了两种令牌，访问令牌（Access token）和刷新令牌（refresh token），OIDC新增了一种令牌openid，当scope 中指定了 openid 后就会得到 openid 令牌



> scope 它定义了客户端所请求的对用户资源的访问权限范围。简而言之，`scope`是一个字符串参数，用于详细说明客户端希望获得哪些类型的访问权限。服务提供商（如授权服务器）会根据提供的`scope`来决定授予访问令牌（access token）时包含哪些权限。










参考：

[SpringSecurity-从入门到精通_spring security 引入-CSDN博客](https://blog.csdn.net/weixin_43847283/article/details/124075302)

[27-前后端分离-请求未认证处理_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV14b4y1A7Wz?p=27)