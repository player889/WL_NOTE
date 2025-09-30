https://www.jianshu.com/p/a64defb44a23

#### 批量消费案例

-   重新创建一份新的消费者配置，配置为一次拉取5条消息
-   创建一个监听容器工厂，设置其为批量消费并设置并发量为5，这个并发量根据分区数决定，必须小于等于分区数，否则会有线程一直处于空闲状态
-   创建一个分区数为8的Topic
-   创建监听方法，设置消费id为batch，clientID前缀为batch，监听topic.quick.batch，使用batchContainerFactory工厂创建该监听容器

```java
@Component
public class BatchListener {

    private static final Logger log= LoggerFactory.getLogger(BatchListener.class);

    private Map<String, Object> consumerProps() {
        Map<String, Object> props = new HashMap<>();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, true);
        props.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, "1000");
        props.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, "15000");
        //一次拉取消息数量
        props.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, "5");
        props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, IntegerDeserializer.class);
        props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        return props;
    }

    @Bean("batchContainerFactory")
    public ConcurrentKafkaListenerContainerFactory listenerContainer() {
        ConcurrentKafkaListenerContainerFactory container = new ConcurrentKafkaListenerContainerFactory();
        container.setConsumerFactory(new DefaultKafkaConsumerFactory(consumerProps()));
        //设置并发量，小于或等于Topic的分区数
        container.setConcurrency(5);
        //设置为批量监听
        container.setBatchListener(true);
        return container;
    }

    @Bean
    public NewTopic batchTopic() {
        return new NewTopic("topic.quick.batch", 8, (short) 1);
    }


    @KafkaListener(id = "batch",clientIdPrefix = "batch",topics = {"topic.quick.batch"},containerFactory = "batchContainerFactory")
    public void batchListener(List<String> data) {
        log.info("topic.quick.batch  receive : ");
        for (String s : data) {
            log.info(  s);
        }
    }

}
```


我们可以看到batchListener这个监听容器的partition分配信息。我们设置concurrency为5，也就是将会启动5条线程进行监听，那我们创建的topic则是有8个partition，意味着将有3条线程分配到2个partition和2条线程分配到1个partition。我们可以看到这段日志的最后5行，这就是每条线程分配到的partition。

```text
 o.a.k.c.c.internals.AbstractCoordinator  : [Consumer clientId=batch-2, groupId=batch] Successfully joined group with generation 98
 o.a.k.c.c.internals.ConsumerCoordinator  : [Consumer clientId=batch-2, groupId=batch] Setting newly assigned partitions [topic.quick.batch-4, topic.quick.batch-5]
 o.a.k.c.c.internals.AbstractCoordinator  : [Consumer clientId=batch-3, groupId=batch] Successfully joined group with generation 98
 o.a.k.c.c.internals.AbstractCoordinator  : [Consumer clientId=batch-0, groupId=batch] Successfully joined group with generation 98
 o.a.k.c.c.internals.AbstractCoordinator  : [Consumer clientId=batch-4, groupId=batch] Successfully joined group with generation 98
 o.a.k.c.c.internals.ConsumerCoordinator  : [Consumer clientId=batch-3, groupId=batch] Setting newly assigned partitions [topic.quick.batch-6]
 o.a.k.c.c.internals.ConsumerCoordinator  : [Consumer clientId=batch-0, groupId=batch] Setting newly assigned partitions [topic.quick.batch-0, topic.quick.batch-1]
 o.a.k.c.c.internals.ConsumerCoordinator  : [Consumer clientId=batch-4, groupId=batch] Setting newly assigned partitions [topic.quick.batch-7]
 o.a.k.c.c.internals.AbstractCoordinator  : [Consumer clientId=batch-1, groupId=batch] Successfully joined group with generation 98
 o.a.k.c.c.internals.ConsumerCoordinator  : [Consumer clientId=batch-1, groupId=batch] Setting newly assigned partitions [topic.quick.batch-2, topic.quick.batch-3]
 o.s.k.l.KafkaMessageListenerContainer    : partitions assigned: [topic.quick.batch-6]
 o.s.k.l.KafkaMessageListenerContainer    : partitions assigned: [topic.quick.batch-0, topic.quick.batch-1]
 o.s.k.l.KafkaMessageListenerContainer    : partitions assigned: [topic.quick.batch-7]
 o.s.k.l.KafkaMessageListenerContainer    : partitions assigned: [topic.quick.batch-2, topic.quick.batch-3]
 o.s.k.l.KafkaMessageListenerContainer    : partitions assigned: [topic.quick.batch-4, topic.quick.batch-5]
```

