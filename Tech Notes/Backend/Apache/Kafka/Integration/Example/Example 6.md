
# kafka消费的完整解决方案

无论是kafka，还是RocketMQ，rabbitMQ等，与springboot的结合得益于spring的强大，使得变的非常easy，但依然知识简单的使用变的非常容易，如果要达到理想的结果，不仅需要他们对原理熟悉一点，还要对spring提供的sdk熟悉，下面就看一下kafka的使用，以及需要解决的一些问题。

1.  引入依赖
    

```pom
            <!--kafka-->  
            <dependency>  
                <groupId>org.springframework.kafka</groupId>  
                <artifactId>spring-kafka</artifactId>  
                <version>2.8.2</version>  
            </dependency>
```

2.  消费
    

```java
/**  
     * 消费者监听.  
     *  
     * @param message 消息内容  
     * @param ack     ack  
     */  
    @KafkaListener(topics = {"test_topic"})  
    public void listener(String message) {  
       //消费落库  
    }
```

3.  配置kafka地址
    

```yaml
spring:      
  kafka:  
    bootstrap-servers: localhost:9092
```

通过以上三步，基本上就可以成功消费到。

### but

如果你在公司写这样的代码，肯定要被吐槽的，因为这段代码和配置，只能简单的消费，并不能解决消息丢失，重复消费，并发消费，消费能力不足或者浪费资源等问题。

接下来一一改造成理想的样子。

### 消息丢失的解决方法

1.  生产者层面，Kafka消息发送有两种方式：
    同步(sync)和异步(async),默认是同步方式，可通过 `producer.type`)属性进行配置。Kafka通过配置`request.required.acks`属性来确认消息的生产：
-   0一表示不进行消息接收是否成功的确认：
-   1一表示当Leader接收成功时确认；
-   -1一表示Leader和Follower都接收成功时确认：
    
这是发送消息阶段需要根据需要去配置的。可以配置-1.但是效率是最低的。
2.  当然还有另一种情况就属于与业务层面，消费后kafka的offset被自动提交了，但实际上业务并没有成功消费。
    针对这种情况，可以设置手动提交，配置`enable.auto.commit`为false。手动 去提交offset，代码改造为：
    
```
   @KafkaListener(topics = {"test_topic"})  
    public void listener(final String message, final Acknowledgment ack) {  
        //消费业务代码  
        //...  
          
        //提交offset  
        ack.acknowledge();  
    }
```

### 消息重复消费

先看一下设置为手动提交offset后，产生的三种情况：

-   1.如果在消费kafka的数据过程中，一直没有提交offset，那么在此程序运行的过程中它不会重复消费。但是如果重启之后，就会重复消费之前没有提交offset的数据。
    
-   2.如果有消费过程中有几条或者一批数据没有提交offset，后面其他的消息消费后正常提交0ffset . 那服务端会更新为消费后最新的offset，不会重新消费，就算重启程序也不会重新消费。
    
-   3.消费者如果没有提交offset，程 序不会阻塞或者重复消费，除非在消费到到这个你不想提交的offset的消息时，你尝试重新初始化一个客户端消费者，即可再次消费这个未提交offset的数据。因为客户端也记录了当前消费者的offset信息，所以程序会在每次消费了数据之后，自己记录offset，而手动提交到服务端的offset与这个并没有关系，所以程序会继续往下消费。在你重新初始化客户端消费者之后，会从服务端得到最新的offset信息记录到本地。所以说如果当前的消费的消息没有提交offset，此时在你重新初始化消费者之后，可得到这条未提交消息的offset,从此位置 开始消费。
    

接下来根据情况来解决

1.  手动提交offset，如果消费的时候业务代码没有完全执行结束，导致偏移量没有提交；
    经过测试，如果消费业务代码出现异常导致`ack.acknowledge()`没有执行，kakaf会重试多次进行消费。
    此时我们的业务代码就要处理这种插入数据的场景产生的重复数据落到数据库里；
第一种解决办法就是在insert的时候使用`INSERT INTO ...ON DUPLICATE KEY UPDATE`语法，不存在时插入，存在时更新，是天然支持幂等性的。
第二种解决办法，就是通过redis，根据业务的唯一键来存储到redis，每次消费时判断是否消费过，但一定要设置一个过期时间。
第三种情况是，如果你的消费者的concurrency设置的是1，没有并发的情况，那你可以先查库判断库里面是否有，再进行插入。（concurrency相当于消费线程，也相当于消费者，`机器数量*concurrency <= 分区数`）

2.  消费端重复发送了
    此时也可以用上面第一种所描述的方案
其实不管那种情况导致的重复消息，解决方案在业务里是一成不变的。

