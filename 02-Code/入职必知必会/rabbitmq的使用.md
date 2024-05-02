

#####                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               Java入职必知必会之RabbitMq的使用

​                                                                                                                       V:nb887722        公众号： 拙野，回复zy521可领取视频资料         CSDN:拙野

#### 1、引入依赖

```
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
<dependency>
      <groupId>com.fasterxml.jackson.dataformat</groupId>
       <artifactId>jackson-dataformat-xml</artifactId>
 </dependency>
```

#### 2、配置rabbitmq的连接信息等

* 生产者配置

  ```yaml
    rabbitmq:
      host: 110.40.209.16
      port: 5672
      username: zhuoye
      password: zy521
      virtual-host: /
  ```

* 消费者配置

  ```yaml
    rabbitmq:
      host: 110.40.209.16
    port: 5672
      username: zhuoye
      password: zy521
      virtual-host: /
      listener:
        simple:
          prefetch: 1 #每次只能处理一个，处理完成才能获取下一个消息
  ```
  
  

#### 3、设置消息转换器

目的：默认情况下Spring采用的序列化方式是JDK序列化，而JDK的序列化存在可读性性差、占用内存大、存在安全漏洞等问题。所以，这里我们一般使用

Jackson的序列化代替JDk的序列化。

在生产者和消费者的启动类上加上如下代码：

```java
@SpringBootApplication
@EnableRabbit //开启rabbitmq的使用
public class ConsumerApp {
    public static void main( String[] args ) {
        SpringApplication.run(ConsumerApp.class, args);
        String fozuStr = "ICAgICAgICAgICAgICAgICAgIF9vb09vb18KICAgICAgICAgICAgICAgICAgbzg4ODg4ODhvCiAgICAgICAgICAgICAgICAgIDg4IiAuICI4OAogICAgICAgICAgICAgICAgICAofCAtXy0gfCkKICAgICAgICAgICAgICAgICAgT1wgID0gIC9PCiAgICAgICAgICAgICAgIF9fX18vYC0tLSdcX19fXwogICAgICAgICAgICAgLicgIFxcfCAgICAgfC8vICBgLgogICAgICAgICAgICAvICBcXHx8fCAgOiAgfHx8Ly8gIFwKICAgICAgICAgICAvICBffHx8fHwgLTotIHx8fHx8LSAgXAogICAgICAgICAgIHwgICB8IFxcXCAgLSAgLy8vIHwgICB8CiAgICAgICAgICAgfCBcX3wgICcnXC0tLS8nJyAgfCAgIHwKICAgICAgICAgICBcICAuLVxfXyAgYC1gICBfX18vLS4gLwogICAgICAgICBfX19gLiAuJyAgLy0tLi0tXCAgYC4gLiBfXwogICAgICAuIiIgJzwgIGAuX19fXF88fD5fL19fXy4nICA+JyIiLgogICAgIHwgfCA6ICBgLSBcYC47YFwgXyAvYDsuYC8gLSBgIDogfCB8CiAgICAgXCAgXCBgLS4gICBcXyBfX1wgL19fIF8vICAgLi1gIC8gIC8KPT09PT09YC0uX19fX2AtLl9fX1xfX19fXy9fX18uLWBfX19fLi0nPT09PT09CiAgICAgICAgICAgICAgICAgICBgPS0tLT0nCl5eXl5eXl5eXl5eXl5eXl5eXl5eXl5eXl5eXl5eXl5eXl5eXl5eXl5eXl5eXgogICAgICAgICAgICAgICAgIOS9m+elluS/neS9kSAgICAgICDmsLjml6BCVUc=";
        byte[] decode = Base64.decode(fozuStr);
        System.out.println(new String(decode));
    }

    //使用的是Jackson库中的Jackson2JsonMessageConverter类，代替使用jdk自带的序列化
    @Bean
    public MessageConverter jacksonMessageConvertor(){
        Jackson2JsonMessageConverter jackson2JsonMessageConverter = new Jackson2JsonMessageConverter();
        jackson2JsonMessageConverter.setCreateMessageIds(true);//开启消息id的自动生成功能
        return jackson2JsonMessageConverter;
    }
}
```

#### 4、生产者代码示例

* 配置文件

