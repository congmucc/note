功能

**身份认证**：·身份认证是验证谁正在访问系统资源，判断用户是否为合法用户。认证用户的常见方式是要求用户输入用户名和密码

**授权**：用户进行身份认证后，系统会控制谁能访问哪些资源，这个过程叫做授权。用户无法访问没有权限的资源。

**防御常见攻击**：

- CSRF

- HTTP Headers
- HTTP Requests



SpringSecurity的原理其实就是一个过滤器链，内部包含了提供各种功能的过滤器。这里我们可以看看入门案例中的过滤器。

- **UsernamePasswordAuthenticationFilter**：负责处理我们在登陆页面填写了用户名密码后的登陆请求。入门案例的认证工作主要有它负责。

- **ExceptionTranslationFilter**:处理过滤器链中抛出的任何AccessDeniedException和AuthenticationException

- **FilterSecuritylnterceptor**：负责权限校验的过滤器。



**Authentication**接口: 它的实现类，表示当前访问系统的用户，封装了用户相关信息。

**AuthenticationManager**接口：定义了认证Authentication的方法

**UserDetailsService**接口：加载用户特定数据的核心接口。里面定义了一个根据用户名查询用户信息的方法。

**UserDetails**接口：提供核心用户信息。通过UserDetailsService根据用户名获取处理的用户信息要封装成UserDetails对象返回。然后将这些信息封装到Authentication对象中。













**具体使用：**

1. 添加依赖

2. 需要添加一个配置类，具体去[spring官网](https://docs.spring.io/spring-security/reference/servlet/configuration/java.html)上进行配置类配置。

3. 基于数据库的用户认证流程实现。

4. 



1. 

**2、需要添加一个配置类**

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





3、**基于数据库的用户认证流程实现**：

[14-自定义配置-基于数据库的用户认证流程实现_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV14b4y1A7Wz?p=14)

- 程序启动时：
  - 创建DBUserDetailsManager类，实现接口UserDetailsManager,UserDetailsPasswordService。
  - 在应用程序中初始化这个类的对象

> 第一步主要是将内存中的配置放入到表中。这里是需要修改一下具体使用的配置，具体可以参照`InMemoryUserDetailsManager`这个方法中返回值等东西
>
> 其中主要是loadUserByUsername重写方式需要参考

重写详情

![image-20240604174043250](./assets/image-20240604174043250.png)

重写后的配置类

![image-20240604174153000](./assets/image-20240604174153000.png)

这个DBUserByUsername就是实现继承的方法

关于初始化类：这里面可以不在配置类中添加，直接再DBUserByUsername类中添加Component注解然后删除这个配置类都行，因为security实行的是