### 消费者自定义消费工厂

自定义配置类

```java
@ConfigurationProperties(prefix = KafkaSourceConfig.KAFKA_SOURCE_PREFIX)  
@Getter  
@Setter  
public class KafkaSourceConfig {  
  
    public static final String KAFKA_SOURCE_PREFIX = "custom.kafka.server";  
  
    private Boolean enable;  
  
    private List<String> bootstrapServers = Lists.newArrayList("localhost:9092");  
  
    private Consumer consumer = new Consumer();  
  
    @Data  
    public static class Consumer {  
  
        private String sessionTimeoutMs = "60000";  
  
        /**  
         * 最大poll数量.  
         */  
        private Integer maxPollRecords = 100;  
  
        /**  
         * 是否自动提交.  
         */  
        private Boolean enableAutoCommit = false;  
  
        private String autoOffsetReset = "earliest";  
  
        private String groupId;  
  
    }  
  
}
```

factory配置类

```java
@Configuration  
@EnableKafka  
@EnableConfigurationProperties(KafkaSourceConfig.class)  
public class KafkaConsumerConfiguration {  
  
    @Autowired  
    private KafkaSourceConfig kafkaSourceConfig;  
  
    @Autowired  
    private KafkaProperties kafkaProperties;  
  
    /**  
     * 自定义消费工厂.  
     *  
     * @return batchFactory  
     */  
    @Bean("batchFactory")  
    public KafkaListenerContainerFactory<?> batchFactory() {  
        ConcurrentKafkaListenerContainerFactory<String, String> factory = new ConcurrentKafkaListenerContainerFactory<>();  
        factory.setConsumerFactory(consumerFactory());  
        //并发数:机器数量*concurrency <= 分区数,the number of consumers to create  
        factory.setConcurrency(1);  
        //设置为批量消费，每个批次数量在Kafka配置参数中设置ConsumerConfig.MAX_POLL_RECORDS_CONFIG 默认 500  
        factory.setBatchListener(true);  
        //设置提交偏移量的方式:listener负责ack，也是批量上去  
        factory.getContainerProperties().setAckMode(ContainerProperties.AckMode.MANUAL);  
        return factory;  
    }  
  
    /**  
     * 配置消费者配置.  
     *  
     * @return config  
     */  
    private ConsumerFactory<String, String> consumerFactory() {  
        Map<String, Object> stringObjectMap = kafkaProperties.buildConsumerProperties();  
        stringObjectMap.put(CommonClientConfigs.BOOTSTRAP_SERVERS_CONFIG, Lists.newArrayList(kafkaSourceConfig.getBootstrapServers()));  
        stringObjectMap.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, kafkaSourceConfig.getConsumer().getEnableAutoCommit());  
        stringObjectMap.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, kafkaSourceConfig.getConsumer().getSessionTimeoutMs());  
        stringObjectMap.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, kafkaSourceConfig.getConsumer().getMaxPollRecords());  
        stringObjectMap.put(ConsumerConfig.GROUP_ID_CONFIG, kafkaSourceConfig.getConsumer().getGroupId());  
        stringObjectMap.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, kafkaSourceConfig.getConsumer().getAutoOffsetReset());  
        return new DefaultKafkaConsumerFactory<>(stringObjectMap);  
    }  
  
}
```

通过自定义 的配置和factory，`@Bean("batchFactory")`，然后我们需要去listener配置batchFactory，消费端代码变成如下：

```java
    @KafkaListener(topics = {"test_topic"}, containerFactory = "batchFactory")  
    public void listener(String message, final Acknowledgment ack) {  
         
        //业务代码  
        //...  
          
        ack.acknowledge();  
    }
```

### 批量消费配置

在batchFactory中配置BatchListener为true。

```java
 /**  
     * 自定义消费工厂.  
     *  
     * @return batchFactory  
     */  
    @Bean("batchFactory")  
    public KafkaListenerContainerFactory<?> batchFactory() {  
        ConcurrentKafkaListenerContainerFactory<String, String> factory = new ConcurrentKafkaListenerContainerFactory<>();  
        factory.setConsumerFactory(consumerFactory());  
        //并发数:机器数量*concurrency <= 分区数,the number of consumers to create  
        factory.setConcurrency(1);  
        //设置为批量消费，每个批次数量在Kafka配置参数中设置ConsumerConfig.MAX_POLL_RECORDS_CONFIG 默认 500  
        factory.setBatchListener(true);  
        //设置提交偏移量的方式:listener负责ack，也是批量上去  
        factory.getContainerProperties().setAckMode(ContainerProperties.AckMode.MANUAL);  
        return factory;  
    }
```

在消费端用list接收消息

