
```text
enable-auto-commit: false #是否开启自动提交
#auto-commit-interval: 1000 #自动提交的间隔时间,自动提交去掉#号
```

```java
@Bean
public KafkaListenerContainerFactory < ConcurrentMessageListenerContainer < String, String >> kafkaListenerContainerFactory() {
  ConcurrentKafkaListenerContainerFactory < String, String > factory = new ConcurrentKafkaListenerContainerFactory < > ();
  factory.setConsumerFactory(consumerFactory());
  //多线程消费分区
  factory.setConcurrency(concurrency);
  //批量消费
  factory.setBatchListener(batchListener);
  //如果消息队列中没有消息，等待timeout毫秒后，调用poll()方法。
  // 如果队列中有消息，立即消费消息，每次消费的消息的多少可以通过max.poll.records配置。
  //手动提交无需配置
  factory.getContainerProperties().setPollTimeout(pollTimeout);
  //设置提交偏移量的方式，手动提交必须设置，自动提交注释掉即可
  factory.getContainerProperties().setAckMode(AbstractMessageListenerContainer.AckMode.MANUAL_IMMEDIATE);
  return factory;
}
```

```java
@KafkaListener(containerFactory = "kafkaListenerContainerFactory", topics = {
  "#{'${kafka.listener.topics}'.split(',')[0]}"
})
public void batchListener(List < ConsumerRecord < ? , ? >> records, Acknowledgment ack) {

  List < User > userList = new ArrayList < > ();
  try {
    records.forEach(record -> {
      User user = JSON.parseObject(record.value().toString(), User.class);
      user.getCreateTime().format(DateTimeFormatter.ofPattern(Contants.DateTimeFormat.DATE_TIME_PATTERN));
      userList.add(user);
    });
  } catch (Exception e) {
    log.error("Kafka监听异常" + e.getMessage(), e);
  } finally {
    //自动提交，注释掉即可，以及去掉方法中的参数：Acknowledgment ack
    ack.acknowledge(); //手动提交偏移量
  }

}
```

```properties
#kafka配置信息
kafka:
  producer:
    bootstrap-servers: 10.161.11.222:6667,10.161.11.223:6667,10.161.11.224:6667
    batch-size: 16785                                   #一次最多发送数据量
    retries: 1                                          #发送失败后的重复发送次数
    buffer-memory: 33554432                             #32M批处理缓冲区
    linger: 1
  consumer:
    bootstrap-servers: 10.161.11.222:6667,10.161.11.223:6667,10.161.11.224:6667
    auto-offset-reset: earliest                         #最早未被消费的offset
    group-id: log-hs-grou20
    max-poll-records: 4639                              #批量消费一次最大拉取的数据量
    enable-auto-commit: false                           #是否开启自动提交
    auto-commit-interval: 1000                          #自动提交的间隔时间
    session-timeout: 6000                               #连接超时时间
  listener:
    batch-listener: true                                #是否开启批量消费，true表示批量消费
    concurrency: 3                                  #设置消费的线程数
    poll-timeout: 1500                        #如果消息队列中没有消息，等待timeout毫秒后，调用poll()方法。如果队列中有消息，立即消费消息，每次消费的消息的多少可以通过max.poll.records配置。
    topics: hs-test,hs-test1
```

生产者配置：
```java
@Configuration
@EnableKafka
public class KafkaProducerConfig {

  @Value("${kafka.producer.bootstrap-servers}")
  private String bootstrapServers;

  @Value("${kafka.producer.retries}")
  private Integer retries;

  @Value("${kafka.producer.batch-size}")
  private Integer batchSize;

  @Value("${kafka.producer.buffer-memory}")
  private Integer bufferMemory;

  @Value("${kafka.producer.linger}")
  private Integer linger;

  public Map < String, Object > producerConfigs() {
    Map < String, Object > props = new HashMap < > (7);
    props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
    props.put(ProducerConfig.RETRIES_CONFIG, retries);
    props.put(ProducerConfig.BATCH_SIZE_CONFIG, batchSize);
    props.put(ProducerConfig.LINGER_MS_CONFIG, linger);
    props.put(ProducerConfig.BUFFER_MEMORY_CONFIG, bufferMemory);
    props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
    props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
    return props;
  }

  public ProducerFactory < String, String > producerFactory() {
    return new DefaultKafkaProducerFactory < > (producerConfigs());
  }

  @Bean
  public KafkaTemplate < String, String > kafkaTemplate() {
    return new KafkaTemplate < > (producerFactory());
  }
}
```

KafkaSender：
```java
@Component
@Slf4j
public class KafkaSender {

  private final KafkaTemplate < String, String > KAFKA_TEMPLATE;

  @Autowired
  public KafkaSender(KafkaTemplate < String, String > kafkaTemplate) {
    this.KAFKA_TEMPLATE = kafkaTemplate;
  }

  public void sendMessage(String topic, String message) {

    ListenableFuture < SendResult < String, String >> sender = KAFKA_TEMPLATE.send(new ProducerRecord < > (topic, message));
    //        //发送成功
    //        SuccessCallback successCallback = result -> log.info("数据发送成功!");
    //        //发送失败回调
    //        FailureCallback failureCallback = ex -> log.error("数据发送失败!");

    sender.addCallback(result -> {}, ex -> log.error("数据发送失败!"));
  }

}
```

