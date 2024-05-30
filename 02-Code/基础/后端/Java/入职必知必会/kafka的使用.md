#####                                                                                                                                                                                                                 Java入职必知必会之kafka的使用

​                                                                                                                       V:nb887722        公众号： 拙野，回复zy521可领取视频资料         CSDN:拙野

1、引入kafka的依赖

```
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-stream-kafka</artifactId>
        </dependency>
```

2、配置kafka

```yaml
spring:
  kafka:
    bootstrap-servers: 156.65.20.76:9092,156.65.20.77:9092,156.65.20.78:9092 #指定Kafka集群的地址，这里有三个地址，用逗号分隔。
    listener:
      ack-mode: manual_immediate #设置消费者的确认模式为manual_immediate，表示消费者在接收到消息后立即手动确认。
      concurrency: 3  #设置消费者的并发数为5
      missing-topics-fatal: false  #设置为false，表示如果消费者订阅的主题不存在，不会抛出异常。
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer  # 设置消息键的序列化器
      value-serializer: org.apache.kafka.common.serialization.StringSerializer #设置消息值的序列化器
      acks: 1  #一般就是选择1，兼顾可靠性和吞吐量 ，如果想要更高的吞吐量设置为0，如果要求更高的可靠性就设置为-1
    consumer:
      auto-offset-reset: earliest #设置为"earliest"表示将从最早的可用消息开始消费，即从分区的起始位置开始读取消息。
      enable-auto-commit: false #禁用了自动提交偏移量的功能，为了避免出现重复数据和数据丢失，一般都是手动提交
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer  # 设置消息键的反序列化器
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer #设置消息值的反序列化器
```

3、创建主题

* 自动创建(不推荐)

  ```
  在kafka的安装目录conf目录下找到该配置文件server.properties，添加如下配置：
  num.partitions=3 #默认3个分区
  auto.create.topics.enable=true #开启自动创建主题
  default.replication.factor=3 #默认3个副本
  ```

* 手动创建

```
在kafka的安装目录bin目录下，执行如下命令：
//创建一个有三个分区和三个副本，名为zhuoye的主题
./kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 3 --partitions 3  --topic zhuoye
```

4、生产者代码

```java
@Slf4j
@Component
public class ALiYunServiceImpl implents IALiYunService {
    @Autowired
    private KafkaTemplate kafkaTemplate;
    @Autowired
    private ExecutorService executorService;
    String topicName = "zhuoye";
    @Override
    public void queryECSMetricInfo() {
        //发送到kafka的消息集合,因为使用了多线程，并且在多线程中往该集合进行添加操作，所以需要线程安全的
        List<Message> messages = Collections.synchronizedList(new ArrayList<>());
        boolean flag = true;
        //获取上次查询时间
        Long startTime = Long.valueOf(queryTimeRecordMapper.selectTimeByBelongId(3)) * 1000;
        Long endTime = System.currentTimeMillis();
        try {
            //查询出所有的运行中的实例
            List<CloudInstanceAssetDto> cloudInstances = cloudInstanceAssetMapper.queryAllRunningInstance(1, "Running");
            if (CollectionUtils.isEmpty(cloudInstances)) {
                return;
            }
            //定义计数器
            CountDownLatch latch = new CountDownLatch(cloudInstances.size());
            //遍历查询
            for (CloudInstanceAssetDto instance : cloudInstances) {
                executorService.submit(() -> {
                    try {
                        //获取内网流出带宽，并将结果封装到消息集合中
                        dealMetricDataToMessage(ALiYunConstant.ECS_INTRANET_OUT_RATE, ALiYunConstant.INTRANET_OUT_RATE_NAME, ALiYunConstant.LW_INTRANET_OUT_RATE_CODE,
                                startTime, endTime, instance, messages);
                    } catch (Exception e) {
                        log.error("获取ECS的指标数据-多线程处理任务异常！", e);
                    } finally {
                        latch.countDown();
                    }

                });
            }
            //等待任务执行完毕
            latch.await();
            //将最终的消息集合发送到kafka
            if (CollectionUtils.isNotEmpty(messages)) {
                for (int i = 0; i < messages.size(); i++) {
                    if (StringUtils.isNotBlank(messages.get(i).getValue())
                            && "noSuchInstance".equals(messages.get(i).getValue())) {
                        continue;
                    }
                  kafkaTemplate.send(topicName,  messages.get(i));
                }
            }
        } catch (Exception e) {
            flag = false;
            log.error("获取ECS的指标数据失败", e);
        }
        //更新记录上次查询时间
        if (flag) {
            QueryTimeRecord queryTimeRecord = new QueryTimeRecord();
            queryTimeRecord.setBelongId(3).setLastQueryTime(String.valueOf((endTime - 1000 * 60 * 1) / 1000)); //开始时间往前推1分钟
            queryTimeRecordMapper.updateByBelongId(queryTimeRecord);
        }
    }
```

这个时候，如果你想看有没有把消息发送到kafka的指定主题可以使用如下命令：

```
kafka-console-consumer.sh --bootstrap-server localhost:9093 --topic zhuoye
```

5、消费者代码

```java
@Slf4j
@Component
public class KafkaConsumer {
    // 消费监听
    @KafkaListener(topics = "zhuoye",groupId ="zhuoye-aliyunmetric")
    public void consumeExtractorChangeMessage(ConsumerRecord<String, String> record, Acknowledgment ack){
        try {
            String value = record.value();
            //处理数据，存入openTsDb
            .................
            ................
            ack.acknowledge();//手动提交
        }catch (Exception e){
            log.error("kafa-topic【zhuoye】消费阿里云指标源消息【失败】");
            log.error(e.getMessage());
        }

    }
}
```



6、常用Kafka的命令

```
//创建主题
./kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 3 --partitions 3  --topic zhuoye
//查看kafka是否接收对应的消息
 kafka-console-consumer.sh --bootstrap-server localhost:9093 --topic zhuoye
// 修改kafka-topic分区数
./kafka-topics.sh --zookeeper localhost:2181 -alter --partitions 6 --topic zhuoye
// 查看topic分区数
./kafka-topics.sh --zookeeper localhost:2181 --describe --topic zhuoye
// 查看用户组消费情况
./kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group zhuoye-aliyunmetric --describe
```

