在短时间内发送12条消息到topic中，可以看到运行结果，对应的监听方法总共拉取了三次数据，其中两次为5条数据，一次为2条数据，加起来就是我们在测试方法发送的12条数据。证明我们的批量消费方法是按预期进行的。
```java
   @Autowired
    private KafkaTemplate kafkaTemplate;

    @Test
    public void testBatch() {
        for (int i = 0; i < 12; i++) {
            kafkaTemplate.send("topic.quick.batch", "test batch listener,dataNum-" + i);
        }
    }
```

```text
com.viu.kafka.listen.BatchListener       : topic.quick.batch  receive : 
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-5
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-2
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-10
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-6
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-3
com.viu.kafka.listen.BatchListener       : topic.quick.batch  receive : 
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-11
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-0
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-8
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-7
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-4
com.viu.kafka.listen.BatchListener       : topic.quick.batch  receive : 
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-1
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-9
```

注意：设置的并发量不能大于partition的数量，如果需要提高吞吐量，可以通过增加partition的数量达到快速提升吞吐量的效果。

#### 监听Topic中指定的分区

@KafkaListener注解的topicPartitions属性监听不同的partition分区。  
@TopicPartition：topic--需要监听的Topic的名称，partitions --需要监听Topic的分区id，  partitionOffsets --可以设置从某个偏移量开始监听 
@PartitionOffset：partition --分区Id，非数组，initialOffset --初始偏移量

```java
   @Bean
    public NewTopic batchWithPartitionTopic() {
        return new NewTopic("topic.quick.batch.partition", 8, (short) 1);
    }

    @KafkaListener(id = "batchWithPartition",clientIdPrefix = "bwp",containerFactory = "batchContainerFactory",
        topicPartitions = {
            @TopicPartition(topic = "topic.quick.batch.partition",partitions = {"1","3"}),
            @TopicPartition(topic = "topic.quick.batch.partition",partitions = {"0","4"},
                    partitionOffsets = @PartitionOffset(partition = "2",initialOffset = "100"))
        }
    )
    public void batchListenerWithPartition(List<String> data) {
        log.info("topic.quick.batch.partition  receive : ");
        for (String s : data) {
            log.info(s);
        }
    }
```

其实和我们刚才写的批量消费区别只是在注解上多了个属性，启动项目我们仔细搜索一下控制台输出的日志，如果存在该日志则说明成功。同样的我们往这个Topic里面写入一些数据，运行后我们可以看到控制台只监听到一部分消息，这是因为创建的Topic的partition数量为8，而我们只监听了0、1、2、3、4这几个partition，也就是说5 6 7这三个分区的消息我们并没有读取出来。

```java
    @Test
    public void testBatch() throws InterruptedException {
        for (int i = 0; i < 12; i++) {
            kafkaTemplate.send("topic.quick.batch.partition", "test batch listener,dataNum-" + i);
        }
    }
```

missing 3 msg
```text
com.viu.kafka.listen.BatchListener       : topic.quick.batch.partition  receive : 
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-4
com.viu.kafka.listen.BatchListener       : topic.quick.batch.partition  receive : 
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-2
com.viu.kafka.listen.BatchListener       : topic.quick.batch.partition  receive : 
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-1
com.viu.kafka.listen.BatchListener       : topic.quick.batch.partition  receive : 
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-10
com.viu.kafka.listen.BatchListener       : topic.quick.batch.partition  receive : 
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-5
com.viu.kafka.listen.BatchListener       : topic.quick.batch.partition  receive : 
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-9
com.viu.kafka.listen.BatchListener       : topic.quick.batch.partition  receive : 
com.viu.kafka.listen.BatchListener       : test batch listener,dataNum-7
```

#### 注解方式获取消息头及消息体

@Payload：获取的是消息的消息体，也就是发送内容  
@Header(KafkaHeaders.RECEIVED_MESSAGE_KEY)：获取发送消息的key  
@Header(KafkaHeaders.RECEIVED_PARTITION_ID)：获取当前消息是从哪个分区中监听到的  
@Header(KafkaHeaders.RECEIVED_TOPIC)：获取监听的TopicName  
@Header(KafkaHeaders.RECEIVED_TIMESTAMP)：获取时间戳

