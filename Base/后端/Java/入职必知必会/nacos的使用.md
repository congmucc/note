#####                                                                                                                                                                                                                                                     java入职必知必会之nacos的使用 

​                                                                                                                                  v:  nb887722         CSDN:拙野

​                                                                                                           

1、服务注册与发现

![image-20240120233908026](图片\image-20240120233908026.png)

2、远程配置

![image-20240120233923608](图片\image-20240120233923608.png)

3、nacos的一般配置

http://110.40.209.16:8848/nacos

```
-Dspring.cloud.nacos.config.server-addr=http://110.40.209.16:8848
-Dspring.cloud.nacos.config.file-extension=yml
#-Dspring.cloud.nacos.config.namespace=f61f6163-9824-4f2b-b27f-9832e53c10f6
-Dspring.cloud.nacos.discovery.group=zhuoye
-Dspring.profiles.active=dev
-Dspring.cloud.nacos.config.group=DEFAULT_GROUP
-Dspring.cloud.nacos.config.username=nacos
-Dspring.cloud.nacos.config.password=nacos
-Dspring.cloud.nacos.discovery.username=nacos
-Dspring.cloud.nacos.discovery.password=nacos
#-Dspring.cloud.nacos.discovery.namespace=f61f6163-9824-4f2b-b27f-9832e53c10f6
```