```java
    @KafkaListener(topics = {"test_topic"}, containerFactory = "batchFactory")  
    public void listener(List<String> message, final Acknowledgment ack) {  
         
        //业务代码  
        //...  
          
        ack.acknowledge();  
    }
```

当然也可以使用ConsumerRecord接受消息，其中有对应分区，topic等详细信息

```java
    @KafkaListener(topics = {"test_topic"}, containerFactory = "batchFactory")  
    public void listener(List<ConsumerRecord<?, ?>> message, final Acknowledgment ack) {  
         
        //业务代码  
        //...  
          
        ack.acknowledge();  
    }
```

### 设置并发数

Concurrency可以设置并发数，如何设置了并发的情况下，一定要解决并发安全问题。

机器数量*concurrency <= 分区数

```java
 /**  
     * 自定义消费工厂.  
     *  
     * @return batchFactory  
     */  
    @Bean("batchFactory")  
    public KafkaListenerContainerFactory<?> batchFactory() {  
        ConcurrentKafkaListenerContainerFactory<String, String> factory = new ConcurrentKafkaListenerContainerFactory<>();  
        factory.setConsumerFactory(consumerFactory());  
        //并发数:机器数量*concurrency <= 分区数,the number of consumers to create  
        factory.setConcurrency(1);  
        //设置为批量消费，每个批次数量在Kafka配置参数中设置ConsumerConfig.MAX_POLL_RECORDS_CONFIG 默认 500  
        factory.setBatchListener(true);  
        //设置提交偏移量的方式:listener负责ack，也是批量上去  
        factory.getContainerProperties().setAckMode(ContainerProperties.AckMode.MANUAL);  
        return factory;  
    }
```

-   了解机器数的含义
    

topic设置10个分区，逐个启动两个服务

启动一个8081的服务

![](https://files.mdnice.com/user/14959/9f6800ba-c349-4188-81ac-c329bed138ab.png)

10个分区全部交给8081这台服务的消费者去消费了

启动8082的服务

![](https://files.mdnice.com/user/14959/896cf400-8c6c-44a7-bc25-6487fcae55c3.png)

发现，0，1,2,3,4四个分区交给了8082这台机器的消费者

现在观察8081服务的变化

![](https://files.mdnice.com/user/14959/f27efaa9-3c37-4a8a-ba12-417b80be3014.png)

发现发生了reblance，只有5,6,7,8,9五个分区交给8081服务的消费者.

所以两个消费者各自消费5个分区

-   了解机器数和并发数量的含义
    

设置3个并发,topic是10个分区

![](https://files.mdnice.com/user/14959/4c93a008-b4a3-4664-bbb9-de5db9106b13.png)

启动8081服务

![](https://files.mdnice.com/user/14959/f020fd32-2443-4177-9408-ca07537aa131.png)

有三个线程，相当于消费者，以数量3，3,4的分区数量去消费了

启动8082服务

![](https://files.mdnice.com/user/14959/459bc59e-4d82-45cf-8010-03a8ead42913.png)

三个线程，相当于三个消费者，2,1,2的分区数量去消费，那此时8081应该会发生reblance。

查看8081服务

![](https://files.mdnice.com/user/14959/84b8ee74-edd2-4c19-a406-70a2b3a3ef30.png)

此时也是由三个消费者，3，3,4变成2，1,2的分区数去消费了

8081服务和8082服务加起来6个线程，平均消费10个分区。

再启动两台8083,8084

8081服务：![](https://files.mdnice.com/user/14959/c82ee769-779a-46c9-acf9-a3dac6080b39.png)8082服务：

![](https://files.mdnice.com/user/14959/04f5b48a-84d7-44f9-a905-d7168e90f1d0.png)

8083服务：

![](https://files.mdnice.com/user/14959/9c5cda6f-eb2b-42ad-9b63-6b82ac9e4096.png)

8084服务：![](https://files.mdnice.com/user/14959/e16441c5-bb84-4fa0-9aa6-d9218cbf90bc.png)

从图中可以得出，四台服务三个并发线程，4*3=12>10个分区数，所以8081,8083两个机器各有一个线程将一直闲着浪费资源

> 结论：机器数量*concurrency <= 分区数

### 配置动态topic

在yaml中根据环境配置topic,获取`custom.kafka.server.consumer.topic`配置的值，同时也可以设置默认值。

```java
    @KafkaListener(topics = {"${custom.kafka.server.consumer.topic:test_topic_dev}"}, containerFactory = "batchFactory")  
    public void listener(List<ConsumerRecord<?, ?>> message, final Acknowledgment ack) {  
         
        //业务代码  
        //...  
          
        ack.acknowledge();  
    }
```