消费者配置：
```java
@Configuration
@EnableKafka
public class KafkaConsumerConfig {

  @Value("${kafka.consumer.bootstrap-servers}")
  private String bootstrapServers;

  @Value("${kafka.consumer.enable-auto-commit}")
  private Boolean autoCommit;

  @Value("${kafka.consumer.auto-commit-interval}")
  private Integer autoCommitInterval;

  @Value("${kafka.consumer.group-id}")
  private String groupId;

  @Value("${kafka.consumer.max-poll-records}")
  private Integer maxPollRecords;

  @Value("${kafka.consumer.auto-offset-reset}")
  private String autoOffsetReset;

  @Value("${kafka.listener.concurrency}")
  private Integer concurrency;

  @Value("${kafka.listener.poll-timeout}")
  private Long pollTimeout;

  @Value("${kafka.consumer.session-timeout}")
  private String sessionTimeout;

  @Value("${kafka.listener.batch-listener}")
  private Boolean batchListener;

  @Bean
  public KafkaListenerContainerFactory < ConcurrentMessageListenerContainer < String, String >> kafkaListenerContainerFactory() {
    ConcurrentKafkaListenerContainerFactory < String, String > factory = new ConcurrentKafkaListenerContainerFactory < > ();
    factory.setConsumerFactory(consumerFactory());
    //多线程消费分区
    factory.setConcurrency(concurrency);
    //批量消费
    factory.setBatchListener(batchListener);
    //如果消息队列中没有消息，等待timeout毫秒后，调用poll()方法。
    // 如果队列中有消息，立即消费消息，每次消费的消息的多少可以通过max.poll.records配置。
    //手动提交无需配置
    factory.getContainerProperties().setPollTimeout(pollTimeout);
    //设置提交偏移量的方式，
    factory.getContainerProperties().setAckMode(AbstractMessageListenerContainer.AckMode.MANUAL_IMMEDIATE);
    return factory;
  }

  public Map < String, Object > consumerConfigs() {
    Map < String, Object > props = new HashMap < > (9);
    props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
    props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, autoCommit);
    props.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, autoCommitInterval);
    props.put(ConsumerConfig.GROUP_ID_CONFIG, groupId);
    props.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, maxPollRecords);
    props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, autoOffsetReset);
    props.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, sessionTimeout);
    props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
    props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
    return props;
  }

  public ConsumerFactory < String, String > consumerFactory() {
    return new DefaultKafkaConsumerFactory < > (consumerConfigs());
  }

}
```

KafkaListener
```java
@Slf4j
@Component
public class KafkaListeners {

  @KafkaListener(containerFactory = "kafkaListenerContainerFactory", topics = {
    "#{'${kafka.listener.topics}'.split(',')[0]}"
  })
  public void batchListener(List < ConsumerRecord < ? , ? >> records, Acknowledgment ack) {

    List < User > userList = new ArrayList < > ();
    try {
      records.forEach(record -> {
        User user = JSON.parseObject(record.value().toString(), User.class);
        user.getCreateTime().format(DateTimeFormatter.ofPattern(Contants.DateTimeFormat.DATE_TIME_PATTERN));
        userList.add(user);
      });
    } catch (Exception e) {
      log.error("Kafka监听异常" + e.getMessage(), e);
    } finally {
      ack.acknowledge(); //手动提交偏移量
    }

  }

}
```

```java
@Component
public class UserTask {

  @Value("#{'${kafka.listener.topics}'.split(',')}")
  private List < String > topics;

  private final MultiService MUlTI_SERVICE;

  private final KafkaSender KAFKA_SENDER;

  @Autowired
  public UserTask(MultiService multiService, KafkaSender kafkaSender) {
    this.MUlTI_SERVICE = multiService;
    this.KAFKA_SENDER = kafkaSender;
  }

  @Scheduled(fixedRate = 10 * 1000)
  public void addUserTask() {
    User user = new User();
    user.setUserName("HS");
    user.setDescription("text");
    user.setCreateTime(LocalDateTime.now());
    String JSONUser = JSON.toJSONStringWithDateFormat(user,
      Contants.DateTimeFormat.DATE_TIME_PATTERN, //日期格式化
      SerializerFeature.PrettyFormat); //格式化json
    for (int i = 0; i < 700; i++) {
      KAFKA_SENDER.sendMessage(topics.get(0), JSONUser);
    }
    MUlTI_SERVICE.addUser(user);
  }
}
```

```java
public class Contants {

  public static class DateTimeFormat {

    public static final String DATE_TIME_PATTERN = "yyyy-MM-dd HH:mm:ss";

    public static final String DATE_PATTERN = "yyyy-MM-dd";

    public static final String TIME_PATTERN = "HH:mm:ss";

    public static final String DATE_TIME_STAMP = "yyyyMMddHHmmss";

    public static final String DATE_STAMP = "yyyyMMdd";

    public static final String TIME_STAMP = "HHmmss";

  }
}
```

```java
@Data
public class User {
  private Integer id;
  private String userName;
  private String description;
  //@JSONField(format = "yyyy-MM-dd HH:mm:ss")
  private LocalDateTime createTime;
}
```

```java
@ComponentScan("com.*****")
@SpringBootApplication(exclude = {
  DataSourceAutoConfiguration.class
})
@EnableScheduling
@EnableAsync
public class MysqldbApplication implements CommandLineRunner {
  public static void main(String[] args) {
    SpringApplication.run(MysqldbApplication.class, args);
  }

  @Override
  public void run(String...strings) throws Exception {

    JSON.DEFFAULT_DATE_FORMAT = Contants.DateTimeFormat.DATE_TIME_PATTERN;
  }
}
```

总结：自动提交，在服务启停时，会有重复数据被生产到kafka中，保证吞吐量的同时，降低了kafka的原子性；手动提交，保证了kafka的原子性，同时降低了kafka的吞吐量，实际开发中，可跟随数据量的大小，自行分析配置。