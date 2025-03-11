## 1.1 SpringBoot启动

1. 首先从`main`找到`run()`方法，在执行run()方法之前new一个`SpringApplication`对象
2. 进入`run()`方法，创建应用监听器`SpringApplicationRunListeners`开始监听
3. 然后加载`SpringBoot配置环境(ConfigurableEnvironment)`，然后把配置环境`(Environment)`加入监听对象中
4. 然后加载应用上下文`(ConfigurableApplicationContext)`，当做run方法的返回对象
5. 最后创建Spring容器，`refreshContext(context)`，实现starter自动化配置和bean的实例化等工作。
SpringBoot启动
```java
@SpringBootApplication
public class Application {  
    public static void main(String[] args) {  
        SpringApplication.run(Application.class,args);  
    }  
}
```

首先进入run()方法，run方法中new创建了一个`SpringApplication`实例
```java
public static ConfigurableApplicationContext run(Class<?>[] primarySources, String[] args) {
  return (new SpringApplication(primarySources)).run(args); }
```

## 1.2 SpringBoot自动装配
通过`@EnableAutoConfiguration`注解在类路径的`META-INF/spring.factories`文件中找到所有的对应配置类，然后将这些自动配置类加载到spring容器中