```java
    @KafkaListener(id = "anno", topics = "topic.quick.anno")
    public void annoListener(@Payload String data,
                             @Header(KafkaHeaders.RECEIVED_MESSAGE_KEY) Integer key,
                             @Header(KafkaHeaders.RECEIVED_PARTITION_ID) int partition,
                             @Header(KafkaHeaders.RECEIVED_TOPIC) String topic,
                             @Header(KafkaHeaders.RECEIVED_TIMESTAMP) long ts) {
        log.info("topic.quick.anno receive : \n"+
            "data : "+data+"\n"+
            "key : "+key+"\n"+
            "partitionId : "+partition+"\n"+
            "topic : "+topic+"\n"+
            "timestamp : "+ts+"\n"
        );

    }
```

```java
    @Test
    public void testAnno() throws InterruptedException {
        Map map = new HashMap<>();
        map.put(KafkaHeaders.TOPIC, "topic.quick.anno");
        map.put(KafkaHeaders.MESSAGE_KEY, 0);
        map.put(KafkaHeaders.PARTITION_ID, 0);
        map.put(KafkaHeaders.TIMESTAMP, System.currentTimeMillis());

        kafkaTemplate.send(new GenericMessage<>("test anno listener", map));
    }
```

```text
com.viu.kafka.listen.SingleListener      : topic.quick.anno receive : 
data : test anno listener
key : 0
partitionId : 0
topic : topic.quick.anno
timestamp : 1536650867015
```


#### Ack机制


Kafka是通过最新保存偏移量进行消息消费的，而且确认消费的消息并不会立刻删除，所以我们可以重复的消费未被删除的数据，当第一条消息未被确认，而第二条消息被确认的时候，Kafka会保存第二条消息的偏移量，也就是说第一条消息再也不会被监听器所获取，除非是根据第一条消息的偏移量手动获取。


1.  设置ENABLE_AUTO_COMMIT_CONFIG=false，禁止自动提交
2.  设置AckMode=MANUAL_IMMEDIATE
3.  监听方法加入Acknowledgment ack 参数

怎么拒绝消息呢，只要在监听方法中不调用ack.acknowledge()即可

```java
```java
@Component
public class AckListener {

    private static final Logger log= LoggerFactory.getLogger(AckListener.class);

    private Map<String, Object> consumerProps() {
        Map<String, Object> props = new HashMap<>();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, false);
        props.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, "1000");
        props.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, "15000");
        props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, IntegerDeserializer.class);
        props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        return props;
    }

    @Bean("ackContainerFactory")
    public ConcurrentKafkaListenerContainerFactory ackContainerFactory() {
        ConcurrentKafkaListenerContainerFactory factory = new ConcurrentKafkaListenerContainerFactory();
        factory.setConsumerFactory(new DefaultKafkaConsumerFactory(consumerProps()));
        factory.getContainerProperties().setAckMode(AbstractMessageListenerContainer.AckMode.MANUAL_IMMEDIATE);
        factory.setConsumerFactory(new DefaultKafkaConsumerFactory(consumerProps())); 　//?
        return factory;
    }


    @KafkaListener(id = "ack", topics = "topic.quick.ack",containerFactory = "ackContainerFactory")
    public void ackListener(ConsumerRecord record, Acknowledgment ack) {
        log.info("topic.quick.ack receive : " + record.value());
        ack.acknowledge();
    }
}
```

在这段章节开头之初我就讲解了Kafka机制会出现的一些情况，导致没办法重复消费未被Ack的消息，解决办法有如下：

重新将消息发送到队列中，这种方式比较简单而且可以使用Headers实现第几次消费的功能，用以下次判断
```java
    @KafkaListener(id = "ack", topics = "topic.quick.ack", containerFactory = "ackContainerFactory")
    public void ackListener(ConsumerRecord record, Acknowledgment ack, Consumer consumer) {
        log.info("topic.quick.ack receive : " + record.value());

        //如果偏移量为偶数则确认消费，否则拒绝消费
        if (record.offset() % 2 == 0) {
            log.info(record.offset()+"--ack");
            ack.acknowledge();
        } else {
            log.info(record.offset()+"--nack");
            kafkaTemplate.send("topic.quick.ack", record.value());
            //ADD ??
            ack.acknowledge();
        }
    }
```