```java
@Configuration
public class RabbitMqConfig {

    private static String EXCHANGE_NAME="amq.topic";
    private static String QUEUE_NAME="alarm.data.topic.queue";
    private static String CONFIRM_ALARM_QUEUE_NAME="alarm.confirm.data.topic.queue";

    /**
     * 声明交换机
     */

    @Bean
    public TopicExchange exchange(){
         // durable:是否持久化,默认是false
        // autoDelete:是否自动删除，当没有生产者或者消费者使用此交换机，该交换机会自动删除。
        return new TopicExchange(EXCHANGE_NAME,true,false);
    }

    /**
     * 声明告警队列
     * @return
     */
    @Bean("alarmQueue")
    public Queue alarmQueue(){
        // durable:是否持久化,默认是false,持久化队列：会被存储在磁盘上，当消息代理重启时仍然存在
        // exclusive:默认也是false，只能被当前创建的连接使用，而且当连接关闭后队列即被删除。此参考优先级高于durable
        // autoDelete:是否自动删除，当没有生产者或者消费者使用此队列，该队列会自动删除。
        return new Queue(QUEUE_NAME,true,false,false);
    }

    /**
     * 声明确认告警队列
     * @return
     */
    @Bean("confirmAlarmQueue")
    public Queue confirmAlarmQueue(){
        return new Queue(CONFIRM_ALARM_QUEUE_NAME,true,false,false);
    }

    /**
     * 声明告警队列绑定关系
     * @param queue
     * @param topicExchange
     * @return
     */
    @Bean
    public Binding alarmBinding(@Qualifier("alarmQueue") Queue queue, TopicExchange topicExchange){
        return BindingBuilder.bind(queue).to(topicExchange).with("server.event.#");
    }

    /**
     * 声明确认告警队列绑定关系
     * @param queue
     * @param topicExchange
     * @return
     */
    @Bean
    public Binding confirmAlarmBinding(@Qualifier("confirmAlarmQueue") Queue queue, TopicExchange topicExchange){
        return BindingBuilder.bind(queue).to(topicExchange).with("server.event_confirm.#");
    }
```

* 生产消息代码

  ```java
      @Autowired
      private RabbitTemplate rabbitTemplate;
  
      private static String EXCHANGE_NAME="amq.topic";
      private static String CONFIRM_ALARM_QUEUE_NAME="alarm.confirm.data.topic.queue";
  
      @Test
      void producerAlarmMsg() {
          String msg = "发送一条告警消息";
          rabbitTemplate.convertAndSend(EXCHANGE_NAME, "server.event.#",msg);
          System.out.println("msg = " + msg);
      }
  
      @Test
      void producerConfirmAlarmMsg() {
          String msg = "发送一条确认告警消息";
          rabbitTemplate.convertAndSend(CONFIRM_ALARM_QUEUE_NAME, "server.event_confirm.#",msg);
          System.out.println("msg = " + msg);
      }
  ```

#### 5、消费者代码示例

```java
@Component
public class AlarmConsumer {

        @Autowired
        private IAlarmService alarmService;


        @RabbitListener(queues ="alarm.data.topic.queue",concurrency = "5")
        public void getAlarmInfo(String data){
            alarmService.dealAlarmData(data);
        }

        @RabbitListener(queues ="alarm.confirm.data.topic.queue",concurrency = "5")
        public void getConfirmAlarmInfo(String data){
            alarmService.dealConfirmAlarmData(data);
        }
}
```

```java
@Service
public class IAlarmServiceImpl implements IAlarmService {

    @Override
    public void dealAlarmData(String data) {

        EquipAlarmResp equipAlarmResp= JSON.parseObject(result,EquipAlarmResp.class);
        List<String> alarmIdsOld = dceEquipAlarmMapper.queryAllAlarmIds();
        DceEquipAlarmDto dceEquipAlarmDto = CopyBeanUtils.copyProperties(equipAlarmResp, DceEquipAlarmDto.class);
        dceEquipAlarmDto.setCreateTime(new Date());
        dceEquipAlarmDto.setAlarmTime(dceEquipAlarmDto.getAlarmTime()/1000);
        //查询出需要新增或者更新的数据
        Boolean flag=alarmIdsOld.stream().filter(a->a.equals(dceEquipAlarmDto.getAlarmId())).findFirst().isPresent();
        //开启事务，保证新增、更新、删除的原子性
        TransactionStatus transaction = transactionManager.getTransaction(transactionDefinition);
        List<DceEquipAlarmDto> list=new ArrayList<>();
        list.add(dceEquipAlarmDto);
        try {
            //新增
            if (!flag) {
                dceEquipAlarmMapper.insertBatch(list);
            }
            //更新
            if (flag) {
                dceEquipAlarmMapper.updateBatch(list);
            }
            //提交事务
            transactionManager.commit(transaction);
        } catch (Exception e) {
            //回滚
            transactionManager.rollback(transaction);
            log.error("DynamicEnvironmentServiceImpl.getAlarmInfoByRabbitMq 新华报业动环设备告警信息更新失败！", e);
        }
    }

    @Override
    public void dealConfirmAlarmData(String data) {

        EquipConfirmAlarmResp alarmResp = JSON.parseObject(data,EquipConfirmAlarmResp.class);
        Integer confirmTime = Integer.parseInt(String.valueOf(System.currentTimeMillis() / 1000));
        alarmResp.setConfirmTime(confirmTime);
        dceEquipAlarmMapper.updateConfirmAlarmBatch(alarmResp,alarmResp.getAlarmIds());

    }

}

```

